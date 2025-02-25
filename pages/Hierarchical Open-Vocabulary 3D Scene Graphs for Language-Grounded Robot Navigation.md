- #robotics #robot_navigation #vlm #HOVSG #paper #research
- # 摘要
  近期的开放词汇机器人建图方法通过预训练的视觉-语言特征丰富了密集几何地图。这些地图虽然能够在针对特定语言概念进行查询时预测逐点显著性图，但在处理大规模环境和超越物体级别的抽象查询时仍面临显著挑战，从而限制了基于语言的机器人导航。在本研究中，我们提出了HOV-SG，一种用于室内机器人导航的分层开放词汇3D场景图建图方法。我们利用开放词汇视觉基础模型，首先在3D中获取最新的开放词汇分割级别地图，随后构建一个包含楼层、房间和物体概念的3D场景图层次结构，并为每个层次添加开放词汇特征。我们的方法能够表示多层建筑，并通过跨楼层的Voronoi图实现机器人遍历。HOV-SG在三个不同数据集上进行了评估，在对象、房间和楼层级别的开放词汇语义精度上超越了现有基线，同时相比密集开放词汇地图实现了75%的表示大小减少。为证明HOV-SG的有效性和泛化能力，我们展示了在真实多层环境中基于语言的长距离机器人导航的成功案例。代码和试验视频数据请访问：
- # 引言
	- 人类通过多模态体验获取关于世界整体和具体物体的概念性知识。这些语义体验对于物体识别、语言、推理以及规划具有重要意义[1, 2]。认知地图通过传感器融合、分解和层次结构存储这些信息，这在导航物理世界的人类能力中起到核心作用[3, 4, 5]。最近的研究表明，语言作为智能系统与人类之间的有效桥梁，可以帮助机器人在复杂的以人为中心的环境中实现自主性[6-14]。
	- 传统的机器人导航方法通过同时定位与建图（SLAM）构建高精度的密集空间地图，这支持基于几何目标的精细导航和操控[15]。近期的进展将密集地图与预训练的零样本视觉-语言模型结合，实现了对观测环境的开放词汇索引[9-17]。尽管这些方法将经典机器人技术与现代开放词汇语义结合，但在表示大型场景和实现抽象化方面仍面临显著挑战。基于真实世界感知输入的可扩展场景表示一般应满足以下要求：1）以物体为中心并具备层次化抽象能力；2）在存储容量和可操作性方面高效；3）实现真正的开放词汇索引和便捷查询。
	- 一些研究通过3D场景图结构实现了对大规模环境的高效表示[18-20]，这些结构同时成为提示大型语言模型（LLM）的有用语义接口。然而，大多数方法依赖于封闭语义集，除了专注于小规模场景的ConceptGraphs [14]。在本研究中，我们提出了分层开放词汇3D场景图（Hierarchical Open-Vocabulary 3D Scene Graphs，简称HOV-SG）。
	- 我们的方法从密集开放词汇地图中抽象出三个不同概念层次，即楼层、房间和物体。我们在所有概念层次中使用开放词汇视觉-语言模型[21, 22]，构建跨多层环境的3D场景图层次结构，同时保持较小的内存占用。由于其以概念为中心的特性，HOV-SG的表示能够被LLM通过提示有效使用。
	  与以往研究不同，我们的方法首先将抽象查询（例如“上层楼浴室中的毛巾”）分解，并将获得的语义标记与层次结构的不同层次进行匹配和评分。此外，我们还引入了一个覆盖多楼层（包括楼梯）的导航Voronoi图，从而将分解的查询有效地与环境关联。这使得从抽象查询中实现物体检索和大规模室内环境中的长距离机器人导航成为可能，如图1所示。
	  总结来说，我们的贡献如下：
	  1）我们提出了一种基于零样本嵌入特征聚类的新型融合方案，在开放集3D语义分割中达到了当前最先进的效果。
	  2）我们设计了一种算法，用于构建真正可操作的多楼层建筑的开放词汇3D场景图。
	  3）我们在Replica[23]和ScanNet[24]数据集上评估了我们方法的语义分割性能，并在Habitat-Matterport 3D Semantics数据集[25]上分析了场景图的关键属性。此外，我们还进行了详细的消融研究以证明设计选择的合理性。
	  4）我们基于自然语言的长查询，在真实多楼层环境中进行了物体导航实验。
	  5）我们引入了一种新的评估指标，称为AUCtop-k，用于衡量开放词汇语义的表现。
	  6）我们将代码和评估协议公开于：
