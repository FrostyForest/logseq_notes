- #ros2 #isaacsim
- ## ai翻译
	- ### **独立工作流中的 ROS 2 桥接器**
		- #### **学习目标**
			- 运行独立的 ROS2 Python 示例。
			- 手动步进 (step) ROS2 组件。
		- #### **准备工作**
			- **重要**
			  在运行 Isaac Sim 之前，请确保已在终端中加载了您的 ROS 2 安装环境。如果加载 ROS 2 的命令是您 ~/.bashrc 文件的一部分，那么可以直接运行 Isaac Sim。
		- #### **先决条件**
			- 1. 已完成 [工作流与 "Hello World"](https://www.google.com/url?sa=E&q=https%3A%2F%2Fdocs.isaacsim.omniverse.nvidia.com%2Flatest%2Fmanual_tutorials%2Ftutorial_manual_workflows.html) 教程，以理解两种工作流（独立式和扩展式）。
			- 2. 通过以下步骤设置启用 ROS2 消息传递所需的环境变量。
				- 1. 在 ~/.ros/ 目录下创建一个名为 fastdds.xml 的文件（如果尚未存在），将以下代码片段粘贴到该文件中。
				  ```
				  <?xml version="1.0" encoding="UTF-8" ?>
				  <license>Copyright (c) 2022, NVIDIA CORPORATION.  All rights reserved.
				  NVIDIA CORPORATION and its licensors retain all intellectual property
				  and proprietary rights in and to this software, related documentation
				  and any modifications thereto.  Any use, reproduction, disclosure or
				  distribution of this software and related documentation without an express
				  license agreement from NVIDIA CORPORATION is strictly prohibited.</license>
				  
				  <profiles xmlns="http://www.eprosima.com/XMLSchemas/fastRTPS_Profiles" >
				      <transport_descriptors>
				          <transport_descriptor>
				              <transport_id>UdpTransport</transport_id>
				              <type>UDPv4</type>
				          </transport_descriptor>
				      </transport_descriptors>
				  
				      <participant profile_name="udp_transport_profile" is_default_profile="true">
				          <rtps>
				              <userTransports>
				                  <transport_id>UdpTransport</transport_id>
				              </userTransports>
				              <useBuiltinTransports>false</useBuiltinTransports>
				          </rtps>
				      </participant>
				  </profiles>
				  ```
				- 2. 在**每个**将要通过脚本启动 Isaac Sim 的终端中，运行 unset LD_LIBRARY_PATH 和 export FASTRTPS_DEFAULT_PROFILES_FILE=~/.ros/fastdds.xml。
				  *(这是一个关键步骤，用于确保 Isaac Sim 使用指定的 DDS 配置，而不是系统默认的配置，从而能正确地与其他 ROS 2 节点通信)*
	- ###
	- ### **手动步进 ROS2 组件**
		- 独立脚本的一个主要用途就是**手动控制仿真步进**。可以将一个 OnImpulseEvent (脉冲事件) OmniGraph 节点连接到任何 ROS2 OmniGraph 节点，这样发布者和订阅者的频率就可以被精确地控制。
		- 以下是一个示例，展示了如何设置一个新的、包含 ROS2 Publish Clock 节点的动作图，并使其能被精确控制，同时使用 1 作为 ROS2 域ID：
		  ```
		  import omni.graph.core as og
		  # 创建一个路径为 /ActionGraph 的新图
		  og.Controller.edit(
		      {"graph_path": "/ActionGraph", "evaluator_name": "execution"},
		      {
		          og.Controller.Keys.CREATE_NODES: [
		              ("ReadSimTime", "isaacsim.core.nodes.IsaacReadSimulationTime"),
		              ("Context", "isaacsim.ros2.bridge.ROS2Context"),
		              ("PublishClock", "isaacsim.ros2.bridge.ROS2PublishClock"),
		              ("OnImpulseEvent", "omni.graph.action.OnImpulseEvent"),
		          ],
		          og.Controller.Keys.CONNECT: [
		              # 将 OnImpulseEvent 节点的执行输出连接到 PublishClock，这样它只在脉冲事件被触发时才发布
		              ("OnImpulseEvent.outputs:execOut", "PublishClock.inputs:execIn"),
		              # 将 ReadSimTime 的仿真时间数据连接到时钟发布器节点
		              ("ReadSimTime.outputs:simulationTime", "PublishClock.inputs:timeStamp"),
		              # 将 ROS2 上下文连接到时钟发布器节点，这样它将在指定的 ROS2 域ID下运行
		              ("Context.outputs:context", "PublishClock.inputs:context"),
		          ],
		          og.Controller.Keys.SET_VALUES: [
		              # 为时钟发布器分配话题名称
		              ("PublishClock.inputs:topicName", "/clock"),
		              # 为 Context 节点分配一个为 1 的域ID
		              ("Context.inputs:domain_id", 1),
		              # 禁用 useDomainIDEnvVar 以确保我们使用上面设置的域ID
		              ("Context.inputs:useDomainIDEnvVar", False),
		          ],
		      },
		  )
		  ```
		- 在任何一帧，运行以下代码来设置一个脉冲事件，这将触发时钟发布器执行一次：
		  ```
		  og.Controller.set(og.Controller.attribute("/ActionGraph/OnImpulseEvent.state:enableImpulse"), True)
		  ```
	- ### 示例
		- 我们将一些教程示例转换为了独立的 Python 示例。以下是运行它们的说明。
		- ####
		- #### **ROS2 时钟 (ROS2 Clock)**
			- 此示例演示了如何创建一个带有 ROS2 组件节点的动作图，然后以不同的速率驱动它们。
			  可以通过运行以下命令来执行该示例：
			- ```
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/clock.py
			  ```
			- 监听以下话题以查看正在发布的消息：
			- ```
			  ros2 topic echo /sim_time
			  ros2 topic echo /manual_time
			  ```
			- 要使用 Isaac Sim UI 创建和设置 ROS2 时钟发布器，请参阅 ROS2 时钟教程。
		- ####
		- #### **ROS2 摄像头 (ROS2 Camera)**
			- 以下两个示例演示了如何创建一个带有 ROS2 Camera Helper 和 Camera Info Helper OmniGraph 节点的动作图，这些节点用于设置 ROS2 RGB 图像、深度图像和相机信息发布器。两个示例都实现了以不同速率发布 ROS2 图像数据的相同结果，但使用了不同的解决方案。
			- **每一帧**：
				- 发布相机信息 (Camera Info)
			- **每 5 帧**：
				- 发布 RGB 图像
			- **每 60 帧**：
				- 发布深度图像
			- **周期性图像发布 (Periodic Image Publishing)**
			  每个 ROS2 图像和相机信息发布器的执行速率（每 N 帧）是通过修改 SDGPipeline 图中各自对应的 Isaac Simulation Gate OmniGraph 节点来设置的。通过设置执行速率，图像发布器将自动每 N 个渲染帧被驱动一次。
			  可以通过运行以下命令来执行该示例：
			  
			  ```
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/camera_periodic.py
			  ```您可以通过在终端中按 `CTRL-C` 来退出示例。
			  
			  **手动图像发布 (Manual Image Publishing)**
			  ROS2 图像和相机信息发布器是通过在每个发布器节点和其对应的 `Isaac Simulation Gate` OmniGraph 节点之间**注入** `Branch` (分支) OmniGraph 节点来手动控制的。`Branch` 节点的作用类似于一个自定义的门，可以随时启用/禁用。每当一个 `Branch` 节点被启用时，连接的 ROS2 发布器节点就会被驱动一次。
			  可以通过运行以下命令来执行该示例：
			  ```bash
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/camera_manual.py
			  ```
			- **可视化结果**
			  要在 RViz2 中可视化任一样例的结果，请在一个新的、已加载 ROS2 环境的终端中，导航到 Isaac Sim 的包目录并运行以下命令：
			- ```
			  rviz2 -d <ros2_ws>/src/isaac_tutorials/rviz2/camera_manual.rviz
			  ```
		- ####
		- #### **卡特机器人立体相机 (Carter Stereo)**
			- 此示例演示了如何使用一个已有的、包含带有 ROS2 组件节点的动作图的 USD 舞台，并修改其默认设置。立体相机对被自动启用，并且第二个视窗在 UI 中被停靠。
			- **每一帧**：
				- 发布 ROS2 时钟
				- 发布来自 RTX 激光雷达的 PointCloud2 消息
				- 发布里程计
				- 驱动 Twist 订阅者
				- 发布 TF 消息
				- 发布左右相机的图像
			- **每两帧**：
				- 发布 Twist 指令消息
			- 可以通过运行以下命令来执行该示例：
			  
			  ```
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/carter_stereo.py
			  ```
			- **可视化结果**：
			  在一个新终端中，运行以下命令加载 RViz2：
			- ```
			  rviz2 -d <ros2_ws>/src/isaac_tutorials/rviz2/carter_stereo.rviz
			  ```
				- 确保 Displays 面板中的 Right Camera - RGB 和 Left Camera - RGB 已启用，以可视化 RGB 图像。
				- **注意**
				  如果某些图像在 RViz2 中没有显示，请在模拟器中按 Stop 和 Play 键，图像就会出现
		- ####
		- #### **多机器人 ROS2 导航 (Multiple Robot ROS2 Navigation)**
			- 此示例展示了如何运行一个已有的 USD 舞台。
			  要可视化输出，请参阅此示例的交互式版本：
			- **每一帧**：
				- 发布 ROS2 时钟组件
				- 发布来自 RTX 激光雷达的 PointCloud2 消息
				- 发布里程计
				- 驱动 Twist 订阅者
				- 发布 TF 消息
				  
				  该示例可以在医院和办公室两种环境中运行。运行以下任一命令以在指定环境中运行示例：
			- **医院环境**
			- ```
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/carter_multiple_robot_navigation.py --environment hospital
			  ```
			- **办公室环境**
			- ```
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/carter_multiple_robot_navigation.py --environment office
			  ```
		- ####
		- #### **MoveIt2**
			- 此示例展示了如何添加多个 USD 舞台。它还演示了如何手动创建一个带有 ROS2 组件节点的动作图，然后手动驱动它们。
			  要可视化输出，请参阅此示例的交互式版本：
			- **每一帧**：
				- 发布 ROS 2 时钟
				- 发布关节状态消息
				- 驱动关节状态订阅者
				- 发布 TF 消息
				  
				  可以通过运行以下命令来执行该示例：
				  
				  ```
				  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/moveit.py
				  ```
		- ####
		- #### **接收 ROS2 消息 (Receiving ROS2 Messages)**
			- 这是一个简单的订阅者示例，在接收到一条空的 ROS2 消息后，场景中的一个立方体会传送到一个随机位置。这个示例启用了渲染，所以您应该能看到场景和移动的立方体。要运行此示例：
			- ```
			  ./python.sh standalone_examples/api/isaacsim.ros2.bridge/subscriber.py
			  ```
			- 一旦带有立方体的场景加载完毕，您可以从另一个终端手动发布空消息。我们将以 1Hz 的频率进行发布：
			- ```
			  ros2 topic pub -r 1 /move_cube std_msgs/msg/Empty
			  ```
			-