- #ros2
- ## 自定义包
  collapsed:: true
	- 这是一个非常核心的 ROS 2 开发问题！
	  
	  简短的答案是：**放在一个 ROS 2 工作空间 (Workspace) 的 `src` 文件夹里。**
	  
	  下面是详细、规范的操作流程和解释，这对于理解 ROS 2 的开发模式至关重要。
	  
	  ---
	- ### ROS 2 工作空间 (Workspace) 的概念
		- 在 ROS 2 中，你不会直接去修改系统安装的 ROS 包（比如那些在 `/opt/ros/humble/` 目录下的）。这样做会破坏你的 ROS 安装，并且在系统更新时你所有的修改都会丢失。
		  
		  相反，你应该创建一个属于你自己的**工作空间**。这只是一个普通的文件夹，但它有特定的内部结构，ROS 2 的编译工具 `colcon` 能够理解它。
		  
		  **一个典型的工作空间结构如下：**
		  ```
		  ros2_ws/          <-- 你的工作空间根目录（名字可以自定）
		  ├── src/          <-- **源代码文件夹 (Source Space)**: 这是你放置所有自定义包的地方！
		  ├── build/        <-- 编译文件夹 (Build Space): colcon 在编译时自动生成，存放中间文件。
		  ├── install/      <-- 安装文件夹 (Install Space): colcon 编译成功后，包的可执行文件、库和配置文件会放在这里。
		  └── log/          <-- 日志文件夹 (Log Space): 存放编译过程的日志。
		  ```
	- ### 标准操作步骤
		- 假设你想创建一个名为 `my_robot_pkg` 的自定义包。
		- #### 第一步：创建工作空间和 `src` 目录
			- 如果你还没有工作空间，先创建一个。打开终端，运行：
			  
			  ```bash
			  # 创建一个名为 ros2_ws 的工作空间，并在其中创建 src 文件夹
			  mkdir -p ~/ros2_ws/src
			  
			  # 进入 src 文件夹，我们接下来的操作将在这里进行
			  cd ~/ros2_ws/src
			  ```
		- #### 第二步：将你的包放在 `src` 目录中
			- 你有两种常见的方式来“放置”一个包：
			  
			  **情况A：从零开始创建一个全新的包**
			  
			  使用 ROS 2 提供的命令行工具 `ros2 pkg create`。
			  
			  ```bash
			  # 确保你当前在 ~/ros2_ws/src 目录下
			  ros2 pkg create --build-type ament_python --node-name my_awesome_node my_robot_pkg
			  ```
			  *   `--build-type ament_python`: 指定这是一个 Python 包。如果是 C++，就用 `ament_cmake`。
			  *   `--node-name my_awesome_node`: 顺便帮你创建一个名为 `my_awesome_node` 的节点文件。
			  *   `my_robot_pkg`: 这是你的包名。
			  
			  执行后，`src` 目录下就会出现一个名为 `my_robot_pkg` 的新文件夹，里面包含了 `package.xml`, `setup.py` 等基本文件。**这就是你放置包的正确位置。**
			  
			  **情况B：从网上（如 GitHub）下载一个现有的包**
			  
			  你想修改或使用一个开源的包。这时使用 `git clone`。
			  
			  ```bash
			  # 确保你当前在 ~/ros2_ws/src 目录下
			  git clone https://github.com/some_organization/some_ros2_package.git
			  ```
			  这会将 `some_ros2_package` 的整个项目文件夹克隆到你的 `src` 目录里。
		- #### 第三步：编译你的工作空间
			- 现在你的包已经在正确的位置了，但 ROS 2 还不知道它的存在。你需要编译工作空间来让它生效。
			  
			  ```bash
			  # 回到工作空间的根目录
			  cd ~/ros2_ws
			  
			  # 使用 colcon 工具进行编译
			  colcon build
			  ```
			  `colcon` 会自动扫描 `src` 目录下的所有包，并对它们进行编译。成功后，你会看到 `build` 和 `install` 目录被创建或更新。
		- #### 第四步：激活（Source）你的工作空间
			- 编译完成后，新包的可执行文件等都在 `install` 目录里。为了让你的终端能找到并运行这些新程序，你需要“激活”这个工作空间的环境。
			  
			  ```bash
			  # 在工作空间的根目录下
			  source install/setup.bash
			  ```
			  **这一步至关重要！**
			  *   它会告诉当前终端：“除了系统默认的 ROS 程序外，请优先使用我这个 `ros2_ws/install` 目录下的程序。”
			  *   每次打开**新终端**想使用这个工作空间的包时，你都需要重新 `source` 一次。为了方便，很多人会把这条命令加到 `~/.bashrc` 文件的末尾。
			  
			  现在，你就可以运行你的自定义包里的节点了：
			  ```bash
			  ros2 run my_robot_pkg my_awesome_node
			  ```
			  
			  ---
	- ### 总结与形象比喻
		- 可以把 ROS 2 的开发想象成**做饭**：
		  
		  *   `/opt/ros/humble/`：这是**超市**。里面有各种现成的食材（官方包），你通常只买来用，不会去改造超市本身。
		  *   `~/ros2_ws/`：这是你家的**厨房**（工作空间）。
		  *   `~/ros2_ws/src/`：这是厨房里的**冰箱和储藏室**（源代码区）。你买来的新食材（`git clone`）或者你自己种的菜（`ros2 pkg create`）都放在这里。
		  *   `colcon build`：这是你的**烹饪过程**（编译）。`colcon` 这个大厨会根据 `src` 里的菜谱（`package.xml`等）把原材料做成菜。
		  *   `~/ros2_ws/install/`：这是你家的**餐桌**（安装区）。做好的菜（可执行程序）都摆在这里，准备上桌。
		  *   `source install/setup.bash`：这是你**告诉家人“开饭了！”**（激活环境）的动作。之后大家就知道可以来餐桌上找吃的了。
		  
		  **所以，永远记住：你的自定义包，永远放在你个人工作空间的 `src` 文件夹里。**