- # 相关工作
	- ## A. 语义3D建图
	  通过语义信息丰富几何地图是实现灵活多功能导航系统的基础[26, 27, 16, 10, 4, 9]。过去的研究通过以下方法创建了增强语义或实例级的地图：
	  学习传感器观测特征[28]；
	  将预先构建的物体形状匹配到几何地图[29]；
	  将2D语义预测反投影到3D空间[30, 31, 32]；
	  用基本3D元素（如立方体或二次曲面）实例化2D检测[33, 34]。
	  这些方法展示了重建具有精确几何结构和语义含义场景的能力。然而，大多数方法依赖于固定的类别集，受限于训练的语义预测模型或预定义的相关物体原型集合。
	  
	  随着大型视觉-语言模型（如CLIP[21]）及其微调版本的进步，一些研究提出了将视觉-语言特征集成到几何地图中的表示方法，从而实现对象[11, 16, 10, 13, 35, 17, 12]、音频数据[16, 13]和图像[26, 16]在非结构化环境中的开放词汇索引。这些方法突破了固定语义类别的限制，但通常需要为地图中的每个几何元素（如点、体素或2D单元格）存储视觉-语言嵌入，导致存储开销显著增加。
	- ## B. 3D场景图
	  
	  3D场景图作为一种以物体为中心的表示形式，在大规模室内[36, 19, 20]和室外场景[18]中表现出色。通过将物体或空间概念表示为节点，将它们的关系表示为边，3D场景图能够高效地表示大型场景[36, 18, 19]。节点和边可以包含几何和语义属性，通常通过现成的网络推断[37]。
	  
	  通过将场景分解为物体及其关系，3D场景图支持机器人导航和操控所需的高层次推理。这在推理、规划和导航领域尤为重要，因为这些任务以物体为中心[19, 38]。结合来自同时定位与建图（SLAM）的里程计估计[39, 18, 40]，3D场景图还能实现语义与高精度建图方法（如网格）之间的紧密结合[19]。
	  
	  早期研究展示了如何通过空间和语义领域的抽象编码层次结构，通常采用离线方法[36, 20]。后续研究探讨了基于学习的场景图构建[41, 37]以及动态室内场景[19]。一些方法（如SceneGraphFusion[37]和S-Graphs[39]）还研究了其提出方法的实时能力。最近，ConceptGraphs[14]首次展示了如何将3D场景图与开放词汇视觉-语言特征结合。此外，该研究还展示了如何使用大型语言模型（LLM）查询图，并实现多种下游应用。
	- ## C. 用于规划的场景图
	  
	  近年来，一些研究探索了将场景图用于机器人规划的可能性。最早的方法依赖预探索的环境，并通过迭代场景图分解来获取有基础的计划[38, 42]。例如，RobLM[42]通过一个微调的GPT-2实例分解规划阶段，提出场景图中的高层次子问题，并通过PDDL任务规划器解决。SayPlan[38]直接利用GPT-4[43]对场景图进行迭代搜索，以生成具有可操作性约束的计划。
	  
	  另一条研究路线探索了基于场景图的机器人导航。SayNav[44]从场景图中获取LLM生成的计划，并执行短距离点目标导航子任务。而VoroNav[45]则构建了一个基于相机观测的Voronoi图，以解决物体导航任务。与此同时，MoMa-LLM[46]使用场景图和GPT-4处理特定任务的移动操控目标。类似地，GRID[47]通过图神经网络预测场景图和LLM编码的操作。
	- ## 本研究的贡献
	  
	  概念上，我们的工作与ConceptGraphs和Hydra最为相似。ConceptGraphs主要在小规模场景中进行评估，并通过人类评估其节点语义精度等属性。而我们的工作不仅提出了一种用于衡量对象特征语义精度的新指标，还引入了开放词汇层次结构。与ConceptGraphs不同，也不同于Hydra[19]（未处理开放词汇特征），我们的方法展示了如何高效地表示具有开放词汇特征的可操作、分层3D场景图。
