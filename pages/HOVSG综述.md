- #综述 #robot_navigation #research #HOVSG #slam
- 方法部分：
	- 如何获取点云数据和相机位姿数据
		- [[FAST-LIO2]]：
		  **FAST (Features from Accelerated Segment Test):**  指的是一种快速特征点检测算法，用于从 LiDAR 点云中提取特征点。
		  **LIO (LiDAR-Inertial Odometry):** 指的是利用 [[LiDAR]] 和 [[IMU]] (惯性测量单元) 数据进行状态估计的方法。
		- [[闭环检测]]：
		  检测机器人是否回到了之前访问过的位置。当机器人回到之前访问过的位置时，闭环检测可以提供一个额外的约束，帮助纠正累积的误差，提高位姿估计的精度和地图的一致性。
		  闭环检测通常通过比较当前帧的 LiDAR 数据与历史帧的 LiDAR 数据来实现。  如果当前帧与历史帧足够相似，则认为检测到了一个闭环。
		- HOV-SG 使用 FAST-LIO2 + 闭环检测 来获取点云数据和 [[RGB-D]] 数据位姿。
	- 3D点云块级特征映射
		- 对于之前获取到的一系列RGBD数据，逐帧进行处理。
			- 3D点云合并：用[[Segment Anything]]将图像分割为不同区域，根据深度信息和位姿数据将每个像素映射到三维空间的一个点。每一张图片对应一块点云。对于多张图片得到多块点云，计算这些点云间的重叠率，大于某个阈值就合并成一块点云。
			- 点级语义赋予：用[[Segment Anything]]将图像分割为不同区域之后，对图片的每一个区域用[[CLIP]]进行特征提取，具体而言，就是对个这个区域进行三类特征提取：去掉背景，保留背景，图片整体，三个图片提取到三个特征在进行加权平均得到的结果作为该区域的特征，再将该区域的二维点映射到三维空间，这样就可以使点云具有语义特征
			- 块级语义赋予：在之前的3D点云合并中得到的点云块中包含多个点，每个点都有特征，文章使用 [[DBSCAN]]算法来对这些特征进行[[聚类]]，并选择离主簇的均值最近的特征作为该块的特征
	- 3D场景图构造
		- 楼层分割：
			- 沿着点云的高度方向 (通常是 Z 轴) 构建点云数量直方图
			- 在直方图中寻找峰值
			- 根据峰值的位置将点云分割成不同的楼层
			- 每个楼层在场景图中表示为一个 **Floor 节点**
		- 房间分割：
			- 将每个楼层的点云投影到 2D 平面，生成鸟瞰图
			- 在楼层平面构建点云密度图，进行阈值处理，将点云密度较高的区域识别为墙壁
			- 根据墙壁图像构建到墙壁距离图，在距离图中找到局部最大值点 (距离墙壁较远的点) 作为种子点
			- 使用[[分水岭算法]]，从种子点开始进行区域生长，将楼层分割成不同的房间区域。
			- 根据房间分割结果，将每个楼层的点云分割成多个房间点云。
			- 每个房间在场景图中表示为一个 **Room 节点**，并与对应的 Floor 节点连接。
		- 构建物体节点：
			- 将之前融合得到的块级点云与每个房间进行比较，如果该点云大部分点在某个房间内，则将该 segment 分配给该房间。
			- 每个物体在场景图中表示为一个 **Object 节点**，并与对应的 Room 节点连接
		- 构建导航图：之前只是进行了节点的构造，而连接节点的边还没完成。
			- **单层 Voronoi 图:**
				- **Pose Region Map (位姿区域地图):**
					- 这个地图表示机器人**曾经到达过的区域**，它是通过将所有相机位姿投影到 2D 平面 (鸟瞰图) 上，并将每个位姿周围的一定区域标记为已访问区域来构建的。
				- **Floor Region Map (楼层区域地图):**
					- 这个地图表示整个楼层的范围
				- **Obstacle Region Map (障碍物区域地图):**
					- 通过将表示障碍物的点云 (例如，墙壁、家具等) 投影到 2D 平面上来获得的。
				- **Free Space Map (自由空间地图):**
					- 位姿地图+楼层区域地图-障碍物地图=自由空间地图
				- 构建维诺图
					- 使用**机器人在每个楼层采集数据时的相机位姿投影到 2D 平面上的点**作为种子点来构建 Voronoi 图
				- 为什么要构建Free Space Map
					- **避免生成穿过障碍物的路径**
					- **确保路径的安全裕度**
					- **处理未探索区域**
			- **跨楼层 Voronoi 图:**  识别楼梯区域，将楼梯区域的相机位姿连接成一个子图，并将该子图与每一层的 Voronoi 图连接起来。
			- 为什么要计算维诺图：
			- Voronoi 图的边是由与两个或多个种子点 (在 HOV-SG 中是相机位姿投影点) 等距的点组成的。  在二维平面上，这些边可以看作是**可通行区域的 “中轴线”**。沿着 Voronoi 图的边行走，可以**最大程度地远离障碍物**，从而提高导航的安全性。