- ## 为什么有些节点又不用编译？
	- 这是一个非常棒的问题，它揭示了 ROS 2 中一个非常重要的区别，也是很多初学者感到困惑的地方。
	  
	  答案的核心在于编程语言的根本不同：**C++ 是编译型语言，而 Python 是解释型语言。**
	  
	  让我们来详细分解一下这意味着什么。
	  
	  ---
	- ### 情况一：C++ 节点（必须编译）
		- 当你用 C++ 编写 ROS 2 节点时，你写的是人类可读的源代码（`.cpp` 文件）。计算机的 CPU 无法直接理解这些代码。
		  
		  **编译** 这个过程，就像是把一份详细的**建筑蓝图**（你的C++代码）交给一个**建筑施工队**（编译器，如`g++`），建造出一座真正的**房子**（一个二进制可执行文件）。
		  
		  **`colcon build` 在这里的作用是：**
		  1.  **调用编译器**：它读取你的 `CMakeLists.txt` 文件（这就像是施工指令），然后指挥编译器把你的 `.cpp` 文件翻译成机器能懂的二进制代码。
		  2.  **链接库**：如果你的代码用到了其他库（比如 `rclcpp`），它会把这些库和你的代码“链接”在一起，形成一个完整的、可以独立运行的程序。
		  3.  **生成可执行文件**：最终产物是一个二进制文件，它被放置在 `install/<包名>/lib/<包名>/` 目录下。
		  
		  `ros2 run my_cpp_pkg my_cpp_node` 实际上是去运行这个编译好的二进制文件。没有这个编译过程，就没有可执行文件，自然也就无法运行。
		  
		  **结论：C++ 节点必须经过 `colcon build` 编译，才能从源代码变成可执行程序。**
		  
		  ---
	- ### 情况二：Python 节点（“编译”的意义不同）
		- 当你用 Python 编写 ROS 2 节点时，你写的是一个脚本（`.py` 文件）。Python 是一种**解释型语言**。
		  
		  这意味着它不需要一个预先的、重量级的编译过程。相反，它有一个**解释器**（`python3`），它在**运行时**逐行读取你的脚本并立即执行。
		  
		  这就像是把一份**菜谱**（你的 Python 脚本）交给一位**厨师**（Python 解释器），厨师会当场阅读菜谱并一步步地做菜。你不需要先把菜谱“建造”成一道菜。
		  
		  **那么，为什么我们仍然要对 Python 包运行 `colcon build` 呢？**
		  
		  这是最关键的一点！对于 Python 包，`colcon build` **不执行传统的编译**，而是执行**安装和注册**。
		  
		  **`colcon build` 对 Python 包做了两件重要的事情：**
		  
		  1.  **安装文件**：它会把你的 Python 脚本（以及其他模块文件）从 `src` 目录复制或链接到 `install` 目录下的一个特定位置（`install/<包名>/lib/pythonX.Y/site-packages/<包名>/`）。这使得你的包可以像其他 Python 库一样被 `import`。
		  
		  2.  **创建入口点 (Entry Point)**：这是最神奇的部分！它会读取你的 `setup.py` 文件，找到 `entry_points` 这个部分。然后，它会在 `install/<包名>/lib/<包_名>/` 目录下创建一个**小小的可执行脚本**。这个脚本其实是一个“包装器”，它的唯一作用就是启动 Python 解释器来运行你指定的 Python 脚本里的 `main` 函数。
		  
		  `ros2 run my_python_pkg my_python_node` 实际上运行的就是这个由 `colcon` 创建的**包装器脚本**，而不是直接运行你的 `.py` 文件。
		  
		  **所以，你可以直接用 `python3 my_node.py` 运行你的脚本吗？**
		  
		  可以，但这样做有很大弊端：
		  *   **ROS 2 工具无法发现它**：`ros2 run`, `ros2 launch` 等工具都依赖于 `colcon` 创建的入口点。直接运行脚本，你的节点就游离于 ROS 2 的生态系统之外。
		  *   **依赖问题**：无法利用 `package.xml` 中定义的依赖关系。
		  *   **不规范、不可移植**：这不是 ROS 2 的标准工作流程。
		  
		  ---
	- ### 总结对比
		- | 特性 | C++ 节点 | Python 节点 |
		  | :--- | :--- | :--- |
		  | **语言类型** | 编译型 | 解释型 |
		  | **`colcon build` 的作用**| **真正的编译**：源代码 -> 二进制可执行文件 | **安装和注册**：复制文件并创建可执行的“包装器”脚本 |
		  | **运行前是否必须**| **是**，否则没有可执行文件 | **是 (为了规范)**，否则 ROS 2 工具无法找到它 |
		  | **`ros2 run` 执行的是** | 编译生成的二进制文件 | `colcon` 创建的包装器脚本 |
		  | **关键配置文件** | `CMakeLists.txt` | `setup.py` (特别是 `entry_points`) |
		  
		  **最终结论：**
		  
		  虽然 Python 节点在技术上不需要“编译”成机器码，但你**仍然必须使用 `colcon build`** 来将它们正确地**安装和注册**到 ROS 2 的工作空间中。这个过程确保了你的节点能够被 ROS 2 的所有工具链发现和使用，是规范化开发流程中不可或缺的一步。
