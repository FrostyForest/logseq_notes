- #hindsight #reinforcement_learning #stable_baseline
- ## AI分析
	- 这段代码定义了一个名为 `HerReplayBuffer` 的类，它是 **Hindsight Experience Replay (HER)** 算法的核心组成部分，用于在基于目标的强化学习任务中提高样本效率。HER 是一种非常有效的技术，尤其是在奖励稀疏的环境中。
	  
	  这个类继承自 `DictReplayBuffer`（可能是 Stable Baselines3 中的一个基类，用于处理观察空间是字典类型的回放缓冲区），并为其添加了 HER 特有的逻辑。
	  
	  以下是对这段代码关键部分的理解：
	  
	  **1. 类的目的和 HER 简介**
	  
	  *   **Hindsight Experience Replay (HER)**:
	      *   核心思想：即使智能体没有达到预设的目标，它在轨迹中达到的某些状态也可以被视为“事后”的目标。通过这种方式，可以将失败的轨迹转化为“成功”的经验（相对于这些事后设定的目标），从而学习到更多有用的信息。
	      *   例如，一个机器人手臂试图将物体移动到 A 点但失败了，最终物体停在了 B 点。HER 会额外生成一些经验，其中目标被设定为 B 点，这样原始轨迹（相对于目标 B）就变成了一个成功的轨迹。
	  *   **`HerReplayBuffer`**: 这个缓冲区不仅存储真实的经验 $(s, a, r, s', d, \text{info})$，还能够在采样时**生成虚拟的 (virtual) 或事后的 (hindsight) 经验**。
	  
	  **2. 初始化 (`__init__`)**
	  
	  *   **继承父类**: 调用 `super().__init__(...)` 初始化 `DictReplayBuffer` 的基本功能（如存储大小、观察/动作空间、设备等）。
	  *   **环境 (`self.env`)**: 存储训练环境的引用。这对于 HER 至关重要，因为需要环境的 `compute_reward` 方法来计算虚拟经验的新奖励。
	  *   **`copy_info_dict`**: 一个布尔值，决定是否在创建虚拟经验时复制 `info` 字典。复制可能导致性能下降，但如果 `compute_reward` 依赖 `info`，则必须复制。
	  *   **`goal_selection_strategy`**:
	      *   决定如何选择事后目标来创建虚拟经验。可以是字符串或 `GoalSelectionStrategy` 枚举类型。
	      *   常见策略包括：
	          *   `'final'`: 使用当前 episode 实际达到的最终状态作为事后目标。
	          *   `'future'`: 从当前转换之后的同一 episode 中的未来某个状态随机选择作为事后目标。代码注释提到这里的实现是“inclusive”，意味着当前转换本身也可以被选为目标。
	          *   `'episode'`: 从当前 episode 中的任意一个状态随机选择作为事后目标。
	      *   `KEY_TO_GOAL_STRATEGY` 字典用于将字符串策略名称映射到相应的策略对象。
	  *   **`n_sampled_goal`**: 每个真实的经验，会额外生成多少个虚拟的 HER 经验。
	  *   **`her_ratio`**: 计算出虚拟经验在采样批次中所占的比例。例如，如果 `n_sampled_goal = 4`，则每个真实经验对应 4 个虚拟经验，总共 5 个“相关”经验。那么虚拟经验的比例是 $1 - (1 / (4+1)) = 0.8$ (即 80%)。
	  *   **`self.infos`**: 一个 NumPy 数组，用于存储每个转换的 `info` 字典。如果 `copy_info_dict` 为 `True`，则会存储。
	  *   **`self.ep_start` 和 `self.ep_length`**:
	      *   这两个 NumPy 数组对于 HER 至关重要。它们用于跟踪缓冲区中每个转换所属 episode 的开始位置和长度。
	      *   `ep_start[pos, env_idx]` 存储了在缓冲区位置 `pos` 的、来自环境 `env_idx` 的转换所属 episode 的起始索引。
	      *   `ep_length[pos, env_idx]` 存储了该 episode 的长度。
	      *   这些信息使得在采样事后目标时，可以确保目标来自同一 episode。
	  *   **`self._current_ep_start`**: 一个 NumPy 数组，跟踪每个并行环境当前正在进行的 episode 在缓冲区中的起始位置。
	  
	  **3. 序列化 (`__getstate__`, `__setstate__`) 和环境设置 (`set_env`)**
	  
	  *   这些方法用于 Python 对象的序列化（pickling）和反序列化。
	  *   `__getstate__` 在序列化时会移除不可序列化的 `self.env` 引用。
	  *   `__setstate__` 在反序列化后恢复状态，并要求用户之后手动调用 `set_env()` 来重新关联环境。
	  *   `set_env()` 用于在初始化后或反序列化后设置环境。
	  
	  **4. 添加经验 (`add`)**
	  
	  *   这是 `HerReplayBuffer` 的核心方法之一，重写了父类的 `add` 方法。
	  *   **处理旧 episode 的覆盖**: 当缓冲区满了并开始覆盖旧数据时，如果覆盖到了一个旧 episode 的一部分，这个方法会确保整个旧 episode 的 `ep_length` 被设置为 0，从而防止采样到不完整的旧 episode。
	  *   **存储 `ep_start`**: 将当前转换的 episode 起始位置（来自 `self._current_ep_start`）存入 `self.ep_start`。
	  *   **存储 `infos`**: 如果 `self.copy_info_dict` 为 `True`，则存储 `info` 字典。
	  *   **调用父类 `add`**: 使用 `super().add(...)` 调用父类的方法来实际存储 `obs`, `next_obs`, `action`, `reward`, `done`。
	  *   **计算并存储 `ep_length`**: 当一个 episode 结束时 (`done[env_idx]` 为 `True`)，调用 `self._compute_episode_length(env_idx)`。
	  
	  **5. 计算 Episode 长度 (`_compute_episode_length`)**
	  
	  *   当一个 episode 结束时，这个方法被调用。
	  *   它根据当前 episode 的起始位置 `self._current_ep_start[env_idx]` 和当前缓冲区指针 `self.pos`（即 episode 的结束位置）计算出 episode 的长度。
	  *   处理了缓冲区循环写入（当 `episode_end < episode_start` 时）的情况。
	  *   将计算得到的长度填充到该 episode 所有转换对应的 `self.ep_length` 条目中。
	  *   最后，更新 `self._current_ep_start[env_idx]` 为当前 `self.pos`，作为下一个 episode 的起始位置。
	  
	  **6. 采样经验 (`sample`)**
	  
	  *   这是 HER 的另一个核心。当算法需要一批数据进行训练时，会调用此方法。
	  *   **选择有效转换**: 首先，它只考虑那些 `ep_length > 0` 的转换进行采样，确保不会采样到不完整或已被标记为无效的 episode。
	  *   **采样索引**: 从有效转换的索引中随机选择 `batch_size` 个索引。
	  *   **区分真实和虚拟样本**:
	      *   根据 `self.her_ratio`，将采样到的索引划分为一部分用于生成**真实样本 (real samples)**，另一部分用于生成**虚拟样本 (virtual samples)**。
	  *   **获取真实样本 (`_get_real_samples`)**:
	      *   对于标记为真实样本的索引，直接从缓冲区中提取原始的 $(s, a, r, s', d)$ 数据。
	      *   进行必要的观察/奖励归一化（如果 `env` 是 `VecNormalize` 实例）。
	      *   将数据转换为 PyTorch 张量。
	      *   注意 `dones` 的处理：`self.dones[...] * (1 - self.timeouts[...])`。如果 `handle_timeout_termination` 为 `True`，并且某个转换是由于超时（timeout）而结束的，那么在计算 TD 目标时，这个 `done` 信号会被视为 `False`（即 $1 - 1 = 0$），允许从该状态进行价值引导 (bootstrapping)。
	  *   **获取虚拟样本 (`_get_virtual_samples`)**:
	      *   对于标记为虚拟样本的索引：
	          1.  提取原始的 $s$ (`obs`) 和 $s'$ (`next_obs`) 以及对应的 $a$ (`self.actions`)。
	          2.  调用 `self._sample_goals()` 根据选定的 `goal_selection_strategy` 为这些转换**采样新的事后目标 `new_goals`**。
	          3.  修改 `obs["desired_goal"]` 和 `next_obs["desired_goal"]` 为这个新的事后目标 `new_goals`。
	          4.  使用环境的 `self.env.env_method("compute_reward", ...)` 方法，根据 `next_obs["achieved_goal"]`（实际达到的状态）和新的 `obs["desired_goal"]`（事后目标）**重新计算奖励 `rewards`**。
	          5.  `dones` 信号通常也会根据新的目标重新判断（例如，如果 `next_obs["achieved_goal"]` 等于 `new_goals`，则 `done` 为 True）。不过在这段代码中，`dones` 似乎直接复用了真实样本的 `dones`（乘以 `1 - timeouts`），这可能是一个简化或者依赖于 `compute_reward` 返回的 `info` 来判断。在标准的 HER 中，`done` 信号也会基于新的目标重新计算。
	          6.  进行归一化和张量转换。
	  *   **合并数据**: 将真实样本和虚拟样本的各个部分（观察、动作、下一个观察、完成标志、奖励）拼接起来，形成最终的训练批次。
	  *   返回 `DictReplayBufferSamples` 对象。
	  
	  **7. 采样事后目标 (`_sample_goals`)**
	  
	  *   根据 `self.goal_selection_strategy` 实现不同的目标采样逻辑：
	      *   **`FINAL`**: 选择当前 episode 的最后一个状态的 `achieved_goal` 作为事后目标。
	      *   **`FUTURE`**: 从当前转换之后、同一 episode 内的未来状态中随机选择一个状态的 `achieved_goal` 作为事后目标。
	      *   **`EPISODE`**: 从当前 episode 内的任意一个状态中随机选择一个状态的 `achieved_goal` 作为事后目标。
	  *   返回采样到的事后目标。
	  
	  **8. 截断最后一个轨迹 (`truncate_last_trajectory`)**
	  
	  *   这是一个辅助方法，用于在某些情况下（例如，从已保存的缓冲区加载后，如果当前不是在同一个 episode 中继续）显式地将会话中最后一个未完成的轨迹标记为已完成。
	  
	  **总结:**
	  
	  `HerReplayBuffer` 通过以下方式实现了 Hindsight Experience Replay：
	  
	  1.  **存储完整的 episode 信息**: 使用 `ep_start` 和 `ep_length` 精确跟踪每个存储的转换所属的 episode。
	  2.  **采样时生成虚拟经验**: 在 `sample()` 方法中，一部分样本被用于生成虚拟经验。
	  3.  **事后目标选择**: 根据设定的策略（`final`, `future`, `episode`）从同一 episode 中选择新的目标。
	  4.  **奖励重计算**: 使用环境的 `compute_reward` 方法，根据新的事后目标重新计算虚拟经验的奖励（和可能的 `done` 状态）。
	  
	  通过这种方式，即使在智能体最初未能达到目标的情况下，HER 也能从“失败”的经验中提取出有价值的学习信号，显著提高了在稀疏奖励和基于目标的任务中的学习效率。这个类是 Stable Baselines3 中实现 HER 的核心组件。
- ## 个人理解
	- ### add方法和其他buffer有何区别
		- 计算并存储了episode的长度
	- ### sample方法和其他buffer的区别
		- ```
		          virtual_data = self._get_virtual_samples(virtual_batch_indices, virtual_env_indices, env)
		  
		          # Concatenate real and virtual data
		          observations = {
		              key: th.cat((real_data.observations[key], virtual_data.observations[key]))
		              for key in virtual_data.observations.keys()
		          }
		  ```
		- 生成了虚拟数据拼接到数据里面
	- ### gymnasium的环境需要做哪些修改才能使用HerReplayBuffer
		- **将观察空间定义为 spaces.Dict**，并包含 'observation', 'achieved_goal', 和 'desired_goal' 三个键。
		- **实现 compute_reward(self, achieved_goal, desired_goal, info) 方法**，使得可以根据任意给定的 achieved_goal 和 desired_goal 计算奖励。
		- 确保环境的 reset() 和 step() 方法返回符合该字典结构的观察。
		- 环境最好能与 Gymnasium 的向量化环境接口 VecEnv 一起工作。
	- ### 当前做法
		- 自定义wrapper来修改环境返回的信息来兼容herbuffer