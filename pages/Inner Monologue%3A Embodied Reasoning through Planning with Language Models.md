- #research #paper #robotics
- 基于感知模型的反馈实现闭环控制
	- **感知模型 (Perception Models):**  用于提供环境反馈，例如：
	- **成功检测 (Success Detection):**  判断一个技能是否执行成功。
		- 论文中使用了两种方法来判断技能是否执行成功：**基于规则的检测 (Rule-based)** 和 **基于学习的检测 (Learned)**。具体使用哪种方法取决于实验环境和具体的任务。
		  
		  **1. 基于规则的检测 (Rule-based):**
		  
		  这种方法主要用于**模拟的桌面整理环境 (Simulated Tabletop Rearrangement)** 和**真实的桌面整理环境 (Real-World Tabletop Rearrangement)** 中的部分任务。它依赖于**预先定义的规则**来判断技能是否执行成功。
		  
		  *   **模拟环境:**  由于模拟环境可以访问物体的真实状态信息（例如位置、姿态等），因此可以直接根据这些信息来定义规则。例如，在 “将所有方块堆叠起来” 的任务中，可以定义规则：如果所有方块都堆叠在一起，并且堆叠的高度达到一定阈值，则认为任务成功。
		  *   **真实环境 (部分任务):**  对于一些简单的任务，例如 “捡起 X 物体” 或者 “将 X 物体放置到 Y 位置”，可以通过比较物体的位置信息来判断是否执行成功。例如，在真实的桌面整理环境中，论文使用了基于 **MDETR** 检测到的物体边界框中心位置来判断：
		      *   **捡起任务:**  如果执行动作后，物体 X 不再出现在场景中，则认为捡起成功。
		      *   **放置任务:**  如果执行动作后，物体 X 的边界框中心位置与目标位置的距离小于一定阈值，则认为放置成功。对于不同的任务，阈值也不同。例如，对于堆叠任务，阈值为 3 厘米；对于放置到盘子里的任务，阈值为 10 厘米。
		  
		  **2. 基于学习的检测 (Learned):**
		  
		  这种方法主要用于**真实的厨房环境 (Real-World Mobile Manipulator in a Kitchen Setting)**。它使用了一个预先训练好的**成功检测模型 (Success Detector)** 来判断技能是否执行成功。
		  
		  *   **模型结构:**  论文中使用了两种成功检测模型：**前瞻模型 (Foresight)** 和 **回溯模型 (Hindsight)**。
		      *   **前瞻模型 (Foresight):**  输入是初始图像 `o0`、最终图像 `of` 和技能的语言描述 `lk`，输出是一个概率值，表示在给定这些输入的情况下，该技能是否执行成功。
		      *   **回溯模型 (Hindsight):**  输入也是初始图像 `o0` 和最终图像 `of`，输出是一个概率分布，表示在给定这些输入的情况下，最有可能执行了哪个技能。
		  *   **训练数据:**  成功检测模型是在离线数据上训练的，这些数据包括了遥操作演示数据和自主生成的 episodes。
		  *   **训练方法:**  前瞻模型使用二元交叉熵损失函数进行训练，回溯模型使用对比损失函数进行训练。
		  *   **具体使用:** 在实际应用中，首先使用前瞻模型进行预测，如果预测的概率值高于某个阈值 `τ`，则认为技能执行成功；否则，使用回溯模型进行预测，并选择概率最高的技能作为预测结果，如果该技能与前瞻模型中使用的技能相同，则认为技能执行成功。
		  
		  **总结:**
		  
		  论文中使用了两种方法来判断技能是否执行成功：
		  
		  *   **基于规则的检测:**  简单直接，但需要针对不同的任务手动定义规则，并且依赖于环境信息的可获取性。
		  *   **基于学习的检测:**  更加通用，可以应用于更复杂的任务和环境，但需要预先训练一个成功检测模型。
	- **场景描述 (Scene Description):**  提供场景的语义信息，可以是结构化的（例如物体列表）或非结构化的（例如自然语言描述）。又可以分为：
		- **被动场景描述 (Passive Scene Description):**  在每个规划步骤都自动提供。
		- **主动场景描述 (Active Scene Description):**  LLM 可以主动提问，并获得答案。
	- **人类反馈 (Human Feedback):**  人类用户可以提供各种形式的反馈，例如指令、纠正、回答问题等。
-