- # 技术路线
	- 本研究旨在通过RGB-D观测和里程计数据，为大规模多楼层室内环境开发一种简洁高效的视觉-语言图表示方法。该图能够通过自然语言查询实现多层次语义概念的索引，例如“第一层楼”（楼层层次）、“第一层楼的办公室”（房间层次）、以及“第二层楼办公室里的植物”（物体层次）。此外，该图还应具备可操作性，使机器人无需额外的几何地图即可在语义和空间上实现环境的定位和导航。
	  为实现这一目标，我们提出了一种 **分层开放词汇场景图**（Hierarchical Open-Vocabulary Scene Graph，简称HOV-SG）。整个流程包括两个阶段：
	  构建一个3D分段级开放词汇地图。
	  基于该地图创建分层开放词汇场景图。
	  接下来的部分将详细描述：(i) 3D分段级开放词汇地图的构建（第III-A节），(ii) 分层开放词汇场景图的创建（第III-B节），以及(iii) 如何使用该图实现大规模环境中的语言条件导航（第III-C节）。图2展示了我们方法的整体概览。
	- ## A. 3D分段级开放词汇建图
		- 构建分段级开放词汇地图的核心思想是从RGB-D视频和里程计数据中生成一系列3D点云（即分段），并为每个分段分配由预训练视觉-语言模型（VLM）生成的开放词汇特征。与以往为每个3D点单独分配视觉-语言特征的方法不同[11, 10, 13, 12]，我们利用了3D空间中相邻点通常共享相同语义信息的特点。这意味着在保持表达能力的同时，可以减少所需的语义特征数量。
		- ### 帧级3D分段合并
		  
		  给定一组RGB-D观测序列，我们利用Segment Anything[22]为每一时刻生成一系列无类别的2D二值掩码。掩码中的像素随后使用深度信息反投影到3D空间，生成一组点云（即3D分段）。基于精确的里程计估计，我们将所有3D分段转换到全局坐标系中。
		  
		  这些帧级分段通过以下方式初始化为新的全局分段或与已有分段合并：
		  $$R(m,n)=\max(\text{overlap}(S_m, S_n), \text{overlap}(S_n, S_m))$$
		  其中，$$S_m$$和 $$S_n$$ 分别表示分段 m 和 n，$$\text{overlap}(S_a, S_b)$$ 表示分段 $$S_a$$中的点在 $$S_b$$ 中邻居点的数量与 $$S_a$$ 总点数的比值。
		  
		  不同于Gu等[14]通过增量方式将新分段与具有最大重叠比的全局分段合并，我们构建了一个完全连接的图，其中每个分段作为节点，边的权重为对应的重叠比。根据这些权重，将高度连接的子图合并。这种方式允许一个分段与多个分段合并，例如当一个新分段填补了两个已注册全局分段之间的间隙时尤其有用。
		- ### 分段级开放词汇特征计算
		  
		  对于每一帧生成的2D SAM掩码，我们根据其边界框裁剪出目标图像，并生成去除背景的掩码图像。然后，我们分别使用CLIP [21]对完整RGB帧及上述两种掩码图像进行编码，并通过加权求和的方式融合这些特征（图2左侧）。
		  
		  此前的研究[48]提出使用去除背景后的掩码图像的CLIP特征，而另一些研究[13]则通过结合全图像的CLIP特征和目标掩码裁剪区域的CLIP特征（包含背景）来计算特征。在我们的工作中，我们实验证明同时对完整RGB帧和两种掩码图像进行CLIP编码并加权融合能够实现更好的结果（详见第IV-E节）。
		  
		  融合公式如下：
		  
		  $$f_i = w_g f_g + w_l f_l + w_m f_m$$
		  
		  其中，$$f_i$$ 表示帧中第 ii 个2D掩码的融合特征；$$f_g$$、$$f_l$$ 和 $$f_m$$ 分别表示从完整RGB帧、掩码裁剪图像（包含背景）以及掩码裁剪图像（不包含背景）中提取的CLIP特征；$$w_g$$、$$w_l$$ 和 $$w_m$$ 为对应的权重，其总和为1。
		  
		  在为每个掩码生成单个CLIP特征后，我们将2D掩码转换到全局3D坐标系，并将融合后的CLIP特征与预先计算的参考点云中最接近的3D点关联起来。基于这种关联，我们将获得的分段特征注册到全局点特征图中。最终的点特征通过计算每个参考点关联特征的平均值确定。
		  
		  基于独立合并步骤获得的3D分段，我们可以推断出所有3D分段的开放词汇视觉-语言特征，如图2所示。在随后的步骤中，我们将点特征与获得的3D分段匹配。对于分段中的每个点，我们识别其在参考点云中的最近邻点，并收集其CLIP特征。
		  
		  随后，我们利用DBSCAN对分段的所有点特征进行聚类，并将与主簇均值最接近的特征分配给该分段（图2中间部分）。这种方法避免了模式坍缩，同时消除了噪声，从而生成更具语义意义的分段特征。
	- ## B. 3D 场景图构建
	  
	  本节描述如何利用场景的全局参考点云、全局 3D 分段列表及其关联的 CLIP 特征（见第 III-A 节）构建分层的开放词汇场景图。我们将图形式化为 G = (N, E)，其中 N 表示节点，E 表示边。
		- ### **节点表示**
		  
		  图的节点 NN 包括以下四种类型：
		  
		  $$N = N_S \cup N_F \cup N_R \cup N_O$$
		  N_S: 根节点，表示整个场景。
		  N_F: 楼层节点，对应环境中的每一层楼。
		  N_R: 房间节点，表示楼层上的每个房间。
		  N_O: 对象节点，表示房间内检测到的对象。
		  除根节点 N_S 外，每个节点包含：
		  所表示概念对应的点云。
		  相关的开放词汇 CLIP 特征。
		- ### **边表示**
		  
		  图中的边 E 表示层次关系，定义如下：
		  
		  $$E = E_{SF} \cup E_{FR} \cup E_{RO}$$
		  E_{SF}: 根节点与楼层节点之间的边。
		  E_{FR}: 楼层节点与房间节点之间的边。
		  E_{RO}: 房间节点与对象节点之间的边。
		- ### **楼层分割**
		  
		  为了区分楼层，我们在点云中识别高度直方图的峰值：
		  使用 0.01 米的箱宽构建点云在高度轴上的直方图。
		  在直方图中（局部范围为 0.2 米）识别峰值，仅保留超过最高强度峰值 90% 的峰。
		  应用 DBSCAN 聚类算法，并为每个聚类选择排名前两位的峰值。
		  将排序后的高度向量中每两个连续的值表示为建筑物的一个楼层（地板和天花板）。
		  楼层分割过程如图 3 所示。根据得到的高度值，可以提取每层楼的点云 PlP_l，其中 ll 是楼层编号。此外，为每个楼层节点附加一个 CLIP 文本嵌入，模板为“floor {}”。在根节点和每个楼层节点 (NS, Nl) ∈ ESF 之间建立图形边。
		- ### 房间分割
		  基于每个获得的楼层点云，我们构建一个 2D 鸟瞰图 (BEV) 直方图，通过对直方图进行阈值处理，从中提取二值墙骨架掩码。在扩张墙掩码并计算欧几里得距离场 (EDF) 后，通过对 EDF 进行阈值处理可以得出多个孤立区域。将这些区域作为种子，我们应用分水岭算法来获得 2D 区域掩码。房间分割过程进一步如图 3 所示。给定 2D 区域掩码，我们提取落入楼层高度区间以及 BEV 房间段的 3D 点，以形成房间点云，这些点云稍后用于将对象关联到房间。
		- 为了用开放词汇特征丰富房间节点，我们将相机姿态位于房间分割内的 RGB-D 观测值与这些房间相关联（参见图 4）。通过使用 k-means 算法提取 k 个代表性视图嵌入来提炼这些图像的 CLIP 嵌入。在推理过程中，给定一个通过 CLIP 编码的房间类别列表，我们构建一个 k 个代表性特征和所有房间类别特征之间的余弦相似度矩阵。接下来，我们沿着类别轴取最大值（argmax），并分别获得每个代表最可能的房间类型，从而为每个房间产生 k 个投票。根据这些投票，我们通过取每个房间所有 k 个代表中的最高分投票或多数投票来获得预测的房间类别。这 k 个代表性嵌入和房间点云为楼层 f 上的房间 r 赋予房间节点 Nf,r 属性。在楼层节点和每个房间节点之间建立一条边 (Nf, Nf,r) ∈ EFR。图 4 说明了视图嵌入的构建和查询。
		- ### 物体识别
		  给定房间点云，我们将那些在鸟瞰视图中与潜在候选房间点云有重叠的物体级3D分段关联起来。每当一个分段与任何房间都没有重叠时，我们将其关联到与之距离最小的房间。为了减少节点数量，我们合并了那些在成对部分重叠显著的情况下（见第三节A部分），在查询选定的标签集时产生相同物体标签的3D分段。每个合并后的点云构成一个物体节点Nf,r,o，该节点通过边(Nf,r, Nf,r,o) ∈ ERO连接到其对应的房间节点Nf,r ∈ NR。每个物体节点包含其相应的3D分段特征（如第三节A部分所述）、3D分段点云以及一个用于临时命名的最高分物体标签。
		- ### 可操作图的创建
		  除了开放词汇层次结构外，场景图还包含一个导航Voronoi图，用于机器人在已映射环境中的可穿越性，跨越多个楼层[49]。这使得基于Voronoi图的高层规划和低层执行成为可能。可操作图的创建包括构建每层楼和跨楼层的导航图。对于楼层级的图，方法包括计算楼层的自由空间图，并基于此创建Voronoi图[49]。为了构建每层楼的图，我们首先获取所有相机姿态，并将其作为二维点投影到每层楼的俯视图（BEV）上，假设两个节点之间一定半径内的区域是成对可导航的。随后，通过将所有楼层点的投影到BEV平面来获取整个楼层区域。根据在预定高度范围内[ymin + δ1, ymin + δ2]的点生成障碍图，其中ymin为楼层点的最小高度，而δ1 = 0.2，δ2 = 1.5是经验调整的值。通过将姿态区域图和楼层区域图的并集减去障碍区域图，得出每层楼的自由空间图。此自由图的Voronoi图即为楼层图（见图5a）。为了构建跨楼层导航图，将楼梯上的相机姿态连接起来形成楼梯级图。随后，选择楼梯图与楼层图之间最接近的节点并进行连接，从而完成跨楼层导航图的构建，如图5b所示。
	- ## C. 场景图导航
		- HOV-SG 将潜在导航目标的范围扩展到比简单的物体目标[14, 16, 10, 13]更具体的空间概念，如区域和楼层。使用HOV-SG进行语言引导的导航涉及处理复杂的查询，如“在2楼的浴室中找到厕所”，这需要使用大语言模型（提示在补充材料S.1-A节中给出）。我们将这种冗长的指令分解为三个独立的查询：分别针对楼层级、房间级和物体级。
		- 利用HOV-SG的明确层次结构，我们顺序地针对每个层次进行查询，以逐步缩小解决方案的范围。这是通过计算查询楼层、查询区域和查询物体与图中给定的所有物体、房间和楼层的余弦相似度来实现的。一旦通过评分确定了目标节点，我们就利用前面提到的导航图来规划从起始姿态到目标目的地的路径，这在图S.1中演示，并在图1中可视化。
