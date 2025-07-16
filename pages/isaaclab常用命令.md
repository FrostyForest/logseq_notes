- #isaaclab
- ## 训练
	- ```
	  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task=Isaac-Ant-v0
	  ./isaaclab.sh -p /home/linhai/code/IsaacLab/scripts/reinforcement_learning/rl_games/train.py --task=Isaac-my_Lift-Cube-Franka-v0
	  ./isaaclab.sh -p /home/linhai/code/IsaacLab/scripts/reinforcement_learning/rsl_rl/train.py --task=Isaac-my_Lift-Cube-Franka-v0 --num_envs 64 --enable_cameras
	  ./isaaclab.sh -p /home/linhai/code/IsaacLab/my_code/train/torch_cube_franka_ppo2.py --enable_cameras
	  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/train.py --task=Isaac-my_Lift-Cube-Franka-v1 --num_envs 64
	  ```
- ## 运行预训练好的模型
	- ```
	  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-Franka-Cabinet-Direct-v0 --num_envs 1 --use_pretrained_checkpoint
	  ./isaaclab.sh -p scripts/reinforcement_learning/skrl/play.py --task Isaac-my_Lift-Cube-Franka-v0 --num_envs 1 --checkpoint /home/linhai/code/IsaacLab/logs/skrl/franka_lift/2025-06-04_13-27-58_ppo_torch/checkpoints/best_agent.pt
	  ```
- 测试环境
	- ```
	  ./isaaclab.sh -p scripts/environments/random_agent.py --task Isaac-my_Lift-Cube-Franka-v1 --num_envs 2 --enable_cameras
	  ```
	- 环境名称在环境对应文件夹的__init__.py中的id对应值中
	- ```
	  ./isaaclab.sh -p scripts/environments/zero_agent.py --task Isaac-Franka-Cube-Direct-v0 --num_envs 1 --enable_cameras
	  ```
- 分析训练数据
	- ```
	  tensorboard --logdir ./
	  ```
- ## 遥控
	- ```
	  ./isaaclab.sh -p scripts/environments/teleoperation/teleop_se3_agent.py --task Isaac-my_Lift-Cube-Franka-IK-Rel-v1 --num_envs 1 --teleop_device keyboard --enable_cameras 
	  ./isaaclab.sh -p /home/linhai/code/IsaacLab/my_code/test/isaaclab/teleop_se3_agent.py --task Isaac-my_Lift-Cube-Franka-v1 --num_envs 1 --teleop_device keyboard --enable_cameras 
	  ```
- ## 任务名
	- Isaac-my_Lift-Cube-Franka-IK-Rel-v1
- ## 测试蒸馏学习
	- ```
	  ./isaaclab.sh -p /home/linhai/code/IsaacLab/scripts/imitation_learning/distilling/distilling_learning.py --task Isaac-my_Lift-Cube-Franka-v1 --num_envs 2 --enable_cameras --headless
	  ```