- ## 修改了节点代码需要重新build吗
	- 又是一个绝佳的、非常实际的问题！这直接关系到你的日常开发效率。
	  
	  答案是：**这取决于你用的是 C++ 还是 Python，以及你最初是如何编译的。**
	  
	  让我们分情况详细讨论。
	- ### 总结速览 (TL;DR)
		- *   **对于 C++ 节点**：**是的，必须重新 `colcon build`**。因为代码的任何改动都需要重新编译成二进制可执行文件。
		  *   **对于 Python 节点**：**通常不需要，如果你使用了 `--symlink-install` 这个选项**。你只需要重启节点即可。如果没有用这个选项，那就需要重新 `colcon build`。
		  
		  ---
	- ### 情况一：C++ 节点
		- **你必须重新编译。**
		  
		  **原因：**
		  C++ 是一种编译型语言。你修改的 `.cpp` 或 `.hpp` 文件是源代码。`colcon build` 的作用是将这些源代码编译成一个机器可以执行的二进制文件（就像 Windows 里的 `.exe`）。
		  
		  当你修改了源代码后，那个已经存在于 `install/` 目录下的二进制可执行文件就**过时了**，它并不知道你的改动。
		  
		  **标准工作流程：**
		  1.  修改你的 C++ 源代码文件。
		  2.  保存文件。
		  3.  回到你的工作空间根目录 (`~/ros2_ws`)。
		  4.  运行 `colcon build`。为了节省时间，你可以只编译你修改的那个包：
		    ```bash
		    # 只编译 my_cpp_pkg 这个包，速度更快
		    colcon build --packages-select my_cpp_pkg
		    ```
		  5.  重新 `source install/setup.bash` (虽然有时不是必须的，但养成习惯最好)。
		  6.  重新运行你的节点 `ros2 run my_cpp_pkg my_cpp_node`。
		  
		  ---
	- ### 情况二：Python 节点
		- **这取决于你如何执行的 `colcon build`。**
		  
		  Python 是解释型语言，这意味着它没有一个重量级的编译步骤。`ros2 run` 实际上是让 Python 解释器去执行你的 `.py` 脚本。关键在于 `install/` 目录下的脚本是如何链接到 `src/` 目录下的源文件的。
		- #### 选项 A：常规编译（`colcon build`）
			- 默认情况下，`colcon build` 会**复制**你的 Python 文件从 `src/` 目录到 `install/` 目录。
			  
			  *   **你的修改**：发生在 `src/my_python_pkg/my_python_pkg/my_node.py`。
			  *   **ROS 运行的是**：`install/my_python_pkg/lib/pythonX.Y/site-packages/my_python_pkg/my_node.py`。
			  
			  因为是复制关系，你修改了 `src` 里的文件，`install` 里的副本并不会自动更新。所以，在这种情况下，你**必须重新运行 `colcon build`** 来把新的代码复制过去。
		- #### 选项 B：使用符号链接安装（`--symlink-install`） - 强烈推荐！
			- 这是一个能极大提升 Python 开发效率的“魔法”选项。
			  
			  当你运行 `colcon build --symlink-install` 时，`colcon` 不会复制文件，而是在 `install/` 目录下创建一个**符号链接 (Symbolic Link)**，它就像一个**快捷方式**，直接指向 `src/` 目录下的原始文件。
			  
			  *   **你的修改**：发生在 `src/my_python_pkg/my_python_pkg/my_node.py`。
			  *   **ROS 运行的是**：`install/` 目录下的一个**快捷方式**，这个快捷方式指向的就是你在 `src/` 里修改的那个文件。
			  
			  **这意味着，只要你保存了 `src` 里的文件，ROS 在运行时访问的实际上就是你刚刚修改的最新版本！**
			  
			  **使用 `--symlink-install` 的工作流程：**
			  1.  **（仅第一次或添加新文件/节点时）** 在工作空间根目录运行：
			    ```bash
			    colcon build --symlink-install
			    source install/setup.bash
			    ```
			  2.  在一个终端运行你的节点：`ros2 run my_python_pkg my_python_node`。
			  3.  **修改你的 Python 节点代码并保存。**
			  4.  回到运行节点的那个终端，按 `Ctrl+C` 停止它。
			  5.  **无需重新编译**，直接再次运行 `ros2 run my_python_pkg my_python_node`。你的改动立刻就生效了！
			  
			  ---
	- ### 特殊情况：修改了配置文件
		- 有一类修改，无论 C++ 还是 Python，都**必须**重新运行 `colcon build`：
		  
		  *   **修改了 `package.xml`**：比如，你添加了一个新的依赖。`colcon` 需要重新读取它来检查依赖关系。
		  *   **修改了 `CMakeLists.txt` (C++)**：这是 C++ 包的“编译菜谱”，任何改动都必须重新编译。
		  *   **修改了 `setup.py` (Python)**：比如，你添加了一个新的节点到 `entry_points`，或者添加了新的依赖。`colcon` 需要重新运行这个脚本来创建新的“入口点”包装器。
		  *   **添加或删除了文件**：特别是 launch 文件或配置文件，需要重新 build 来把它们安装到 `install` 目录。
	- ### 总结与推荐
		- | 修改类型 | C++ 节点 | Python 节点 (使用 `--symlink-install`) |
		  | :--- | :--- | :--- |
		  | **修改节点代码逻辑** | **必须 `colcon build`** | **不需要**，只需重启节点 |
		  | **修改配置文件** (`package.xml`, `CMakeLists.txt`, `setup.py`) | **必须 `colcon build`** | **必须 `colcon build`** |
		  | **添加新的节点文件** | **必须 `colcon build`** | **必须 `colcon build`** (因为要更新 `setup.py`) |
		  
		  **给你的最终建议：**
		  
		  > **如果你主要用 Python 开发，养成第一次就使用 `colcon build --symlink-install` 的习惯。** 这会让你后续的开发过程变得非常流畅，只需“修改代码 -> 重启节点”即可，大大节省了等待编译的时间。