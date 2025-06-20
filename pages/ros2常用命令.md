- #ros2 #robotics
- rviz2
	- ```
	  ros2 run rviz2 rviz2
	  ```
- ## 发布twist命令
	- ```
	  ros2 run teleop_twist_keyboard teleop_twist_keyboard
	  ```
	- ```
	  ros2 topic echo /cmd_vel
	  ```
- ## 使用 topic_tools 包进行节流（Throttling）
	- ```
	  ros2 run topic_tools throttle messages /tf 10 /tf_limit
	  ```
	- ```
	  ros2 run topic_tools throttle messages /depth 5 /depth_limit
	  ```
- ## 记录数据
	- ```
	  ros2 bag record -o /home/linhai/isaacsim_ros_data/tf_limit /tf_limit
	  ```
	- ```
	  ros2 bag record /synced/rgb /synced/depth /synced/tf
	  ```
- ## 运行自己的包
	- ```
	  ros2 run my_isaacsim_subscriber sync_node
	  ```
- ## 查看某topic发布频率
	- ```
	  ros2 topic hz /tf
	  ```
- ## 构建特定的包
	- ```
	  colcon build --symlink-install --packages-select my_interfaces
	  ```