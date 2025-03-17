- #skrl #isaaclab #reinforcement_learning
- 首先是train.py
	- 创建环境
		- [[load_isaaclab_env()]]
	- 封装环境
		- [[wrap_env()]]
			- _isaac_sim/kit/python/lib/python3.10/site-packages/skrl/envs/wrappers/torch/isaaclab_envs.py
	- 创建模型
	- 设置训练参数
	- 创建agent//具体的负责训练的框架算法
		- agent包括
			- 模型、训练参数、环境参数
	- 创建trainer
		- trainer需要以下参数
			- 训练参数
			- 环境
			- agent
	- 开始训练
		- 具体的训练函数实现
			- _isaac_sim/kit/python/lib/python3.10/site-packages/skrl/trainers/torch/base.py:single_agent_train
			- This method executes the following steps in loop:
				- Pre-interaction
				- Compute actions
				- Interact with the environments
				- Render scene
				- Record transitions
				- Post-interaction
				- Reset environments
- **总结**
	- 通过wrapper对环境的step进行封装
		- ```
		          actions = unflatten_tensorized_space(self.action_space, actions)
		          observations, reward, terminated, truncated, self._info = self._env.step(actions)
		          self._observations = flatten_tensorized_space(tensorize_space(self.observation_space, observations["policy"]))
		          return self._observations, reward.view(-1, 1), terminated.view(-1, 1), truncated.view(-1, 1), self._info
		  ```
		- self._observations = flatten_tensorized_space(tensorize_space(self.observation_space, observations["policy"]))中的self.observation_space=self._unwrapped.single_observation_space["policy"]是什么
	- 其中在single_agent_train函数中实现训练的逻辑，需要用到step的返回，其中self._observations对应states，而agent需要states作为输入来输出action，具体由act函数进行封装
	- act是怎么调用自定义的model？
	- model中的compute中调用unflatten_tensorized_space(space, tensor)
	- 很好奇gym.space哪里来的key,observation里面没有key
-
-
-
- ~~runner.py~~
	- [[skrl runner.py]]