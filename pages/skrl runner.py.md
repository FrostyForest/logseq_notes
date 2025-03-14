- #skrl #isaaclab
- | 方法 | 输入 | 输出类型 | 主要责任 | 典型配置参数示例 |
  | ---- | ---- | ---- |
  | `_generate_models` | 环境空间 + 模型配置 | 神经网络字典 | 定义网络结构 | hidden_layers, activation |
  | `_generate_agent` | 模型 + 算法配置 | 算法实例 | 实现RL算法逻辑 | learning_rate, batch_size |
  | `_generate_trainer` | 环境 + 代理 + 训练配置 | 训练器实例 | 管理训练流程 | max_episodes, checkpoint_interval |
- runner的核心逻辑run包括
	- *self*._trainer.train()
	- *self*._trainer.eval()
	- 这两个方法是由具体的rl算法来进行实现如
		- _isaac_sim/kit/python/lib/python3.10/site-packages/skrl/agents/jax/ppo/ppo.py