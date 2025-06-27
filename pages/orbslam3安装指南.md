- #orbslam #slam
- ## ai总结
	- 好的，根据您提供的 `README.md` 文件，ORB-SLAM3 的安装和使用步骤可以总结如下。
	  
	  这是一个清晰、分步的安装指南。
	  
	  ---
	- ### **ORB-SLAM3 安装指南 (根据官方 README.md)**
		- 整个过程主要分为三个部分：**环境准备**、**编译安装** 和 **运行测试**。
		- #### **第一步：环境准备 (Prerequisites)**
			- 在编译 ORB-SLAM3 之前，你需要确保你的系统（推荐 **Ubuntu 16.04** 或 **18.04**）已经安装了以下所有依赖项。
			  
			  1.  **C++11 编译器**: 系统需要支持 C++11 标准。现代的 g++ 通常都支持。
			  
			  2.  **Pangolin**: 用于可视化和用户界面。
			    *   安装指南：[https://github.com/stevenlovegrove/Pangolin](https://github.com/stevenlovegrove/Pangolin)
			  
			  3.  **OpenCV**: 用于图像和特征处理。
			    *   **版本要求**: 最低 3.0，官方测试过 3.2.0 和 4.4.0 版本。
			    *   安装指南：[http://opencv.org](http://opencv.org)
			  
			  4.  **Eigen3**: g2o 库的依赖项。
			    *   **版本要求**: 最低 3.1.0。
			    *   安装指南：[http://eigen.tuxfamily.org](http://eigen.tuxfamily.org)
			  
			  5.  **Python**: 用于评估脚本（计算轨迹与真值的对齐度）。
			    *   需要安装 `Numpy` 模块。
			    *   在 Ubuntu/Debian 系统上，可以通过以下命令安装开发库：
			        ```bash
			        sudo apt install libpython2.7-dev
			        ```
			  
			  6.  **DBoW2 和 g2o**: 这两个库用于位置识别和非线性优化。
			    *   **注意**: 你**不需要**手动下载安装它们。ORB-SLAM3 的代码库中已经包含了修改过的版本，位于 `Thirdparty` 文件夹内，编译时会自动处理。
			  
			  7.  **ROS (可选)**: 如果你想使用 ROS 接口，需要安装 ROS。
			    *   官方测试环境为 **ROS Melodic (Ubuntu 18.04)**。
			    *   这是一个**可选**步骤，只在需要运行 ROS 示例时才需要。
		- #### **第二步：编译和安装 ORB-SLAM3**
			- 当所有依赖都准备好后，就可以开始编译 ORB-SLAM3 主程序库和示例了。
			  
			  1.  **克隆代码仓库**:
			    ```bash
			    git clone https://github.com/UZ-SLAMLab/ORB_SLAM3.git ORB_SLAM3
			    ```
			  
			  2.  **进入项目目录**:
			    ```bash
			    cd ORB_SLAM3
			    ```
			  
			  3.  **执行编译脚本**:
			    *   首先给脚本添加可执行权限：
			        ```bash
			        chmod +x build.sh
			        ```
			    *   然后运行脚本：
			        ```bash
			        ./build.sh
			        ```
			  
			  这个 `build.sh` 脚本会自动编译 `Thirdparty` 文件夹中的 DBoW2 和 g2o，然后再编译 ORB-SLAM3 自身。
			  
			  编译成功后，你会在 `lib` 文件夹下找到库文件 `libORB_SLAM3.so`，并在 `Examples` 文件夹下找到各种可执行的示例程序。
		- #### **第三步：运行与测试**
			- 编译完成后，强烈建议运行官方提供的数据集示例来验证安装是否成功。
			- ##### **A. 运行 EuRoC 数据集示例**
				- 1.  **下载数据**: 从 [EuRoC 官网](http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets) 下载一个序列（例如 `V1_01_easy.zip`），并解压。
				  
				  2.  **修改脚本路径**: 打开项目根目录下的 `euroc_examples.sh` 文件，将 `pathDatasetEuroc` 变量的值修改为你存放数据集的路径。
				  
				  3.  **运行示例**:
				    ```bash
				    ./euroc_examples.sh
				    ```
				    (注意：README 中写的是`./euroc_examples`，但根据文件名应该是`.sh`后缀，请根据实际文件名操作)。
			- ##### **B. (可选) 安装和运行 ROS 示例**
				- 如果你已经安装了 ROS，可以编译并运行 ROS 节点。
				  
				  1.  **设置 ROS 环境变量**:
				    *   打开 `.bashrc` 文件：`gedit ~/.bashrc`
				    *   在文件末尾添加下面这行，并将 `PATH` 替换成你克隆 ORB_SLAM3 的实际路径：
				        ```bash
				        export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:PATH/ORB_SLAM3/Examples/ROS
				        ```
				    *   保存并关闭文件，然后执行 `source ~/.bashrc`。
				  
				  2.  **编译 ROS 节点**:
				    *   给编译脚本添加可执行权限：
				        ```bash
				        chmod +x build_ros.sh
				        ```
				    *   运行脚本：
				        ```bash
				        ./build_ros.sh
				        ```
				  
				  3.  **运行 ROS 示例 (以 EuRoC 数据集为例)**:
				    *   下载一个 EuRoC 的 rosbag 文件 (例如 `V1_02_medium.bag`)。
				    *   打开 **3个** 终端。
				    *   **终端1**: 启动 `roscore`
				        ```bash
				        roscore
				        ```
				    *   **终端2**: 运行 ORB-SLAM3 的 Stereo-Inertial 节点
				        ```bash
				        rosrun ORB_SLAM3 Stereo_Inertial Vocabulary/ORBvoc.txt Examples/Stereo-Inertial/EuRoC.yaml true
				        ```
				    *   **终端3**: 播放 rosbag (注意话题重映射)
				        ```bash
				        rosbag play --pause V1_02_medium.bag /cam0/image_raw:=/camera/left/image_raw /cam1/image_raw:=/camera/right/image_raw /imu0:=/imu
				        ```
				    *   等 ORB-SLAM3 节点加载完词袋后，在播放 rosbag 的终端（终端3）按**空格键**开始播放数据。
				  
				  ---
				  
				  **总结**: 按照 **环境准备 -> 编译安装 -> 运行测试** 的流程操作，即可成功安装并验证 ORB-SLAM3。如果遇到问题，请仔细检查每一步的依赖是否都已正确安装。