- 实验部分：
	- 3D 语义分割任务
	  logseq.order-list-type:: number
		- 数据集：
			- **ScanNet:** 室内场景的 RGB-D 数据集，包含 1513 个扫描场景和 21 个常见物体类别的语义分割标注。
			- **Replica:**  高保真的 RGB-D 数据集，包含 18 个场景，涵盖办公室和住宅环境，并提供密集的语义标注。
		- 对比方法：
			- **ConceptFusion:**  一种开放词汇的 3D 语义分割方法，它将 CLIP 特征融合到 3D 点云中。
			- **ConceptGraphs:**  一种基于 3D 场景图的开放词汇方法，与 HOV-SG 最为接近。
			- **MinkowskiNet:**  一种在 ScanNet 数据集上训练的监督学习方法，作为性能上限参考。
		- 实验设置：
			- **评估场景:** 为了与 ConceptFusion 和 ConceptGraphs 保持一致，HOV-SG 在 ScanNet 的 scene0011_00, scene0050_00, scene0231_00, scene0378_00, scene0518_00 和 Replica 的 office0-office4 和 room0-room2 场景上进行评估。
			- **CLIP Backbone:**  实验使用了两种 CLIP Backbone：ViT-H-14 和 ViT-L-14 (OVSeg 微调版)。
		- 预测生成：
			- **类别嵌入:** 对于数据集中的每个类别，使用两种提示模板："There is the {category} in the scene." 和 "{category}" 生成 CLIP 文本嵌入，并将这两个嵌入进行平均得到最终的类别嵌入。
			- **对象节点嵌入:**  对于场景图中的每个对象节点，计算其 CLIP 特征嵌入。这里按文章的做法应该是segment合并再提取特征。
			- **相似度计算:** 计算每个对象节点的嵌入与所有类别嵌入之间的余弦相似度。
			- **预测标签:**  对于每个对象节点，选择与其相似度最高的类别作为其预测标签。
			- **点云着色:** 将所有对象节点的点云根据预测的类别进行着色，得到预测的 3D 语义分割点云 Ppred。
			- **坐标系对齐:**  将 Ppred 转换到与 ground-truth 点云 PGT 相同的坐标系下。
		- 评估：
			- **点级评估:**  由于 Ppred 和 PGT 的点密度可能不同，因此对每个 PGT 中的点，在 Ppred 中找到其最近的 5 个点，并使用多数投票的方式确定该点的预测标签。
			- **评估指标:** 使用 mIOU, F-mIOU 和 mAcc 三个指标来评估预测的 3D 语义分割结果与 ground-truth 之间的匹配程度。
	- 评估构建的场景图的质量
	  logseq.order-list-type:: number
		- 数据集：
			- **HM3DSem 数据集:**  选择了 HM3DSem 数据集，因为它提供了跨大型多层场景的真实开放词汇标签，以及对象-区域的对应关系。
			- **数据采集:** 作者手动记录了 HM3DSem 数据集中 8 个场景的随机游走数据，涵盖多个房间和楼层 (00824, 00829, 00843, 00861, 00862, 00873, 00877, 00890)。
			- **Ground Truth 构建:**  根据提供的 RGB-D 数据和全景分割数据，利用精确的位姿信息，融合所有帧的数据，构建 RGB 和全景分割的全局地图，并将其体素化 (voxelized) 为 0.02 厘米分辨率。
		- **B.1 Floor and Class-Agnostic Room Segmentation (楼层和类别无关的房间分割):**
			- **目的:** 评估场景图在几何结构上的准确性，即楼层分割和房间分割的准确性。
			- **Ground Truth:**  由于 HM3DSem 数据集不包含楼层高度标签，作者手动标注了所有楼层的上下边界。 此外，根据对象关联的区域标签，将标注的对象集中到构建的 ground truth 点云中，得到区域级点云作为 ground truth。
			- **评估指标:**
				- **楼层分割:** 使用 0.5 米的阈值评估正确预测的楼层数的准确率。
				- **房间分割:** 使用 Hydra [19] 中定义的 precision 和 recall 指标评估房间分割的准确性。
			- 对比方法：与 Hydra [19] 方法进行对比。
		- **B.2 Semantic Room Classification (语义房间分类):**
			- **目的:**  评估场景图中房间节点的语义分类准确性。
			- **数据集:**  由于 HM3DSem 数据集没有提供房间类别标注，作者手动标注了 8 个场景的房间类别，并提供了一个封闭的房间类别集合。
			- **对比方法:**
				- **Privileged Baseline:**  使用 ground truth 的对象类别，通过 LLM (GPT-3.5 和 GPT-4) 推断房间类别。本质：将数据集自带的房间所包含的对象类别喂给gpt让其判断这是个什么类型的房间。作为完全信息下的比较基线。
				- **Unprivileged Baseline:**  使用 HOV-SG 预测的对象类别，通过 LLM (GPT-3.5 和 GPT-4) 推断房间类别。
			- **评估指标:** 使用严格准确率 (Acc=) 和近似准确率 (Acc~，通过人工评估) 评估房间分类的准确性。
		- **B.3 Object-Level Semantics (对象级语义):**
			- **目的:**  评估场景图中对象节点的开放词汇语义标注的准确性。
			- **评估指标:**  提出了一个新的评估指标 AUCtop-k，用于量化预测对象类别和实际 ground-truth 对象类别之间的 top-k 准确率曲线下的面积。
			- **对比方法:**  与 VLMaps [10] 和 ConceptGraphs [14] 进行对比。
			- **评估细节:**  在 1624 个对象类别的集合上进行评估，通过线性分配预测对象和 GT 对象，只考虑 IOU > 50% 的预测对象。
		- **B.4 Hierarchical Concept Retrieval (分层概念检索):**
			- **目的:** 评估 HOV-SG 场景图在处理分层查询 (例如，“二楼的浴室里的毛巾”) 时的能力。
			- **查询分解:** 使用 GPT-3.5 将分层查询分解成 sought-after hierarchical concepts (例如，[floor 2, bathroom, pillow])。
			- **对比方法:** 与增强版的 ConceptGraphs [14] 进行对比，该版本具有特权楼层信息，并根据请求的房间和对象对对象进行评分。
			- **评估指标:** 使用检索成功率 (SR) 进行评估。
			- **导航实验:**  在 Habitat 模拟器中进行导航实验，使用 Voronoi 图进行路径规划，并报告物理检索成功率。
	- 真实世界的语言引导导航
	  logseq.order-list-type:: number
		- 实验平台
			- **机器人:** Boston Dynamics Spot 四足机器人。
			- **传感器:**  Azure Kinect RGB-D 相机 (用于构建场景图) 和一个 3D LiDAR (用于定位和避障)。
			- **环境:**  一个两层的办公楼，包含多种类型的房间，例如办公室、走廊、厨房、会议室等。
		- 场景图的构建
			- 使用 Azure Kinect RGB-D 相机采集环境数据
			- 使用 HOV-SG 方法构建场景图，包括楼层、房间、物体以及它们之间的关系。  其中，物体检测使用了 Segment Anything (SAM) 和 CLIP 模型。
			- 构建跨楼层的 Voronoi 图作为导航地图。
		- 导航任务
			- **目标选择:**  在两层办公楼中选择 41 个物体目标、9 个房间目标和 2 个楼层目标。
			- **自然语言指令:**  使用自然语言描述导航目标，例如：
				- "go to floor 0" (去一楼)
				  "navigate to the kitchen on floor 1" (导航到一楼的厨房)
				  "find the plant in the office on floor 0" (找到一楼办公室里的植物)
		- 导航流程
			- **指令解析:** 使用 GPT-3.5 将自然语言指令分解成三个层级的查询：楼层、房间和物体 (如果指令中包含物体)。
			- **场景图查询:**  根据分解后的查询，在 HOV-SG 场景图中逐级查询，找到目标楼层、房间和物体。
			- **目标定位:**  将目标物体在 Voronoi 图中最近的节点作为导航目标点。 如果指令中没有指定物体，则将目标房间或楼层在 Voronoi 图中最近的节点作为导航目标点。
			- **路径规划:**  使用 Voronoi 图进行路径规划，找到从机器人当前位置到目标点的最短路径。
			- **路径执行:**  使用 Spot 机器人的导航 API 控制机器人沿着规划的路径移动。
		- 评估指标
			- **检索成功率 (Retrieval Success Rate):** HOV-SG 能否正确找到目标楼层、房间和物体。  如果找到的楼层和房间与指令一致，并且找到的物体与指令中的物体有重叠 (IOU > 0)，则认为检索成功。
			- **导航成功率 (Navigation Success Rate):** 机器人能否成功到达目标位置。  如果机器人到达目标点附近 (距离小于 1 米)，则认为导航成功。
		- 实验结果
			- **检索成功率:**
				- 楼层: 100%
				  房间: 55.6%
				  物体: 70.7%
			- **导航成功率:** 56.1% (在所有物体导航任务上)
		- 失败案例分析
			- **房间检索失败:** 主要原因是不同房间之间存在视觉相似性，例如 "meeting room" (会议室), "seminar room" (研讨室), "dining room" (餐厅) 等，这些房间都包含桌子和椅子，难以区分。
			- **导航失败:**  一个典型的失败案例是导航到 “二楼办公室的白板”。  由于白板贴在墙上，机器人到达了目标附近，但位于墙的另一侧。  另一个失败原因是实验中没有考虑楼梯上的目标位置，以防止机器人做出危险动作。
- 感想：
	- 似乎正确率不高
	  >We achieve a 100% success rate in floor retrieval, a 55.6% success rate in room retrieval, and a 70.7% success rate in object retrieval.
- 改进：
	- 如何增强对复数微小物体的检索。
	- 如何实现动态的修正：比如拿了某个东西之后不需要重复建图，而是就地修改。