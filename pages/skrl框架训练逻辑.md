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
	- 其中在single_agent_train函数中实现训练的逻辑，需要用到step的返回，其中self._observations对应states，而agent需要states作为输入来输出action，具体由agent的act函数进行封装
	- act是怎么调用自定义的model？
		- policy model返回的是`return self.mean_layer(self._shared_output), self.log_std_parameter`
		- ppo类中的act方法里面居然还有封装：
			- ```
			  actions, log_prob, outputs = self.policy.act({"states": self._state_preprocessor(states)}, role="policy")
			  ```
		- self.policy = self.models.get("policy", None)//self.policy是预定义的赋予给ppo的神经网络模型
		- 模型的act方法具体实现如下：
			- ```
			      def act(self, inputs, role):
			          if role == "policy":
			              return GaussianMixin.act(self, inputs, role)
			          elif role == "value":
			              return DeterministicMixin.act(self, inputs, role)
			  ```
	- model中的compute中调用unflatten_tensorized_space(space, tensor)
	- 很好奇gym.space哪里来的key,observation里面没有key
	- 目前没有看到哪里调用了模型的compute方法？
		- compute方法在具体模型的act中得到调用，很复杂的继承关系
			- Shared继承了GaussianMixin，又定义了act和compute
			- GaussianMixin又定义了act，为什么在GaussianMixin里面能调用compute？这个类根本没有定义compute
				- 分析：[[mixin类]] [[父类与子类]]
	- 意义不明的state_preprocessor
-
-
-
- ~~runner.py~~
	- [[skrl runner.py]]