- #isaacsim #ros2
- 好的，这是对 NVIDIA Isaac Sim 官方教程 **"Publishing Camera’s Data"** 的完整中文翻译和解析。
  
  ---
- ### **发布摄像头的数据**
	- #### **学习目标**
	- 在本教程中，您将学习如何以编程方式为 Isaac Sim 的摄像头设置数据发布器，并以近似的频率发布数据。
	- #### **准备工作**
		- **先决条件**
		  
		  *   已完成 [ROS2 摄像头](https://docs.isaacsim.omniverse.nvidia.com/latest/ros2_tutorials/tutorial_ros2_camera.html) 教程。
		  *   已完成 ROS 和 ROS 2 的安装，以便在启动 NVIDIA Isaac Sim 之前，必要的环境变量已经设置和加载。
		  *   已阅读 [传感器坐标系表示法（激光雷达、摄像头）](https://docs.isaacsim.omniverse.nvidia.com/latest/application_manual/appendix/axes.html)。
		  *   已阅读如何在场景中以编程方式创建[摄像头传感器对象](https://docs.isaacsim.omniverse.nvidia.com/latest/scripting/creating_sensors.html#camera-sensor-core-api)。
		  *   ROS2 桥接器已被启用。
		  
		  > **注意**
		  > 在 Windows 10 或 11 中，根据您机器的配置，RViz2 可能无法正常打开。
		  
		  ---
- ### **在场景中设置一个摄像头**
	- 要开始本教程，请先设置一个包含 `isaacsim.sensors.camera` 摄像头对象的环境。运行以下代码将在场景中加载一个带摄像头的基础仓库环境。
	  
	  ```python
	  import carb
	  from isaacsim import SimulationApp
	  import sys
	  
	  # 背景舞台的路径
	  BACKGROUND_STAGE_PATH = "/background"
	  # 背景 USD 资源的路径
	  BACKGROUND_USD_PATH = "/Isaac/Environments/Simple_Warehouse/warehouse_with_forklifts.usd"
	  
	  # 仿真配置，使用光线追踪渲染，并以图形界面模式启动
	  CONFIG = {"renderer": "RayTracedLighting", "headless": False}
	  
	  # 一个演示手动加载场景和手动发布图像的 ROS2 桥接示例
	  simulation_app = SimulationApp(CONFIG)
	  
	  import omni
	  import numpy as np
	  from isaacsim.core.api import SimulationContext
	  from isaacsim.core.utils import stage, extensions, nucleus
	  import omni.graph.core as og
	  import omni.replicator.core as rep
	  import omni.syntheticdata._syntheticdata as sd
	  
	  from isaacsim.core.utils.prims import set_targets
	  from isaacsim.sensors.camera import Camera
	  import isaacsim.core.utils.numpy.rotations as rot_utils
	  from isaacsim.core.utils.prims import is_prim_path_valid
	  from isaacsim.core.nodes.scripts.utils import set_target_prims
	  
	  # 启用 ROS2 桥接扩展
	  extensions.enable_extension("isaacsim.ros2.bridge")
	  simulation_app.update()
	  
	  # 初始化仿真上下文
	  simulation_context = SimulationContext(stage_units_in_meters=1.0)
	  
	  # 定位 Isaac Sim 资产文件夹以加载环境和机器人舞台
	  assets_root_path = nucleus.get_assets_root_path()
	  if assets_root_path is None:
	    carb.log_error("找不到 Isaac Sim 资产文件夹")
	    simulation_app.close()
	    sys.exit()
	  
	  # 加载环境
	  stage.add_reference_to_stage(assets_root_path + BACKGROUND_USD_PATH, BACKGROUND_STAGE_PATH)
	  
	  ###### 用于设置发布器的摄像头辅助函数 ########
	  
	  # 在此处粘贴教程中的函数
	  # def publish_camera_tf(camera: Camera): ...
	  # def publish_camera_info(camera: Camera, freq): ...
	  # def publish_pointcloud_from_depth(camera: Camera, freq): ...
	  # def publish_depth(camera: Camera, freq): ...
	  # def publish_rgb(camera: Camera, freq): ...
	  
	  ###################################################################
	  
	  # 创建一个摄像头图元。注意，Camera 类接受世界坐标系约定下的位置和方向。
	  camera = Camera(
	    prim_path="/World/floating_camera",
	    position=np.array([-3.11, -1.87, 1.0]),
	    frequency=20,
	    resolution=(256, 256),
	    orientation=rot_utils.euler_angles_to_quats(np.array([0, 0, 0]), degrees=True),
	  )
	  camera.initialize()
	  
	  simulation_app.update()
	  camera.initialize()
	  
	  ############### 调用摄像头发布函数 ###############
	  
	  # 调用发布器。
	  # 在运行前，请确保您已粘贴了上方的辅助函数，并取消了以下代码行的注释。
	  
	  approx_freq = 30
	  # publish_camera_tf(camera)
	  # publish_camera_info(camera, approx_freq)
	  # publish_rgb(camera, approx_freq)
	  # publish_depth(camera, approx_freq)
	  # publish_pointcloud_from_depth(camera, approx_freq)
	  
	  ####################################################################
	  
	  # 初始化物理
	  simulation_context.initialize_physics()
	  simulation_context.play()
	  
	  while simulation_app.is_running():
	    simulation_context.step(render=True)
	  
	  simulation_context.stop()
	  simulation_app.close()
	  ```
	  
	  ---
- ### **将相机内参发布到 CameraInfo 话题**
	- 以下代码片段将把与 `isaacsim.sensors.camera` 摄像头关联的相机内参 (intrinsics) 发布到一个 `sensor_msgs/CameraInfo` 类型的 ROS 2 话题上。
	  
	  ```python
	  def publish_camera_info(camera: Camera, freq):
	    from isaacsim.ros2.bridge import read_camera_info
	  
	    # 以下代码将链接摄像头的渲染产品，并将数据发布到指定的话题名称。
	    render_product = camera._render_product_path
	    step_size = int(60/freq)  # 通过步长控制发布频率
	    topic_name = camera.name+"_camera_info"
	    queue_size = 1
	    node_namespace = ""
	    frame_id = camera.prim_path.split("/")[-1] # 这需要与 TF 树发布的内容匹配
	  
	    writer = rep.writers.get("ROS2PublishCameraInfo")
	    camera_info = read_camera_info(render_product_path=render_product)
	    writer.initialize(
	        frameId=frame_id,
	        nodeNamespace=node_namespace,
	        queueSize=queue_size,
	        topicName=topic_name,
	        width=camera_info["width"],
	        height=camera_info["height"],
	        projectionType=camera_info["projectionType"],
	        k=camera_info["k"].reshape([1, 9]),
	        r=camera_info["r"].reshape([1, 9]),
	        p=camera_info["p"].reshape([1, 12]),
	        physicalDistortionModel=camera_info["physicalDistortionModel"],
	        physicalDistortionCoefficients=camera_info["physicalDistortionCoefficients"],
	    )
	    writer.attach([render_product])
	  
	    gate_path = omni.syntheticdata.SyntheticData._get_node_path(
	        "PostProcessDispatch" + "IsaacSimulationGate", render_product
	    )
	  
	    # 设置 ROS 发布器上游的 Isaac Simulation Gate 节点的 step 输入，以控制其执行频率
	    og.Controller.attribute(gate_path + ".inputs:step").set(step_size)
	    return
	  ```
- ### **从深度图像发布点云**
	- 在以下代码片段中，一个点云被发布到 `sensor_msgs/PointCloud2` 类型的消息中。这个点云是利用相机的内参从深度图像重建而来的。
	  
	  ```python
	  def publish_pointcloud_from_depth(camera: Camera, freq):
	    # 以下代码将链接摄像头的渲染产品，并将数据发布到指定的话题名称。
	    render_product = camera._render_product_path
	    step_size = int(60/freq)
	    topic_name = camera.name+"_pointcloud" # 将话题名称设为摄像头的名字
	    queue_size = 1
	    node_namespace = ""
	    frame_id = camera.prim_path.split("/")[-1] # 这需要与 TF 树发布的内容匹配
	  
	    # 注意，这个点云发布器将使用相机内参将深度图像转换为点云。
	    # 这种点云生成方法不支持带语义标签的对象。
	    rv = omni.syntheticdata.SyntheticData.convert_sensor_type_to_rendervar(
	        sd.SensorType.DistanceToImagePlane.name
	    )
	  
	    writer = rep.writers.get(rv + "ROS2PublishPointCloud")
	    writer.initialize(
	        frameId=frame_id,
	        nodeNamespace=node_namespace,
	        queueSize=queue_size,
	        topicName=topic_name
	    )
	    writer.attach([render_product])
	  
	    # 设置 ROS 发布器上游的 Isaac Simulation Gate 节点的 step 输入，以控制其执行频率
	    gate_path = omni.syntheticdata.SyntheticData._get_node_path(
	        rv + "IsaacSimulationGate", render_product
	    )
	    og.Controller.attribute(gate_path + ".inputs:step").set(step_size)
	  
	    return
	  ```
- ### **发布 RGB 图像**
	- ```python
	  def publish_rgb(camera: Camera, freq):
	    # 以下代码将链接摄像头的渲染产品，并将数据发布到指定的话题名称。
	    render_product = camera._render_product_path
	    step_size = int(60/freq)
	    topic_name = camera.name+"_rgb"
	    queue_size = 1
	    node_namespace = ""
	    frame_id = camera.prim_path.split("/")[-1] # 这需要与 TF 树发布的内容匹配
	  
	    rv = omni.syntheticdata.SyntheticData.convert_sensor_type_to_rendervar(sd.SensorType.Rgb.name)
	    writer = rep.writers.get(rv + "ROS2PublishImage")
	    writer.initialize(
	        frameId=frame_id,
	        nodeNamespace=node_namespace,
	        queueSize=queue_size,
	        topicName=topic_name
	    )
	    writer.attach([render_product])
	  
	    # 设置 ROS 发布器上游的 Isaac Simulation Gate 节点的 step 输入，以控制其执行频率
	    gate_path = omni.syntheticdata.SyntheticData._get_node_path(
	        rv + "IsaacSimulationGate", render_product
	    )
	    og.Controller.attribute(gate_path + ".inputs:step").set(step_size)
	  
	    return
	  ```
- ### **发布深度图像**
	- ```python
	  def publish_depth(camera: Camera, freq):
	    # 以下代码将链接摄像头的渲染产品，并将数据发布到指定的话题名称。
	    render_product = camera._render_product_path
	    step_size = int(60/freq)
	    topic_name = camera.name+"_depth"
	    queue_size = 1
	    node_namespace = ""
	    frame_id = camera.prim_path.split("/")[-1] # 这需要与 TF 树发布的内容匹配
	  
	    rv = omni.syntheticdata.SyntheticData.convert_sensor_type_to_rendervar(
	                            sd.SensorType.DistanceToImagePlane.name
	                        )
	    writer = rep.writers.get(rv + "ROS2PublishImage")
	    writer.initialize(
	        frameId=frame_id,
	        nodeNamespace=node_namespace,
	        queueSize=queue_size,
	        topicName=topic_name
	    )
	    writer.attach([render_product])
	  
	    # 设置 ROS 发布器上游的 Isaac Simulation Gate 节点的 step 输入，以控制其执行频率
	    gate_path = omni.syntheticdata.SyntheticData._get_node_path(
	        rv + "IsaacSimulationGate", render_product
	    )
	    og.Controller.attribute(gate_path + ".inputs:step").set(step_size)
	  
	    return
	  ```
	  
	  ---
- ### **为相机位姿发布一个 TF 坐标树**
	- 使用上述函数发布的点云，将会在 ROS 的相机坐标系约定（-Y 轴向上, +Z 轴向前）下发布。为了方便在 ROS 的 RViz 中可视化这个点云，以下代码片段将向 `/tf` 话题发布一个包含两个坐标系的 TF 坐标树。
	  
	  这两个坐标系是：
	  
	  *   `{camera_frame_id}`: 这是 ROS 相机约定下的相机位姿（-Y 轴向上, +Z 轴向前）。点云在此坐标系中发布。
	    ![ROS Camera Frame](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/camera_frames_v2.005.png)
	  *   `{camera_frame_id}_world`: 这是世界坐标系约定下的相机位姿（+Z 轴向上, +X 轴向前）。这将反映相机的真实位姿。
	    ![World Frame](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/camera_frames_v2.004.png)
	  
	  TF 坐标树看起来像这样：
	  ![TF Tree](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/transformation.png)
	  
	  *   `world -> {camera_frame_id}` 是一个动态变换，表示从原点到 ROS 相机约定下的相机的变换，它会跟随相机的任何移动。
	  *   `{camera_frame_id} -> {camera_frame_id}_world` 是一个静态变换，仅包含旋转，没有平移。这个静态变换可以用四元数 `[0.5, -0.5, 0.5, 0.5]`（以 [w, x, y, z] 约定）来表示。
	  
	  因为点云是在 `{camera_frame_id}` 中发布的，所以建议将点云话题的 `frame_id` 设置为 `{camera_frame_id}`。最终在 RViz 中，点云的可视化结果可以在 `world` 坐标系下查看。
	  
	  ```python
	  def publish_camera_tf(camera: Camera):
	    camera_prim = camera.prim_path
	  
	    if not is_prim_path_valid(camera_prim):
	        raise ValueError(f"相机路径 '{camera_prim}' 无效。")
	  
	    try:
	        # 生成 camera_frame_id。OmniActionGraph 将使用完整相机图元路径的最后一部分作为坐标系名称，
	        # 所以我们在这里提取它，并用于点云的 frame_id。
	        camera_frame_id = camera_prim.split("/")[-1]
	  
	        # 生成一个与相机 TF 发布相关的动作图。
	        ros_camera_graph_path = "/CameraTFActionGraph"
	  
	        # 如果找不到相机图，就创建一个新的。
	        if not is_prim_path_valid(ros_camera_graph_path):
	            (ros_camera_graph, _, _, _) = og.Controller.edit(
	                {
	                    "graph_path": ros_camera_graph_path,
	                    "evaluator_name": "execution",
	                    "pipeline_stage": og.GraphPipelineStage.GRAPH_PIPELINE_STAGE_SIMULATION,
	                },
	                {
	                    og.Controller.Keys.CREATE_NODES: [
	                        ("OnTick", "omni.graph.action.OnTick"),
	                        ("IsaacClock", "isaacsim.core.nodes.IsaacReadSimulationTime"),
	                        ("RosPublisher", "isaacsim.ros2.bridge.ROS2PublishClock"),
	                    ],
	                    og.Controller.Keys.CONNECT: [
	                        ("OnTick.outputs:tick", "RosPublisher.inputs:execIn"),
	                        ("IsaacClock.outputs:simulationTime", "RosPublisher.inputs:timeStamp"),
	                    ]
	                }
	            )
	  
	        # 为每个相机生成2个节点：从世界坐标到ROS相机约定的TF，以及世界坐标系。
	        og.Controller.edit(
	            ros_camera_graph_path,
	            {
	                og.Controller.Keys.CREATE_NODES: [
	                    ("PublishTF_"+camera_frame_id, "isaacsim.ros2.bridge.ROS2PublishTransformTree"),
	                    ("PublishRawTF_"+camera_frame_id+"_world", "isaacsim.ros2.bridge.ROS2PublishRawTransformTree"),
	                ],
	                og.Controller.Keys.SET_VALUES: [
	                    ("PublishTF_"+camera_frame_id+".inputs:topicName", "/tf"),
	                    # 注意：如果 topic_name 改为除 "/tf" 之外的其他名称，
	                    # ROS 的 tf broadcaster 将无法捕获到它。
	                    ("PublishRawTF_"+camera_frame_id+"_world.inputs:topicName", "/tf"),
	                    ("PublishRawTF_"+camera_frame_id+"_world.inputs:parentFrameId", camera_frame_id),
	                    ("PublishRawTF_"+camera_frame_id+"_world.inputs:childFrameId", camera_frame_id+"_world"),
	                    # 从 ROS 相机约定到世界坐标系 (+Z向上, +X向前) 的静态变换：
	                    ("PublishRawTF_"+camera_frame_id+"_world.inputs:rotation", [0.5, -0.5, 0.5, 0.5]),
	                ],
	                og.Controller.Keys.CONNECT: [
	                    (ros_camera_graph_path+"/OnTick.outputs:tick",
	                        "PublishTF_"+camera_frame_id+".inputs:execIn"),
	                    (ros_camera_graph_path+"/OnTick.outputs:tick",
	                        "PublishRawTF_"+camera_frame_id+"_world.inputs:execIn"),
	                    (ros_camera_graph_path+"/IsaacClock.outputs:simulationTime",
	                        "PublishTF_"+camera_frame_id+".inputs:timeStamp"),
	                    (ros_camera_graph_path+"/IsaacClock.outputs:simulationTime",
	                        "PublishRawTF_"+camera_frame_id+"_world.inputs:timeStamp"),
	                ],
	            },
	        )
	    except Exception as e:
	        print(e)
	  
	    # 为 USD 位姿添加目标图元。所有其他坐标系都是静态的。
	    set_target_prims(
	        primPath=ros_camera_graph_path+"/PublishTF_"+camera_frame_id,
	        inputName="inputs:targetPrims",
	        targetPrimPaths=[camera_prim],
	    )
	    return
	  ```
	  
	  ---
- ### **运行示例**
	- 启用 `isaacsim.ros2.bridge` 扩展并按照[此工作流教程](https://docs.isaacsim.omniverse.nvidia.com/latest/ros2_tutorials/tutorial_ros2_workspace_setup.html)设置 ROS2 环境变量。保存上述脚本，并使用 Isaac Sim 文件夹中的 `python.sh` 来运行它。在我们的示例中，`{camera_frame_id}` 是相机的图元名称，即 `floating_camera`。
	  
	  您将在场景中看到一个路径为 `/World/floating_camera` 的悬浮摄像头，并验证它能看到一辆叉车：
	  ![Sim View](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/isaac_tutorial_ros_camera_publishing_simview.png)
	  
	  如果您打开一个终端并输入 `ros2 topic list`，请验证您能看到以下内容：
	  ```bash
	  ros2 topic list
	  /camera_camera_info
	  /camera_depth
	  /camera_pointcloud
	  /camera_rgb
	  /clock
	  /parameter_events
	  /rosout
	  /tf
	  ```
	  
	  TF 发布的坐标系将如下所示：
	  ![Frames](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/frames.png)
	  
	  现在，您可以通过 RViz2 来可视化点云和深度图像。打开 RViz2，并将 `Fixed Frame` 字段设置为 `world`。
	  ![RViz Setup](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/rviz.png)
	  
	  然后，启用对 `/camera_depth`, `/camera_rgb`, `/camera_pointcloud` 和 `/tf` 话题的查看。
	  
	  深度图像 `/camera_depth` 和 RGB 图像 `/camera_rgb` 应分别如下所示：
	  ![RGBD Images](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/isaac_tutorial_ros_camera_publishing_rgbd.png)
	  
	  点云将如下所示。验证 TF 发布器发布的相机坐标系显示了两个坐标系。左图显示了 `{camera_frame_id}_world` 坐标系，右图显示了 `{camera_frame_id}` 坐标系。
	  ![Point Cloud Front View](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/isaac_tutorial_ros_camera_publishing_pc_frontview.png)
	  
	  从侧视图看：
	  ![Point Cloud Side View](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/isaac_tutorial_ros_camera_publishing_pc_sideview.png)
	  
	  ---
- ### **总结**
	- 本教程演示了如何以编程方式为 Isaac Sim 摄像头设置 ROS2 发布器，并以近似的频率发布数据。