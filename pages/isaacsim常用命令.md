- #isaacsim #ros2
- ## 尝试手动控制小车并发布camera数据
	- 创建终端a,输入
	  ```
	  ros2 run teleop_twist_keyboard teleop_twist_keyboard
	  ```
	- 创建终端b,输入
	  ```
	  ./python.sh /home/linhai/app/isaacsim/isaac-sim-standalone@4.5.0-rc.36+release.19112.f59b3005.gl.linux-x86_64.release/my_codes/navigation_ros2/my_camera_manual.py
	  ```
- ## 处理收集到的数据
	- ```
	  ./python.sh /home/linhai/app/isaacsim/isaac-sim-standalone@4.5.0-rc.36+release.19112.f59b3005.gl.linux-x86_64.release/my_codes/ROS2_data_process/process_data.py
	  ```