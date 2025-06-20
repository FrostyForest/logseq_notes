- #isaacsim
- # core api 文档
  collapsed:: true
	- https://docs.isaacsim.omniverse.nvidia.com/latest/core_api_tutorials/index.html
	- ## 场景设置
		- 好的，这是对 **"Scene Setup Snippets"** (场景设置代码片段) 文档的完整中文翻译和解析。
		  
		  这份文档是一份非常实用的速查手册，提供了大量通过 Python 脚本来操作 Isaac Sim 场景、物体和物理属性的常用代码示例。
		  
		  ---
		- ### **场景设置代码片段**
		- #### **物体的创建与操作**
			- > **注意**
			  > 以下脚本应仅在默认的新建舞台上运行，并且只运行一次。您可以通过 `File->New` 创建一个新舞台，然后从 `Window-> Script Editor` (脚本编辑器) 运行这些代码来尝试。
			  
			  ---
		- #### **刚体对象的创建**
			- 以下代码片段在场景中添加了一个具有给定属性的动态立方体和一个地面。
			  ```python
			  import numpy as np
			  from isaacsim.core.api.objects import DynamicCuboid
			  from isaacsim.core.api.objects.ground_plane import GroundPlane
			  from isaacsim.core.api.physics_context import PhysicsContext
			  
			  # 初始化物理上下文
			  PhysicsContext()
			  # 添加地面
			  GroundPlane(prim_path="/World/groundPlane", size=10, color=np.array([0.5, 0.5, 0.5]))
			  # 添加动态立方体
			  DynamicCuboid(prim_path="/World/cube",
			    position=np.array([-.5, -.2, 1.0]),
			    scale=np.array([.5, .5, .5]),
			    color=np.array([.2,.3,0.]))
			  ```
			  
			  ---
		- #### **视图对象 (View Objects)**
			- 在此扩展中，**视图 (View)** 类是相似图元 (prim) 的集合。视图类以**矢量化 (vectorized)** 的方式操作其底层的物体。大多数视图 API 都要求在它们被使用之前，世界 (World) 和物理仿真已经初始化。这可以通过将视图类添加到 `World` 的场景中并重置世界来实现，如下所示：
			  ```python
			  from isaacsim.core.api.world import World
			  from isaacsim.core.prims import RigidPrim
			  from isaacsim.core.api.objects import DynamicCuboid
			  
			  # 视图类在被添加到场景且世界被重置时初始化
			  world = World()
			  cube = DynamicCuboid(prim_path="/World/cube_0")
			  # RigidPrim 视图会管理所有路径匹配 /World/cube_[0-100] 的图元
			  rigid_prim = RigidPrim(prim_paths_expr="/World/cube_[0-100]")
			  world.scene.add(rigid_prim)
			  world.reset()
			  # rigid_prim 现在已初始化并可以使用
			  ```
			  以上代码在通过 Isaac Sim 的 Python 脚本运行时有效。当使用 `Window-> Script Editor` (脚本编辑器) 运行代码片段时，您需要使用 `reset` 的**异步 (asynchronous)** 版本，如下所示：
			  ```python
			  import asyncio
			  from isaacsim.core.api.world import World
			  from isaacsim.core.prims import RigidPrim
			  from isaacsim.core.api.objects import DynamicCuboid
			  
			  async def init():
			    if World.instance():
			        World.instance().clear_instance()
			    world=World()
			    await world.initialize_simulation_context_async()
			    world.scene.add_default_ground_plane(z_position=-1.0)
			    cube = DynamicCuboid(prim_path="/World/cube_0")
			    rigid_prim = RigidPrim(prim_paths_expr="/World/cube_[0-100]")
			    # 视图类在被添加到场景且世界被重置时在内部进行初始化
			    world.scene.add(rigid_prim)
			    await world.reset_async()
			    # rigid_prim 现在已初始化并可以使用
			  
			  asyncio.ensure_future(init())
			  ```
			  请参阅 [工作流教程](https://docs.isaacsim.omniverse.nvidia.com/latest/manual_tutorials/tutorial_manual_workflows.html) 以获取有关在 Isaac Sim 中进行开发的各种工作流的更多详细信息。
			  
			  ---
		- #### **创建 RigidPrim 视图**
			- 以下代码片段在场景中添加了三个立方体，并创建了一个 `RigidPrim` (前身为 `RigidPrimView`) 来批量操作它们。
			  ```python
			  import asyncio
			  import numpy as np
			  from isaacsim.core.api.world import World
			  from isaacsim.core.prims import RigidPrim
			  from isaacsim.core.api.objects import DynamicCuboid
			  
			  async def example():
			    if World.instance():
			        World.instance().clear_instance()
			    world=World()
			    await world.initialize_simulation_context_async()
			    world.scene.add_default_ground_plane(z_position=-1.0)
			  
			    # 创建刚体立方体
			    for i in range(3):
			        DynamicCuboid(prim_path=f"/World/cube_{i}")
			  
			    # 创建视图对象以批量操作这些立方体
			    rigid_prim = RigidPrim(prim_paths_expr="/World/cube_[0-2]")
			    world.scene.add(rigid_prim)
			    await world.reset_async()
			    
			    # 设置世界坐标系下的位姿
			    rigid_prim.set_world_poses(positions=np.array([[0, 0, 2], [0, -2, 2], [0, 2, 2]]))
			  
			  asyncio.ensure_future(example())
			  ```
			  请参阅 **API 文档** 以了解 `RigidPrim` 支持的所有可能操作。
			  
			  ---
		- #### **创建 RigidContactView (刚体接触视图)**
			- 在某些场景下，您可能对每个物体上的**净接触力 (net contact forces)** 以及特定物体之间的**接触力 (contact forces)** 感兴趣。这可以通过由 `RigidPrim` 管理的 `RigidContactView` 对象来实现。
			  ```python
			  import asyncio
			  import numpy as np
			  from isaacsim.core.api.world import World
			  from isaacsim.core.prims import RigidPrim
			  from isaacsim.core.api.objects import DynamicCuboid
			  
			  async def example():
			    if World.instance():
			        World.instance().clear_instance()
			    world = World()
			    await world.initialize_simulation_context_async()
			    world.scene.add_default_ground_plane()
			  
			    # 创建三个坐在另外三个立方体顶部的刚体立方体
			    for i in range(3):
			        DynamicCuboid(prim_path=f"/World/bottom_box_{i+1}", size=2, color=np.array([0.5, 0, 0]), mass=1.0)
			        DynamicCuboid(prim_path=f"/World/top_box_{i+1}", size=2, color=np.array([0, 0, 0.5]), mass=1.0)
			  
			    # 如前所述，创建 RigidPrim 来操作底部的盒子，但这次将顶部的盒子指定为该视图对象的过滤器
			    # 这允许接收底部盒子和顶部盒子之间的接触力
			    bottom_box = RigidPrim(
			        prim_paths_expr="/World/bottom_box_*",
			        name="bottom_box",
			        positions=np.array([[0, 0, 1.0], [-5.0, 0, 1.0], [5.0, 0, 1.0]]),
			        contact_filter_prim_paths_expr=["/World/top_box_*"], # 过滤器
			    )
			    # 创建一个 RigidPrim 来操作顶部的盒子
			    top_box = RigidPrim(
			        prim_paths_expr="/World/top_box_*",
			        name="top_box",
			        positions=np.array([[0.0, 0, 3.0], [-5.0, 0, 3.0], [5.0, 0, 3.0]]),
			        track_contact_forces=True, # 开启接触力追踪
			    )
			  
			    world.scene.add(top_box)
			    world.scene.add(bottom_box)
			    await world.reset_async()
			  
			    # 作用在底部盒子上的净接触力
			    print(bottom_box.get_net_contact_forces())
			    # 顶部和底部盒子之间的接触力
			    print(bottom_box.get_contact_force_matrix())
			  
			  asyncio.ensure_future(example())
			  ```
			  关于摩擦力和接触力的更详细信息，可以分别从 `get_friction_data` 和 `get_contact_force_data` 获得。这些 API 提供了传感器图元和过滤器图元对之间的所有接触力和接触点。`get_contact_force_data` API 还提供了接触距离和接触法向量。
			  
			  在下面的例子中，我们在场景中添加了三个盒子，并对每个盒子施加一个大小为 10 的切向力。然后我们使用上述 API 来接收所有的接触信息，并对所有接触点进行求和，以找出盒子和地面之间的摩擦力/法向力。
			  ```python
			  # ... (contact_force_example 代码) ...
			  ```
			  请参阅 **API 文档** 以获取关于 `RigidContactView` 的更多信息。
			  
			  ---
		- #### **为网格设置质量属性**
			- 下面的代码片段展示了如何设置一个物理对象的质量。也可以指定密度作为替代方案。
			  ```python
			  import omni
			  from pxr import UsdPhysics
			  from omni.physx.scripts import utils
			  
			  stage = omni.usd.get_context().get_stage()
			  result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cube")
			  
			  # 获取图元
			  cube_prim = stage.GetPrimAtPath(path)
			  # 使其成为一个刚体
			  utils.setRigidBody(cube_prim, "convexHull", False)
			  
			  # 应用质量 API
			  mass_api = UsdPhysics.MassAPI.Apply(cube_prim)
			  # 创建质量属性，值为 10
			  mass_api.CreateMassAttr(10)
			  
			  ### 或者，设置密度
			  mass_api.CreateDensityAttr(1000)
			  ```
			  
			  ---
		- #### **获取网格的尺寸**
			- 下面的代码片段展示了如何获取一个网格的尺寸。
			  ```python
			  import omni
			  from pxr import Usd, UsdGeom, Gf
			  
			  stage = omni.usd.get_context().get_stage()
			  result, path = omni.kit.commands.execute("CreateMeshPrimCommand", prim_type="Cone")
			  
			  # 获取图元
			  prim = stage.GetPrimAtPath(path)
			  # 获取尺寸
			  bbox_cache = UsdGeom.BBoxCache(Usd.TimeCode.Default(), includedPurposes=[UsdGeom.Tokens.default_])
			  bbox_cache.Clear()
			  prim_bbox = bbox_cache.ComputeWorldBound(prim)
			  prim_range = prim_bbox.ComputeAlignedRange()
			  prim_size = prim_range.GetSize()
			  print(prim_size)
			  ```
			  
			  ---
		- #### **在整个舞台上应用语义数据**
			- 下面的代码片段展示了如何通过遍历整个舞台，以编程方式在物体上应用语义数据。
			  ```python
			  # ... (remove_prefix, remove_numerical_suffix, remove_underscores 函数) ...
			  
			  stage = omni.usd.get_context().get_stage()
			  for prim in stage.Traverse():
			    if prim.GetTypeName() == "Mesh":
			        # ... (从路径中提取并处理标签字符串) ...
			        add_update_semantics(prim, semantic_label=label, type_label="class")
			  ```
			  
			  ---
		- #### **将资产转换为 USD**
			- 下面的脚本将把一个非 USD 格式的资产（如 OBJ/STL/FBX）转换为 USD。这应该在脚本编辑器内部使用。若要作为独立应用程序运行，请查看 [Python 环境](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_python.html)。
			  ```python
			  import carb
			  import omni
			  import asyncio
			  
			  async def convert_asset_to_usd(input_obj: str, output_usd: str):
			    import omni.kit.asset_converter
			    # ... (设置转换器上下文和选项) ...
			    instance = omni.kit.asset_converter.get_instance()
			    task = instance.create_converter_task(input_obj, output_usd, progress_callback, converter_context)
			    success = await task.wait_until_finished()
			    # ...
			    print("转换完成")
			  
			  asyncio.ensure_future(
			    convert_asset_to_usd(
			        "</path/to/mesh.obj>",
			        "</path/to/mesh.usd>",
			    )
			  )
			  ```
			  关于第 13-23 行可选导入选项的详细信息可以在[这里](https://docs.omniverse.nvidia.com/py/kit/docs/kit-sdk/py/omni.kit.asset_converter/omni.kit.asset_converter.html#omni.kit.asset_converter.AssetConverterContext)找到。
			  
			  ---
		- ### **物理操作指南 (Physics How-Tos)**
		- #### **创建一个物理场景**
			- ```python
			  import omni
			  from pxr import Gf, Sdf, UsdPhysics
			  
			  stage = omni.usd.get_context().get_stage()
			  # 向舞台添加一个物理场景图元
			  scene = UsdPhysics.Scene.Define(stage, Sdf.Path("/World/physicsScene"))
			  # 设置重力向量
			  scene.CreateGravityDirectionAttr().Set(Gf.Vec3f(0.0, 0.0, -1.0))
			  scene.CreateGravityMagnitudeAttr().Set(981.0)
			  ```
			  可以添加以下内容来设置特定设置，本例中使用 CPU 物理和 TGS 解算器：
			  ```python
			  from pxr import PhysxSchema
			  # ... (应用 PhysxSceneAPI 并设置各种物理参数) ...
			  ```
			  通过以下代码可以将地面添加到舞台上：它创建了一个 Z 轴向上、尺寸为 100 厘米、位于 Z 坐标 -100 的平面。
			  ```python
			  import omni
			  from pxr import PhysicsSchemaTools
			  stage = omni.usd.get_context().get_stage()
			  PhysicsSchemaTools.addGroundPlane(stage, "/World/groundPlane", "Z", 100, Gf.Vec3f(0, 0, -100), Gf.Vec3f(1.0))
			  ```
			  
			  ---
		- #### **为网格启用物理和碰撞**
			- 下面的脚本假设舞台中已有一个物理场景。
			  ```python
			  import omni
			  from omni.physx.scripts import utils
			  
			  # ... (创建立方体) ...
			  # 在图元上启用物理
			  # 如果需要更紧密的碰撞近似，请使用 convexDecomposition 而不是 convexHull
			  utils.setRigidBody(cube_prim, "convexHull", False)
			  ```
			  如果需要更紧密的碰撞近似，请使用 `convexDecomposition`。
			  要验证碰撞网格已成功启用，请点击“眼睛”图标 > “按类型显示 (Show By Type)” > “物理网格 (Physics Mesh)” > “全部 (All)”。这将在物体上以粉色轮廓显示碰撞网格。
			  
			  ---
		- #### **遍历舞台并为子物体分配碰撞网格**
			- ```python
			  # ...
			  # 遍历从该路径开始的舞台中的所有图元
			  curr_prim = stage.GetPrimAtPath("/")
			  for prim in Usd.PrimRange(curr_prim):
			    # 只处理几何形状和网格
			    if (prim.IsA(UsdGeom.Cylinder) ... or prim.IsA(UsdGeom.Cube)):
			        # 对常规图元使用凸包
			        utils.setCollider(prim, approximationShape="convexHull")
			    elif prim.IsA(UsdGeom.Mesh):
			        # "None" 将使用基础的三角网格（如果可用）
			        # 也可以使用 "convexDecomposition", "convexHull", "boundingSphere", "boundingCube"
			        utils.setCollider(prim, approximationShape="None")
			    # ...
			  ```
			  
			  ---
		- #### **进行重叠测试**
			- 这些代码片段检测并报告物体何时与指定的立方体/球体区域重叠。假设：舞台包含物理场景，所有物体都启用了碰撞网格，并且已点击播放按钮。
			  参数 `extent`, `origin` 和 `rotation` (或 `origin` 和 `radius`) 定义了要检查重叠的立方体/球体区域。`physX` 查询的输出是与该区域重叠的物体数量。
			  ```python
			  # ...
			  def report_hit(hit):
			    # ... (当检测到碰撞时，将物体颜色变为红色) ...
			    return True
			  
			  def check_overlap():
			    # 定义一个立方体区域来检查重叠
			    extent = carb.Float3(20.0, 20.0, 20.0)
			    # ...
			    # physX 查询以检测立方体区域的命中次数
			    numHits = get_physx_scene_query_interface().overlap_box(...)
			    # physX 查询以检测球形区域的命中次数
			    # numHits = get_physx_scene_query_interface().overlap_sphere(...)
			    return numHits > 0
			  ```
			  
			  ---
		- #### **进行射线投射测试**
			- 此代码片段检测与指定射线相交的最近物体。假设：舞台包含物理场景，所有物体都启用了碰撞网格，并且已点击播放按钮。
			  参数 `origin`, `rayDir` 和 `distance` 定义了一条可能检测到射线命中的射线。查询的输出可用于访问物体的引用及其与射线投射起点的距离。
			  ```python
			  # ...
			  def check_raycast():
			    # 从 'origin' 沿 'rayDir' 方向投射一条长度为 'distance' 厘米的射线
			    origin = carb.Float3(0.0, 0.0, 0.0)
			    rayDir = carb.Float3(1.0, 0.0, 0.0)
			    distance = 100.0
			    # physX 查询以检测最近的命中
			    hit = get_physx_scene_query_interface().raycast_closest(origin, rayDir, distance)
			    if(hit["hit"]):
			        # ... (将物体颜色变为黄色并记录距离) ...
			        return usdGeom.GetPath().pathString, distance
			    return None, 10000.0
			  
			  print(check_raycast())
			  ```
			  
			  ---
		- ### **USD 操作指南 (USD How-Tos)**
		- #### **创建、修改、分配材质**
			- ```python
			  # ...
			  # 使用 OmniGlass.mdl 创建一个新材质
			  omni.kit.commands.execute("CreateAndBindMdlMaterialFromLibrary", ...)
			  # ...
			  # 获取对创建材质的引用
			  mtl_prim = stage.GetPrimAtPath(mtl_created_list[0])
			  # 设置材质输入，这些可以通过查看 .mdl 文件或在舞台窗口中选择材质附加的着色器并查看细节面板来确定
			  omni.usd.create_material_input(mtl_prim, "glass_color", Gf.Vec3f(0, 1, 0), ...)
			  # ...
			  # 将材质绑定到图元
			  UsdShade.MaterialBindingAPI(cube_prim).Bind(...)
			  ```
			  将纹理分配给支持它的材质可以按如下方式完成：
			  ```python
			  # ...
			  # 使用 OmniPBR.mdl 创建一个新材质
			  omni.kit.commands.execute("CreateAndBindMdlMaterialFromLibrary", ...)
			  # ...
			  # 设置材质输入
			  omni.usd.create_material_input(
			    mtl_prim,
			    "diffuse_texture",
			    default_server + "/Isaac/Samples/DR/Materials/Textures/marble_tile.png",
			    Sdf.ValueTypeNames.Asset,
			  )
			  # ...
			  ```
			  
			  ---
		- #### **向图元添加变换矩阵**
			- ```python
			  # ...
			  # 获取图元并设置其变换矩阵
			  cube_prim = stage.GetPrimAtPath("/World/Cube")
			  xform = UsdGeom.Xformable(cube_prim)
			  transform = xform.AddTransformOp()
			  mat = Gf.Matrix4d()
			  mat.SetTranslateOnly(Gf.Vec3d(.10, 1, 1.5))
			  mat.SetRotateOnly(Gf.Rotation(Gf.Vec3d(0,1,0), 290))
			  transform.Set(mat)
			  ```
			  
			  ---
		- #### **对齐两个 USD 图元**
			- ```python
			  # ...
			  # 获取第一个立方体的变换
			  pose = omni.usd.utils.get_world_transform_matrix(prim_a)
			  # ...
			  # 将 prim_b 的位姿设置为 prim_a 的位姿
			  xform_op = xform.AddXformOp(...)
			  xform_op.Set(pose)
			  ```
			  
			  ---
		- #### **获取所选图元在当前时间戳的世界变换**
			- ```python
			  # ...
			  # 获取选定图元的列表
			  selected_prims = usd_context.get_selection().get_selected_prim_paths()
			  # 获取当前时间码
			  timeline = omni.timeline.get_timeline_interface()
			  timecode = timeline.get_current_time() * timeline.get_time_codes_per_seconds()
			  # 循环遍历所有图元并打印其变换
			  for s in selected_prims:
			    curr_prim = stage.GetPrimAtPath(s)
			    # ...
			    pose = omni.usd.utils.get_world_transform_matrix(curr_prim, timecode)
			    # ...
			  ```
			  
			  ---
		- #### **将当前舞台保存到 USD**
			- 这在用 Python 生成舞台并希望存储它以便稍后重新加载进行调试时很有用。
			  ```python
			  import omni
			  import carb
			  
			  # ...
			  # 根据需要更改路径
			  omni.usd.get_context().save_as_stage("/path/to/asset/saved.usd", None)
			  `````
- ## slam教程
  collapsed:: true
	- https://nvidia-isaac-ros.github.io/repositories_and_packages/isaac_ros_visual_slam/isaac_ros_visual_slam/index.html#quickstart
	- nvidia-isaac-ros.github.io/concepts/visual_slam/cuvslam/tutorial_isaac_sim.html
	-
	-
- ## ros2教程
  collapsed:: true
	- ### 发布相机数据
		- [[isaacsim发布相机数据]]
- ## isaacsim约定
	- [[isaacsim约定]]
	-