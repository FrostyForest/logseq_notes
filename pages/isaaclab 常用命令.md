- #isaaclab
- 训练
	- ./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task=Isaac-Ant-v0
- 运行预训练好的模型
	- ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Franka-Cabinet-Direct-v0 --num_envs 1 --use_pretrained_checkpoint
- 测试环境
	- ```
	  ./isaaclab.sh -p scripts/environments/random_agent.py --task Isaac-Franka-Cube-Direct-v0 --num_envs 1
	  ```
	- 环境名称在环境对应文件夹的__init__.py中的id对应值中
	- ```
	  ./isaaclab.sh -p scripts/environments/zero_agent.py --task Isaac-Franka-Cube-Direct-v0 --num_envs 1 --enable_cameras
	  ```