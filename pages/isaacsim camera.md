- #isaacsim
- Isaac Sim/Omniverse 支持三种不同的相机坐标系：
  
  1. **USD坐标系**  
     • 定义为+Y向上，-Z向前  
     • 场景属性面板（Property tab）显示的数据  
     • 计算机图形学领域的通用标准
  
  2. **世界坐标系**  
     • 定义为+Z向上，+X向前  
     • 通过`omni.isaac.sensor`的`Camera`类使用：  
       `set_world_pose()`和`get_world_pose()`方法操作该坐标系下的位姿
  
  3. **ROS相机坐标系**  
     • 定义为+Y向上，+Z向前  
     • 通过OmniGraph发布的ROS相机相关消息使用该坐标系
- ### 常见问题
  如果直接将属性面板（USD坐标系）的位姿通过`get/set`方法应用到`omni.isaac.sensor.Camera`（世界坐标系），会导致坐标系不匹配问题。
  
  ---
- ### 解决方案
- #### 方法一：直接使用世界坐标系
  ```python
  from omni.isaac.sensor import Camera 
  from omni.isaac.core.utils.rotations import euler_angles_to_quat
  
  # 在世界坐标系中定义位姿
  pos_world = np.array([1, 1, 1])  # X,Y,Z
  quat_world = euler_angles_to_quat([0, -90, 0], degrees=True)  # 绕Y轴旋转-90度
  
  # 初始化相机
  camera = Camera(position=pos_world, orientation=quat_world)
  
  # 验证位姿
  camera.get_world_pose()  # 返回应与初始化值一致
  ```
- #### 方法二：使用USD坐标系
  通过`XForm`直接操作USD场景中的相机位姿：
  ```python
  # 假设 camera_prim 是场景中的相机Prim路径
  from pxr import UsdGeom
  camera_xform = UsdGeom.XFormable.Get(stage, camera_prim)
  camera_xform.SetTranslate((x, y, z))  # 使用属性面板中的坐标数据
  ```
  
  ---
- ### 未来更新计划 (Isaac Sim)
  1. **多坐标系支持**  
   `omni.isaac.sensor.Camera`将支持直接在不同坐标系（USD/世界/ROS）间获取和设置位姿。
  
  2. **Camera Inspector工具**  
   • 实时显示所有坐标系下的相机位姿  
   • 支持通过视口动态调整相机  
   • 场景数据一键生成代码
  
  3. **新版教程**  
   新增相机操作指南，涵盖GUI和API的深度集成。
  
  ---
- ### 最佳实践建议
  • **调试工具**：使用`print(camera.get_world_pose())`实时验证位姿  
  • **坐标系转换**：当需要跨坐标系操作时，使用`tf`库进行四元数/旋转矩阵转换  
  • **ROS集成**：发布消息前将位姿转换为ROS坐标系（+Y up, +Z forward）
-