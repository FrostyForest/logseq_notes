- #isaacsim #python
- **Python 环境**
  
  本文档将涵盖以下内容：
  
  *   关于独立 Python 脚本如何工作的详细信息。
  *   一份简短的、有趣/有用的独立 Python 脚本列表，供您尝试。
  *   用于为 NVIDIA Isaac Sim 开发 Python 脚本的资源，例如 VSCode 和 Jupyter Notebook 支持。
  
  **详细信息：python.sh 的工作原理**
  
  **注意**
  
  *   在 Windows 上使用 `python.bat` 代替 `python.sh`
  *   下面关于 `python.sh` 工作原理的详细信息与 `python.bat` 的工作原理类似
  
  此脚本首先定义了 apps 文件夹的位置，以便运行时可以找到包含的 `.kit` 文件。
  
  ```bash
  # 获取脚本路径
  SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"
  # apps 目录相对于脚本所在的目录
  export EXP_PATH=$SCRIPT_DIR/apps
  ```
  
  然后，我们引用 NVIDIA Isaac Sim Python 环境，以便正确加载所有扩展接口。
  
  ```bash
  source ${SCRIPT_DIR}/setup_python_env.sh
  ```
  
  `setup_python_env.sh` 脚本更新/定义了以下环境变量：
  
  *   `ISAAC_PATH`: 主 isaac 文件夹的路径
  *   `PYTHONPATH`: 每个扩展 Python 接口的路径
  *   `LD_LIBRARY_PATH`: 运行时查找符号所需的二进制接口的路径
  *   `CARB_APP_PATH`: 核心 Omniverse kit 可执行文件的路径
  
  最后，我们执行 Omniverse 打包的 Python 解释器：
  
  ```bash
  python_exe=${PYTHONEXE:-"${SCRIPT_DIR}/kit/python/bin/python3"}
  ...
  $python_exe $@
  ```
  
  **SimulationApp**
  
  `SimulationApp` 类提供了便捷的功能来管理 NVIDIA Isaac Sim 应用程序的生命周期。
  
  **用法示例：**
  
  以下代码提供了一个关于如何使用 `SimulationApp` 创建应用程序、前进时间步然后退出的用法示例。
  
  **注意**
  
  任何 Omniverse 级别的导入都必须在类实例化之后进行。 因为 API 由扩展/运行时插件系统提供，所以必须先加载插件系统，然后才能导入 API。
  
  **重要**
  
  以无头模式运行时：
  
  *   在初始化 `SimulationApp` 时，在配置中设置 `"headless": True`
  *   任何创建/打开 matplotlib 窗口的调用都需要注释掉
  
  ```python
  from isaacsim import SimulationApp
  
  # 展示如何启动和停止 helper 的简单示例
  simulation_app = SimulationApp({"headless": True})
  
  ### 在 helper 加载后，在此处执行任何 omniverse 导入 ###
  
  simulation_app.update()  # 渲染单帧
  simulation_app.close()  # 清理应用程序
  ```
  
  **详细信息：SimulationApp 的工作原理**
  
  尽管 `SimulationApp` 进一步配置了应用程序并公开了 API，但在任何基于 Omniverse Kit 的实现中，都必须执行一些基本步骤。
  
  第一步是获取 carbonite 框架。 此处，环境变量（例如：`CARB_APP_PATH`、`ISAAC_PATH` 和 `EXP_PATH`）在运行 `python.sh` 脚本时已定义。
  
  ```python
  import carb
  import omni.kit.app
  
  framework = carb.get_framework()
  framework.load_plugins(
      loaded_file_wildcards=["omni.kit.app.plugin"],
      search_paths=[os.path.abspath(f'{os.environ["CARB_APP_PATH"]}/kernel/plugins')],
  )
  ```
  
  加载框架后，可以在加载应用程序之前配置启动参数。 例如：
  
  ```python
  # 注入 experience 配置
  sys.argv.insert(1, f'{os.environ["EXP_PATH"]}/isaacsim.exp.base.python.kit')
  
  # 添加扩展的路径
  sys.argv.append(f"--ext-folder")
  sys.argv.append(f'{os.path.abspath(os.environ["ISAAC_PATH"])}/exts')
  
  # 以无头模式运行
  sys.argv.append("--no-window")
  ```
  
  然后启动应用程序。
  
  ```python
  app = omni.kit.app.get_app()
  app.startup("Isaac-Sim", os.environ["CARB_APP_PATH"], sys.argv)
  ```
  
  关闭正在运行的应用程序通过调用 `shutdown` 然后卸载框架来完成：
  
  ```python
  app.shutdown()
  framework.unload_all_plugins()
  ```
  
  **启用其他扩展**
  
  有两种方法可以添加其他扩展：
  
  *   在 experience 文件（例如：`apps/isaacsim.exp.base.python.kit`）的 `[dependencies]` 部分下：
  
  ```ini
  [dependencies]
  # 在 UI 中启用 layers 和 stage 窗口
  "omni.kit.window.stage" = {}
  "omni.kit.widget.layers" = {}
  ```
  
  *   从 Python 代码中：
  
  ```python
  from isaacsim import SimulationApp
  
  # 启动应用程序
  simulation_app = SimulationApp({"headless": False})
  
  # 获取用于启用扩展的实用程序
  from isaacsim.core.utils.extensions import enable_extension
  
  # 在 UI 中启用 layers 和 stage 窗口
  enable_extension("omni.kit.widget.stage")
  enable_extension("omni.kit.widget.layers")
  
  simulation_app.update()
  ```
  
  **独立示例脚本**
  
  **时间步进 (Time Stepping)**
  
  此示例演示了如何启动 Omniverse Kit Python 应用程序，然后创建回调，这些回调在每个渲染帧和每个物理时间步被调用。 它还展示了步进物理和渲染的不同方式。
  
  可以通过运行以下命令来执行此示例：
  
  ```bash
  ./python.sh standalone_examples/api/isaacsim.core.api/time_stepping.py
  ```
  
  **加载 USD Stage (Load USD Stage)**
  
  此示例演示了如何加载 USD stage 并开始模拟它。
  
  可以通过运行以下命令来执行此示例，将 `usd_path` 指定为 Nucleus 服务器上的位置：
  
  ```bash
  ./python.sh standalone_examples/api/isaacsim.simulation_app/load_stage.py --usd_path /Isaac/Environments/Simple_Room/simple_room.usd
  ```
  
  **URDF 导入 (URDF Import)**
  
  此示例演示了如何使用 URDF Python API，配置其物理属性，然后模拟固定帧数。
  
  可以通过运行以下命令来执行此示例：
  
  ```bash
  ./python.sh standalone_examples/api/isaacsim.asset.importer.urdf/urdf_import.py
  ```
  
  **更改分辨率 (Change Resolution)**
  
  此示例演示了如何在运行时更改视口的分辨率。
  
  可以通过运行以下命令来执行此示例：
  
  ```bash
  ./python.sh standalone_examples/api/isaacsim.simulation_app/change_resolution.py
  ```
  
  **将 Assets 转换为 USD (Convert Assets to USD)**
  
  此示例演示了如何批量将 OBJ/STL/FBX assets 转换为 USD。
  
  要使用示例数据执行它，请运行以下命令：
  
  ```bash
  ./python.sh standalone_examples/api/omni.kit.asset_converter/asset_usd_converter.py --folders standalone_examples/data/cube standalone_examples/data/torus
  ```
  
  包含 OBJ/STL/FBX assets 的输入文件夹作为参数指定，它将在终端输出转换后的 USD 文件的路径。
  
  ```
  Converting folder standalone_examples/data/cube...
  ---Added standalone_examples/data/cube_converted/cube_fbx.usd
  
  Converting folder standalone_examples/data/torus...
  ---Added standalone_examples/data/torus_converted/torus_stl.usd
  ```
  
  此示例利用了 Asset Importer 扩展的 Python API。
  
  有关导入选项的详细信息，请点击[此处](链接可能会在此处)。
  
  **Livestream (直播)**
  
  此示例演示了如何在原生 Python 中运行时启用直播。
  
  有关运行客户端的更多信息，请参阅 [Isaac Sim WebRTC Streaming Client](链接可能会在此处)。
  
  ```bash
  ./python.sh standalone_examples/api/isaacsim.simulation_app/livestream.py
  ```
  
  **注意**
  
  *   运行 `livestream.py` 不会启用所有默认的 Isaac Sim 扩展。 有关更多信息，请参阅 [启用其他扩展](#enabling-additional-extensions)。
  
  ---
-