- # 实验评估
	- 我们的实验目标有五个方面：(i) 我们在ScanNet和Replica数据集上通过3D语义分割定量比较HOV-SG与近期的开放词汇地图表示（第四节A部分），(ii) 我们研究HOV-SG在Habitat Matterport 3D语义数据集上的楼层、房间和物体级别的语义和几何精度（第四节B部分），(iii) 我们研究HOV-SG如何在现实世界中支持大规模基于语言的导航（第四节C部分），(iv) 我们展示了HOV-SG与之前的开放词汇表示相比的紧凑内存占用（第四节D部分），最后，(v) 我们通过消融研究证明我们的设计选择（第四节E部分）。
	- ## A. ScanNet和Replica上的3D语义分割
	  为了测试我们HOV-SG方法的语义表达能力，我们在ScanNet [24] 和Replica [23] 上评估了开放词汇3D语义分割性能。我们将我们的方法与两种替代的视觉-语言表示（ConceptFusion [13] 和ConceptGraphs [14]）进行了比较，同时使用不同的CLIP骨干网。我们考虑了ViT-H14和与OVSeg [48] 工作一起发布的微调骨干网ViT-L-14。使用的评估指标在S.2-B节中定义。为了展示零样本和全监督方法之间的差距，我们还评估了在ScanNet [24] 上训练的MinkowskiNet [50] 实例。
		- 预测生成：我们通过使用“场景中有{category}。”的模板以及类别名称“{category}”本身，为数据集中包含的每个类别生成CLIP文本嵌入。接下来，我们平均这两个嵌入以获得每个特定类别的嵌入。我们通过计算所有物体节点嵌入与所有类别嵌入之间的余弦相似度来为每个物体节点获得预测标签，最后应用argmax操作符。在此之后，我们连接所有物体的点云以创建我们的预测点云Ppred，并将其转换到与具有地面真值（GT）语义标签的点云PGT相同的坐标框架中。鉴于预测点云可能与地面真值（GT）相比具有不同的点密度，我们遍历每个GT点，找到其在Ppred中的五个最近点，然后确定这些点中的多数标签作为每个GT点的预测标签。
		- 评估场景：为了保持一致性，我们评估了[14, 13]中评估的相同场景。对于ScanNet，我们评估的场景包括：scene0011_00, scene0050_00, scene0231_00, scene0378_00, scene0518_00。关于Replica，我们在office0-office4和room0-room2上进行评估。
		- 结果：Replica和ScanNet上的语义分割结果如表I所示。在mIOU和FmIOU方面，HOV-SG大幅度优于开放词汇基线。这主要是由于我们做出的以下改进：首先，当我们合并分段特征时，我们考虑了每个分段覆盖的所有点特征，并使用DBSCAN来获取主导特征，这比ConceptGraphs [14] 采用的均值方法提高了稳健性。其次，在生成点特征时，我们使用了掩码特征，它是子图像及其无上下文对应物的加权和。这减轻了显著背景物体的影响。进一步的定性结果在图6中给出。
	- ## B. 在Habitat 3D Semantics上的场景图评估
	  我们从四个方面评估我们的场景图。首先，为了分析场景图的几何精度，我们在第IV-B1节分析了楼层和类别无关的区域分割性能。其次，为了评估语义精度，我们评估了预测的区域语义（IV-B2节）以及开放词汇的物体级语义（IV-B3节）。为了审视HOV-SG的下游导航能力，我们在给定抽象语言查询的条件下进行了层次化物体检索和导航实验（IV-B4节）。我们在图7中展示了两个示例性构建的层次化3D场景图。其余场景的3D场景图可视化在图S.4中显示。
		- ### 数据集：
			- 为了评估所生成的场景图层次结构的各个方面，我们选择了Habitat-Semantics数据集（HM3DSem），因为它在跨多个楼层的场景中提供了真正的开放词汇标签，并提供了物体-区域分配。由于我们的方法在RGB-D帧上操作，我们手动记录了Habitat Semantics数据集[25]中8个场景的随机漫步，这些场景跨越多个房间和楼层：00824, 00829, 00843, 00861, 00862, 00873, 00877, 00890。为了构建用于比较的真实地图，我们结合了所有帧的RGB-D和全景数据，利用精确的里程计数据，获得RGB和全景全局地图。这些地图最终以0.02厘米的分辨率进行体素化。
		- ### 1) 楼层和类别无关的房间分割
			- 为了评估楼层和类别无关的房间分割，我们在数据集提供的元数据基础上确定了几种启发式方法。由于HM3DSem没有提供楼层高度标签，我们手动标注了所有上层和下层的楼层边界。此外，我们根据其关联的区域标签，汇集了我们构建的真实点云中包含的标注物体。基于此，我们获得了每个区域的点云，用作真实值。如表II所示，我们的方法在检索楼层数量方面达到了100%的准确率，无论是单层还是多层场景。此外，我们评估了HOV-SG的区域分割性能，并在HM3DSem的八个场景上与Hydra [19]进行了比较。我们观察到精度略低，但召回率显著提高，达到83.59%，比77.55%高。除了表II中给出的整体结果外，我们也在补充材料的表S.1中提供了场景具体的结果。一般来说，我们在包含较少区域的较小场景上获得了更高的精度和召回率。类似于Hydra [19]，我们的方法使用了一种简单的形态学启发式方法来分割区域，这种方法在处理更复杂、语义上模糊的房间布局（如厨房和客厅结合的空间）时效果不佳。然而，我们的方法不会太过受到这一缺点的困扰，因为我们为每个分割区域配备了10个代表性嵌入。这允许在不需要直接设定固定房间类别的情况下进行自适应提示。
		- ### 2) 语义房间分类
		  
		  我们对所提出的基于视图嵌入的房间类别标注方法（见图 4）进行了量化评估，并在 HM3DSem 数据集的八个场景中将其与两个强基线方法进行比较。两个基线方法均依赖对象标签来分类房间类别。为了进行公平比较，所有方法均依赖于真实值的房间分割结果，即每个房间的类别无关掩码。因此，所有对象根据真实值的房间布局被分配到相应房间。这与通用的 HOV-SG 方法不同，后者还需要估计房间掩码。
			- 数据集
			  
			  在此评估中，我们使用了一组封闭的房间类别集合。HM3DSem 数据集并未提供标注的房间类别，仅包含推测性投票，而这些投票通常不足以满足需求。因此，我们对 Sec. IV-B 中详细描述的八个场景的区域进行了手动标注。房间类别的具体列表详见 Sec. S.2-D。
			- 基线方法
			  我们将 HOV-SG 方法中使用的过滤视图嵌入来标注房间类别与两个基线方法进行比较：一个特权基线和一个非特权基线。
				- 特权基线：基于每个房间中包含的真实对象类别运行。为了获得房间标签，该基线通过提示一个大型语言模型（LLM，如 GPT-3.5 和 GPT-4 [43]）根据每个房间的对象类别，从封闭的房间类别集合中给出一个房间类别猜测，使用的是 few-shot 方法（提示的具体细节详见 Sec. S.2-D）。
				- 非特权基线：采用相同的提示 LLM 的原则，但仅能访问通过 HOV-SG 预测的对象类别。这意味着每个预测的对象都被标注为与对象特征相似度最高的类别。通常，由于 HOV-SG 的预测存在欠分割和过分割，预测对象的数量可能与特权基线不同。
				  相比之下，我们的视图嵌入方法依赖于 10 个不同的视图嵌入，这些嵌入与选定的房间类别集合进行相似度评分。最终预测的房间类别是所有视图嵌入中相似度最高的房间类别，如图 4 所述。
			- 评估指标
			  collapsed:: true
			  我们采用两种不同的评估标准：
				- **严格准确率（Acc=）**：评估预测的房间类别与真实类别在文本上是否完全一致，以促进结果的可复现性。
				- **近似准确率（Acc≈）**：通过人工评估得出，用于解决房间类别在标注时并非总能完全确定的问题。例如，厨房和客厅的混合区域。此外，由于大型语言模型（LLM）频繁出现幻觉，其提供的答案并非总是明确的类别。房间中对象数量较多时，这种现象会更加明显，尤其对处理欠分割问题的非特权基线更为不利。
				  
				  为了规避上述问题，我们手动筛选了所有八个场景的输出，并检查 LLM 是否倾向于正确的答案，例如预测出真实房间类别的同义词，这对基于 LLM 的方法的结果起到了提升作用。此外，我们还使用当前最先进的 LLM（GPT-4）对相同任务进行了评估，GPT-4 显著减少了幻觉现象，并提高了准确率。
			- 结果分析
				- 如表 III 所示，HOV-SG 的视图嵌入方法在严格准确率（Acc=）和近似准确率（Acc≈）方面均显著优于所有非特权基线。此外，HOV-SG 在依赖 GPT-3.5 的特权基线之上也取得了更好的表现，同时其近似准确率（84.10%）与 GPT-4（84.25%）接近。
				  
				  因此，我们得出结论：HOV-SG 的房间标注方法具有较高的鲁棒性，并在与可比的非特权方法的对比中表现出显著优势。此外，补充材料中的表 S.2 提供了逐场景的额外评估结果。
		- ### 3) 物体级语义评估
			- **背景问题**
			  
			  现有的开放词汇评估通常绕开了衡量真正开放词汇语义准确性的问题。这是由于以下因素：
			  被研究标签集的大小是任意的。
			  物体类别数量可能极其庞大且难以处理【25】。
			  现有评估协议【10, 13】的使用相对简单。
			  
			  虽然像 Amazon Mechanical Turk（AMT）这样的人类评估部分解决了该问题，但结果的稳健性、可复现性和可扩展性仍然是挑战【14】。
			- **评估指标**
			  
			  在本研究中，我们提出了新的 **AUC_top-k** 指标，用于量化预测类别与实际真实物体类别之间的前-k 准确率曲线下面积（见附图 S.2）。具体方法如下：
			  **计算余弦相似性排名**：预测物体特征与所有可能类别文本特征之间的余弦相似性排名。类别文本特征通过视觉语言模型（CLIP）编码。
			  **度量开集相似性**：指标反映在预测真实标签之前，平均需要多少错误选项。基于此，AUC_top-k 指标能够在处理大型、大小不一的标签集时量化实际的开放集相似性。
			  
			  我们在 HM3DSem 数据集的场景 00824 上可视化了 HOV-SG 的 AUC_top-k 曲线（见附图 S.2）。曲线越接近图的左上角，开放词汇相似性越高。相比仅显示特定 k 值的准确率，我们对 k 进行归一化处理，范围覆盖标签集的大小（HM3DSem 包含 1624 个类别）。这种归一化方式直观地展示了 AUC_top-k 指标如何可靠地评估大型但大小不一的标签集。
			- **基线方法**
			  为了展示 AUC_top-k 指标的适用性，我们在 HabitatSemantics 数据集【25】（包含 1624 个物体类别的大型标签集）上对 HOV-SG 与两个强基线 VLMaps【10】和 ConceptGraphs【14】进行了比较。为了公平比较，我们执行以下操作：
			  在预测物体和真实物体之间进行线性分配。
			  仅考虑与真实值的 IoU > 50% 的预测物体。
			  
			  由于 VLMaps【10】本身不预测物体掩码，因此其使用 HOV-SG 预测的物体掩码，并将所有掩码体素的特征平均作为物体特征。
			- ### **结果分析**
			  
			  表 IV 展示了整体 AUC_top-k 分数以及不同 top-k 阈值的表现。结果表明：
			  **VLMaps**【10】：表现较差，这可能是由于其稠密特征聚合的方式以及对微调视觉语言模型（LSeg【51】）的依赖，在具有挑战性的开放词汇场景中限制了其泛化能力。它在 top-5 预测类别中仅正确识别了 5% 的物体，且在 top-500 类别中仅在 40.02% 的情况下正确预测了类别。
			  **ConceptGraphs**【14】：取得了具有竞争力的 84.07% 分数。
			  **HOV-SG**：达到了 84.88%，尤其在 top-100 最高排名类别内，其表现优于 ConceptGraphs。
			  
			  总体而言，HOV-SG 在开放词汇语义评估中表现优异，特别是在处理大规模、多样化标签集时，展现了其优势。
		- ### 4) 分层概念检索
			- #### **方法概述**
			  
			  为了利用我们所提出表示方法的分层特性，我们评估了基于分层查询检索物体的能力，例如查询形式：“二楼客厅的枕头”或“厨房中的瓶子”。具体步骤如下：
				- **查询分解**
				  
				  使用 GPT-3.5 将查询分解为目标的分层概念，例如 `[floor 2, living room, pillow]` 或 `[-, kitchen, bottle]`，并计算其对应的 CLIP 嵌入。
				- **逐级查询**
				  
				  按以下顺序执行查询：
					- **楼层选择**：简单地确定最可能的楼层。
					- **房间选择**：根据查询房间与每个候选房间的十个嵌入的最大余弦相似性，选择最匹配的房间。相比均值或中值的计算方法，此方法平均成功率更高。
					- **物体选择**：在选定房间内检索最匹配的物体。
					  
					  检索性能结果展示于表 V。
			- #### **检索结果**
			  
			  我们将 HOV-SG 与增强版 ConceptGraphs【14】进行比较，该版本具备优先的楼层信息，能将物体与请求的房间及物体类别匹配，从而支持在楼层和房间级别上给出答案。
			  
			  结果如下：
				- 在 **物体-房间-楼层查询** 上，HOV-SG 的表现比 ConceptGraphs 提高了 **11.69%**。
				- 在 **物体-房间查询** 上，HOV-SG 相比 ConceptGraphs 提高了 **2.2%**。
				  
				  尽管 HOV-SG 因其设计上的缺陷（如房间分割错误）受到一定影响，但它在更大的场景和更详细的查询下依然表现出显著优势，而 ConceptGraphs 在此类条件下表现较差。详细信息请参阅附录 Sec. S.1-A。
			- #### **基于语言的模拟导航**
			  
			  除了一般的查询任务，我们还在 Habitat 模拟器中进行了导航实验。实验基于第 III-B 节中提出的多楼层导航 Voronoi 图，尝试访问符合查询的高概率检索物体，并报告实际导航成功率。
			  
			  **导航成功判定**：
			  
			  当机器人停在匹配的真实点云附近，且距离小于 1 米时，认为导航成功。
			  
			  **实验结果**：
				- 与检索成功率相比，导航成功率更高。这是因为机器人经常到达与目标物体实际位置接近的预测物体位置。
				- 使用欧几里得距离评估导航成功性，这一方法客观反映了机器人抵达的位置与目标物体实际位置的接近程度。
				- 不完美的实例分割掩码可能导致检索的物体段在几何和语义上无法完全覆盖真实物体，这种偏差可能使查询位置产生微小的偏移。
				  
				  表 V 中的数据显示了导航成功率的优势，同时图 8 展示了部分导航实验的例子。
		- ## C.基于语言的真实世界导航
		  
		  为了验证系统的实际效果，我们使用配备 Azure Kinect RGB-D 相机和 3D LiDAR 的波士顿动力 Spot 机器人。在一栋两层办公楼内，采集了包含丰富语义信息的 RGB-D 数据流，机器人穿越多个房间（如图 9 所示）。
		- ### **HOV-SG 在真实环境中的应用**
			- **传感器校准与位姿获取**
			  
			  我们校准了 LiDAR 和 RGB-D 相机之间的外参，并使用现成的 LiDAR SLAM 实现，结合 FAST-LIO2【52】和回环闭合模块获取 LiDAR 位姿。随后，通过外参计算相应的相机位姿。
			  
			  最后，利用 RGB-D 数据和里程计信息，按照第 III 节的方法构建 HOV-SG 表示。
			  
			  ---
		- ### **机器人导航：复杂查询任务**
			- **实验设置**
			  
			  在两层建筑中，我们设置了 41 个物体目标、9 个房间目标和 2 个楼层目标，使用自然语言查询已构建的 HOV-SG 表示。示例查询包括：
				- “前往楼层 0”；
				- “导航至楼层 1 的厨房”；
				- “找到楼层 0 办公室中的植物”。
				  
				  不同于以往仅检索物体目标的方法，HOV-SG 表示支持包含多个层次概念的复杂查询，能够实现更细化的约束性检索。
			- **检索任务评估**
			  
			  为了分离 HOV-SG 表示和导航系统的评估，我们首先定性地评估检索任务的准确性：
				- 根据建筑的明确结构，划分办公室、走廊、餐厅等房间与区域的边界。
				- 手动标注建筑中所有区域的类别，类别集包括办公室、走廊、厨房、研讨室、会议室、餐厅和卫生间。
				- 若 HOV-SG 表示返回正确的楼层和房间点云，并且目标物体点云与真实物体点云重叠，则认为检索成功。
			- **检索结果**
				- **楼层检索成功率**：100%。
				- **房间检索成功率**：55.6%。
				- **物体检索成功率**：70.7%。
				  
				  房间检索的主要失败原因是视觉上相似房间（如“会议室”、“研讨室”和“餐厅”）之间的混淆（见图 S.3）。
				  
				  ---
		- ### **导航实验**
			- **任务描述**
			  
			  利用语言指令，机器人导航到目标物体，并跨楼层导航至成功检索的房间和楼层。
			- **导航成功率**
				- 总体成功率为 56.1%。
				- 一个失败案例是查询“导航至二楼办公室的白板”，由于白板安装在隔墙上，机器人虽然到达了距离目标最近的位置，但因隔墙阻隔未能正确定位（见图 S.6）。
				- 为避免机器人在不稳定区域行驶，我们排除了楼梯段的目标位置。
				  
				  ---
		- ### **实验结果**
			- **检索与导航成功率**详见表 VI。
			- 图 S.6 显示了部分目标物体，补充材料图 S.5 展示了三次试验的详细过程。
			  
			  通过实验结果可以看出，HOV-SG 表示支持复杂的多层次查询和导航任务，尽管在视觉上相似的场景中存在一些挑战。
	- ## D. 表示存储开销评估
	  
	  HOV-SG 的一个关键优势在于其表示的紧凑性。我们将 VLMaps【10】、ConceptGraphs【14】和 HOV-SG 针对 HM3DSem 数据集中八个场景创建的表示存储大小进行比较，结果如表 VII 所示。
	- **VLMaps 存储设置**
	  
	  为适配评估，我们调整了 VLMaps，将 LSeg 特征存储在 3D 体素位置中，体素大小为 0.05m。LSeg 的骨干网络为 ViTB-32，生成 512 维特征。
	- **ConceptGraphs 和 HOV-SG 设置**
	  
	  两者均使用 ViT-H-14 CLIP 作为骨干网络，其表示中需要保存 1024 维特征。
	- **存储优化**
	  
	  VLMaps 优化后仅在靠近物体表面的体素处保存特征，而非在非占用体素处存储冗余特征。然而，由于其稠密存储结构，VLMaps 的存储需求显著高于 ConceptGraphs 和 HOV-SG。
	  
	  相比之下，ConceptGraphs 和 HOV-SG 利用紧凑的图结构大幅减少了存储开销。HOV-SG 平均将存储占用减少了 **75%**，与 VLMaps 相比具有显著优势。
	- **语义特性比较**
		- ConceptGraphs 编码了补充的物体关系信息。
		- HOV-SG 集成了层次化的语义特征。
		  
		  总体而言，ConceptGraphs 和 HOV-SG 作为互补表示，各自突出了场景语义的不同方面，为多样化任务提供了灵活支持。
	- ## E. 消融实验
	  
	  为了探讨我们方法中各关键组件的贡献，我们在 Replica 数据集【23】上进行了消融实验，结果如表 VIII 所示。
	- **3D 开放词汇分割级别映射的关键组件**
		- **DBSCAN 聚类**
		  
		  在每个分割区域中，我们对像素级 CLIP 嵌入应用 DBSCAN 聚类，从中选择最具代表性的特征。这种设计受到“多数表决”原则的启发，有效减轻了以下因素对语义准确性的不利影响：
			- CLIP 的固有局限性导致的异常特征；
			- SAM 输出中噪声的干扰。
			  
			  通过 DBSCAN 聚类，语义准确性得到了显著提升。
	- **CLIP 特征融合**
		- 我们的方法融合了从不同来源提取的 CLIP 特征：
			- 全局图像的 CLIP 特征；
			- 基于 SAM 掩码提取的目标遮罩图像的 CLIP 特征；
			- 基于 SAM 掩码但移除背景后的目标遮罩图像的 CLIP 特征。
		- 相比 ConceptGraphs【14】，其仅集成全局图像嵌入和子图像嵌入，我们假设将子图像中的显著特征融入 CLIP 嵌入能够提高语义准确性。
	- **实验设置**
	  
	  为验证上述假设，我们测试了三种配置：
		- **L-CLIP**：仅使用包含背景的目标遮罩的 CLIP 嵌入；
		- **M-CLIP**：仅使用移除背景的目标遮罩的 CLIP 嵌入；
		- **融合配置**：结合 L-CLIP 和 M-CLIP 嵌入，与 HOV-SG 中的处理方式一致。
	- **实验结果**
		- 我们的研究表明，融合两种嵌入的配置（HOV-SG 的方法）实现了最高的语义准确性，如表 VIII 所示。这表明，多特征来源的融合能够更全面地捕获目标的语义信息，从而显著提升模型性能。
- # 结论
  
  我们提出了 HOV-SG，这是一种新颖的分层开放词汇 3D 场景图表示，用于室内机器人导航。通过对环境进行语义分解（包括楼层、房间和物体），我们展示了从抽象语言查询中有效检索概念的能力，并实现了在真实世界中跨多层环境的长距离导航。
  
  通过在多个数据集上进行的广泛实验，我们表明 HOV-SG 在语义准确性、开放词汇能力和表示紧凑性方面均优于先前的基线方法。然而，HOV-SG 并非没有局限性。由于包含多个阶段和组件，我们的方法需要大量的超参数。此外，HOV-SG 的构建过程耗时，使其不适合实时映射。更进一步，它假设环境是静态的，因此无法处理动态环境。
  
  未来的研究方向可能包括开发动态环境的开放词汇表示，或整合一个具有反应能力的具身代理以增强物理世界中的推理和定位能力。为了促进后续研究，我们将代码公开，网址为 [https://hovsg.github.io](https://hovsg.github.io)。