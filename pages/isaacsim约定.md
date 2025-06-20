- #isaacsim #相机位姿
- 好的，这是对 **"Isaac Sim Conventions"** (Isaac Sim 约定) 文档的完整中文翻译和解析。
  
  ---
- ### **Isaac Sim 约定**
	- 本节为 NVIDIA Isaac Sim 内部使用的单位、表示法和坐标约定提供了参考。
	- #### **默认单位**
		- | 测量项 | 单位 | 备注 |
		  | :--- | :--- | :--- |
		  | 长度 (Length) | 米 (Meter) | |
		  | 质量 (Mass) | 千克 (Kilogram) | |
		  | 时间 (Time) | 秒 (Seconds) | |
		  | 物理时间步长 | 秒 (Seconds) | 用户可配置。默认为 1/60 秒。 |
		  | 力 (Force) | 牛顿 (Newton) | |
		  | 频率 (Frequency) | 赫兹 (Hertz) | |
		  | 线性驱动刚度 | | |
		  | 角向驱动刚度 | | |
		  | 线性驱动阻尼 | | |
		  | 角向驱动阻尼 | | |
		  | 惯性对角线 | | |
	- #### **默认旋转表示法**
		- **四元数 (Quaternions)**
		  
		  | API | 表示格式 |
		  | :--- | :--- |
		  | Isaac Sim Core (核心库) | (QW, QX, QY, QZ) - **实部在前** |
		  | USD (通用场景描述) | (QW, QX, QY, QZ) - **实部在前** |
		  | PhysX (物理引擎) | (QX, QY, QZ, QW) - **虚部在前** |
		  | Dynamic Control (动态控制) | (QX, QY, QZ, QW) - **虚部在前** |
		  
		  **角度 (Angles)**
		  
		  | API | 表示格式 |
		  | :--- | :--- |
		  | Isaac Sim Core (核心库) | 弧度 (Radians) |
		  | USD (通用场景描述) | 度 (Degrees) |
		  | PhysX (物理引擎) | 弧度 (Radians) |
		  | Dynamic Control (动态控制) | 弧度 (Radians) |
		  
		  > **注意**
		  > 即使用户界面 (UI) 元素显示的属性来自物理引擎，它也应该始终以**度 (Degrees)** 来显示角度。
	- #### **矩阵顺序 (Matrix Order)**
		- | API | 表示格式 |
		  | :--- | :--- |
		  | Isaac Sim Core (核心库) | 行主序 (Row Major) |
		  | USD (通用场景描述) | 行主序 (Row Major) |
		  
		  ---
	- #### **世界坐标轴 (World Axes)**
		- NVIDIA Isaac Sim 遵循**右手坐标系**约定。
		  ![世界坐标系](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/isaac_conventions_world_frame.png)
		  
		  | 方向 | 轴向 | 备注 |
		  | :--- | :--- | :--- |
		  | 上 (Up) | +Z | |
		  | 前 (Forward) | +X | |
	- #### **默认摄像头坐标轴 (Default Camera Axes)**
		- ![默认摄像头坐标系](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/isaac_conventions_camera_frame.png)
		  
		  | 方向 | 轴向 | 备注 |
		  | :--- | :--- | :--- |
		  | 上 (Up) | +Y | |
		  | 前 (Forward) | -Z | |
		  
		  > **注意**
		  > **Isaac Sim 到 ROS 的转换**：要从 Isaac Sim 摄像头坐标系转换到 ROS 摄像头坐标系，需要绕 X 轴旋转 180 度。
	- #### **图像帧（合成数据）**
		- | 坐标 | 角落位置 |
		  | :--- | :--- |
		  | (0,0) | 左上角 (Top Left) |
		  
		  ---
- ### **传感器坐标系表示法（激光雷达、摄像头）**
	- 在 Isaac Sim 中，摄像头会涉及到**三种不同类型的坐标轴定义**，具体取决于使用上下文。在这里，我们介绍这三种约定以及它们在不同上下文中的使用方式。
	- #### **世界坐标轴 (World Axes)**
		- 世界坐标轴使用 **+X 轴向前，+Z 轴向上** 的约定。世界 (World) 图元 (prim) 的原点总是用世界坐标轴来表示。下图显示了在世界坐标轴下表示的摄像头图元。
		  ![世界坐标系下的摄像头](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/camera_frames_v2.002.png){:height 367, :width 718}
	- #### **USD 坐标轴**
		- 在计算机图形学界，使用 USD 约定。USD 坐标轴使用 **+Y 轴向上，-Z 轴向前** 的约定。在一个 Isaac Sim 应用程序中，“属性 (Property)”面板会显示舞台上物体的位姿。舞台上所有物体的位姿都以世界坐标轴显示，**只有一个例外**：摄像头图元，它以 +Y 轴向上，-Z 轴向前的约定来显示。因此，在摄像头图元的上下文中，这个约定被称为 USD 坐标轴。下图显示了在 USD 坐标轴约定下表示的摄像头图元。
		  ![USD 坐标系下的摄像头](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/camera_frames_v2.001.png)
	- #### **ROS 坐标轴**
		- ROS 坐标轴使用 **-Y 轴向上，+Z 轴向前** 的约定。因此，任何发布到 ROS (ROS Cameras) 或 ROS2 (ROS2 Cameras) 的摄像头数据，包括变换关系，都将以这个约定来表示。下图显示了在 ROS 坐标轴约定下表示的摄像头图元。
		  ![ROS 坐标系下的摄像头](https://docs.isaacsim.omniverse.nvidia.com/latest/_images/camera_frames_v2.003.png)
	- #### **这些坐标系之间的转换**
		- 要以正确的坐标轴约定来观察摄像头图元的位姿，请参阅[摄像头检查器 (Camera Inspector) 教程](https://docs.isaacsim.omniverse.nvidia.com/latest/manual_tutorials/tutorial_manual_camera_inspector.html)。