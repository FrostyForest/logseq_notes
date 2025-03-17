- #isaaclab #gym #reinforcement_learning
- ### Gym/Gymnasium 中的 Space 详解
- #### 1. 概念定义
  
  Gym/Gymnasium 中的 `Space` 是强化学习环境的核心组件，用于**形式化描述环境的状态和动作的数学空间**。它定义了：
- 观测空间（Observation Space）：环境返回的状态/观测的可能取值范围
- 动作空间（Action Space）：智能体可以执行的动作的可能取值范围
- #### 2. 主要空间类型
  
  以下是常见的空间类型及其特点：
  
  | 空间类型 | 数学描述 | 典型应用场景 | 示例代码 |
  | ---- | ---- | ---- |
  | ​**Box** | 连续多维空间 | 机器人关节控制、物理模拟 | `Box(low=-1, high=1, shape=(3,))` |
  | ​**Discrete** | 离散整数空间 | 简单游戏动作选择 | `Discrete(4)` → {0,1,2,3} |
  | ​**MultiDiscrete** | 多维离散组合 | 多关节离散控制 | `MultiDiscrete([3, 2])` → [0-2, 0-1] |
  | ​**MultiBinary** | 二进制向量空间 | 开关组合决策 | `MultiBinary(5)` → 5位二进制 |
  | ​**Dict** | 复合空间（键值对） | 多模态观测（图像+传感器） | `Dict({"image": Box(...), "sensor": Box(...)})` |
  | ​**Tuple** | 复合空间（有序元组） | 时序观测组合 | `Tuple((Box(...), Discrete(...)))` |
-