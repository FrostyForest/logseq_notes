- #isaaclab #skrl #reinforcement_learning
- https://skrl.readthedocs.io/en/latest/api/utils/model_instantiators.html
- 当然，这是对提供的文本的中文翻译：
  
  **模型实例化器**
  
  用于快速创建模型实例的实用工具。
  
  **模型**
  
  *   **pytorch**
  *   **jax**
  
  *   **表格模型** (离散域)
  *   **分类模型** (离散域)
  *   **高斯模型** (连续域)
  *   **多元高斯模型** (连续域)
  *   **确定性模型** (连续域)
  *   **共享模型**
  
  **网络定义**
  
  网络由一个或多个容器组成。对于每个容器，都指定了其输入、隐藏层和激活函数。
  
  **实现细节:**
  
  *   网络的计算/前向传播是通过按照容器定义的顺序调用容器来完成的
  *   容器在 PyTorch 中使用 `torch.nn.Sequential`，在 JAX 中使用 `flax.linen.Sequential`
  *   如果指定了单个激活函数（映射或序列），它将应用于容器中每个层之后（展平层除外）
  
  ```yaml
  network:
    - name: <名称>  # 容器名称
      input: <输入>  # 容器输入（支持某些操作）
      layers:  # 支持的层列表
        - <层 1>
        - ...
        - <层 N>
      activations:  # 支持的激活函数列表
        - <激活函数 1>
        - ...
        - <激活函数 N>
  ```
  
  **输入**
  
  输入可以使用令牌或先前定义的容器输出（通过容器名称）来指定。可以在其上指定某些操作，包括索引和切片。
  
  **提示**
  
  操作可以混合使用以创建复杂的输入语句
  
  **可用令牌:**
  
  *   `STATES`: 令牌，指示转发到模型的输入状态 (`inputs["states"]`)
  *   `ACTIONS`: 令牌，指示转发到模型的输入动作 (`inputs["taken_actions"]`)
  *   `STATES_ACTIONS`: 令牌，指示转发的输入状态和动作的连接
  
  **支持的操作:**
  
  **操作** | **示例**
  ------- | --------
  **张量/数组索引和切片**<br>例如：`gymnasium.spaces.Box` | `STATES[:, 0]`<br>`STATES[:, 2:5]`
  **字典按键索引**<br>例如：`gymnasium.spaces.Dict` | `STATES["joint-pos"]`
  **算术运算 (+, -, *, /)** | `features_extractor + ACTIONS`
  **连接** | `concatenate([features_extractor, ACTIONS])`
  **置换维度** | `permute(STATES, (0, 3, 1, 2))`
  
  **输出**
  
  输出可以使用令牌或定义的容器输出（通过容器名称）来指定。可以在其上指定某些操作。
  
  **注意**
  
  如果使用令牌，则将创建一个线性层，其输入特征数量为列表中最后一个容器的输出特征数量，输出特征数量为令牌表示的值。
  
  **提示**
  
  操作可以混合使用以创建复杂的输出语句
  
  **可用令牌:**
  
  *   `ACTIONS`: 令牌，指示输出形状为动作空间中的元素数量
  *   `ONE`: 令牌，指示输出形状为 1
  
  **支持的操作:**
  
  **操作** | **示例**
  ------- | --------
  **激活函数** | `tanh(ACTIONS)`
  **算术运算 (+, -, *, /)** | `features_extractor + ONE`
  **连接** | `concatenate([features_extractor, net])`
  
  **激活函数**
  
  下表列出了支持的激活函数：
  
  **激活函数** | **pytorch** | **jax**
  ------- | -------- | --------
  **relu** | `torch.nn.ReLU` | `flax.linen.activation.relu`
  **tanh** | `torch.nn.Tanh` | `flax.linen.activation.tanh`
  **sigmoid** | `torch.nn.Sigmoid` | `flax.linen.activation.sigmoid`
  **leaky_relu** | `torch.nn.LeakyReLU` | `flax.linen.activation.leaky_relu`
  **elu** | `torch.nn.ELU` | `flax.linen.activation.elu`
  **softplus** | `torch.nn.Softplus` | `flax.linen.activation.softplus`
  **softsign** | `torch.nn.Softsign` | `flax.linen.activation.soft_sign`
  **selu** | `torch.nn.SELU` | `flax.linen.activation.selu`
  **softmax** | `torch.nn.Softmax` | `flax.linen.activation.softmax`
  
  **层**
  
  下表列出了支持的层和变换：
  
  **层** | **pytorch** | **jax**
  ------- | -------- | --------
  **linear** | `torch.nn.Linear` | `flax.linen.Dense`
  **conv2d** | `torch.nn.Conv2d` | `flax.linen.Conv`
  **flatten** | `torch.nn.Flatten` | `jax.numpy.reshape`
  
  **linear**
  
  应用线性变换 (`torch.nn.Linear` 在 PyTorch 中, `flax.linen.Dense` 在 JAX 中)
  
  **注意**
  
  令牌 `STATES` (观察/状态空间中的元素数量)、`ACTIONS` (动作空间中的元素数量)、`STATES_ACTIONS` (观察/状态空间的元素数量和动作空间的元素数量之和) 和 `ONE` (1) 可以用作层的输入/输出特征数量。
  
  **注意**
  
  如果未指定 PyTorch 的 `in_features` 参数，则将使用 `torch.nn.LazyLinear` 模块推断。
  
  **pytorch** | **jax** | **类型** | **必需** | **描述**
  ------- | -------- | -------- | -------- | --------
  `in_features` | - | `int` | - | 输入特征数量
  `0` | `features` | `int` | 是 | 输出特征数量
  `1` | `use_bias` | `bool` | 是 | 是否添加偏置
  
  ```yaml
  layers:
    - 32
  ```
  
  **conv2d**
  
  应用 2D 卷积 (`torch.nn.Conv2d` 在 PyTorch 中, `flax.linen.Conv` 在 JAX 中)
  
  **警告**
  
  *   PyTorch 的 `torch.nn.Conv2d` 期望输入格式为 NCHW (N: 批次, C: 通道, H: 高度, W: 宽度)。可能需要进行置换操作来修改通常为 NHWC 的图像批次的维度。
  *   JAX 的 `flax.linen.Conv` 期望输入格式为 NHWC (图像批次的典型维度)。
  
  **注意**
  
  如果未指定 PyTorch 的 `in_channels` 参数，则将使用 `torch.nn.LazyConv2d` 模块推断。
  
  **pytorch** | **jax** | **类型** | **必需** | **描述**
  ------- | -------- | -------- | -------- | --------
  `in_channels` | - | `int` | - | 输入通道数量
  `0` | `features` | `int` | 是 | 输出通道数量（滤波器数量）
  `1` | `kernel_size` | `int`, `tuple[int]` | 是 | 卷积核大小
  `2` | `strides` | `int`, `tuple[int]` | 是 | 窗口间步幅
  `3` | `padding` | `str`, `int`, `tuple[int]` | 是 | 添加到所有维度的填充
  `4` | `use_bias` | `bool` | 是 | 是否添加偏置
  
  ```yaml
  layers:
    - conv2d: [32, 8, [4, 4]]
  ```
  
  **flatten**
  
  展平连续的维度范围 (`torch.nn.Flatten` 在 PyTorch 中, `jax.numpy.reshape` 操作在 JAX 中)
  
  **pytorch** | **jax** | **类型** | **必需** | **描述**
  ------- | -------- | -------- | -------- | --------
  `0` | `start_dim` | `int` | - | 要展平的第一个维度
  `1` | `end_dim` | `int` | - | 要展平的最后一个维度
  
  ```yaml
  layers:
    - flatten
  ```
  
  **API (PyTorch)**
  
  `skrl.utils.model_instantiators.torch.categorical_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | torch.device | None = None, unnormalized_log_prob: bool = True, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个分类模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `torch.device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `unnormalized_log_prob` (`bool`, 可选) – 标志，指示如何解释模型的输出 (默认值: `True`)。如果为 `True`，则模型的输出被解释为未归一化的对数概率（可以是任何实数），否则为归一化概率（输出必须是非负、有限且具有非零和）。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   分类模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  `skrl.utils.model_instantiators.torch.deterministic_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | torch.device | None = None, clip_actions: bool = False, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个确定性模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `torch.device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `clip_actions` (`bool`, 可选) – 标志，指示是否应裁剪动作 (默认值: `False`)。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   确定性模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  `skrl.utils.model_instantiators.torch.gaussian_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | torch.device | None = None, clip_actions: bool = False, clip_log_std: bool = True, min_log_std: float = -20, max_log_std: float = 2, reduction: str = 'sum', initial_log_std: float = 0, fixed_log_std: bool = False, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个高斯模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `torch.device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `clip_actions` (`bool`, 可选) – 标志，指示是否应裁剪动作 (默认值: `False`)。
  
      *   `clip_log_std` (`bool`, 可选) – 标志，指示是否应裁剪对数标准差 (默认值: `True`)。
  
      *   `min_log_std` (`float`, 可选) – 对数标准差的最小值 (默认值: `-20`)。
  
      *   `max_log_std` (`float`, 可选) – 对数标准差的最大值 (默认值: `2`)。
  
      *   `reduction` (`str`, 可选) – 用于返回对数概率密度函数的缩减方法：(默认值: `"sum"`)。支持的值为 `"mean"`, `"sum"`, `"prod"` 和 `"none"`。如果为 `"none"`，则对数概率密度函数将作为形状为 `(num_samples, num_actions)` 而不是 `(num_samples, 1)` 的张量返回。
  
      *   `initial_log_std` (`float`, 可选) – 对数标准差的初始值 (默认值: `0`)。
  
      *   `fixed_log_std` (`bool`, 可选) – 对数标准差参数是否应固定 (默认值: `False`)。固定的参数将禁用梯度计算。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   高斯模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  `skrl.utils.model_instantiators.torch.multivariate_gaussian_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | torch.device | None = None, clip_actions: bool = False, clip_log_std: bool = True, min_log_std: float = -20, max_log_std: float = 2, initial_log_std: float = 0, fixed_log_std: bool = False, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个多元高斯模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `torch.device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `clip_actions` (`bool`, 可选) – 标志，指示是否应裁剪动作 (默认值: `False`)。
  
      *   `clip_log_std` (`bool`, 可选) – 标志，指示是否应裁剪对数标准差 (默认值: `True`)。
  
      *   `min_log_std` (`float`, 可选) – 对数标准差的最小值 (默认值: `-20`)。
  
      *   `max_log_std` (`float`, 可选) – 对数标准差的最大值 (默认值: `2`)。
  
      *   `initial_log_std` (`float`, 可选) – 对数标准差的初始值 (默认值: `0`)。
  
      *   `fixed_log_std` (`bool`, 可选) – 对数标准差参数是否应固定 (默认值: `False`)。固定的参数将禁用梯度计算。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   多元高斯模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  `skrl.utils.model_instantiators.torch.shared_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | torch.device | None = None, structure: Sequence[str] = ['GaussianMixin', 'DeterministicMixin'], roles: Sequence[str] = [], parameters: Sequence[Mapping[str, Any]] = [], single_forward_pass: bool = True, return_source: bool = False) → Model | str`
  
  *   实例化一个共享模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `torch.device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `structure` (`sequence` of `string`, 可选) – 共享模型结构 (默认值: Gaussian-Deterministic)。
  
      *   `roles` (`sequence` of `string`, 可选) – 模型角色的组织列表 (默认值: `[]`)。
  
      *   `parameters` (`sequence` of `dict`, 可选) – 模型实例化器参数的组织列表 (默认值: `[]`)。
  
      *   `single_forward_pass` (`bool`) – 是否为共享层/网络执行单次前向传播 (默认值: `True`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   共享模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  **API (JAX)**
  
  `skrl.utils.model_instantiators.jax.categorical_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | jax.Device | None = None, unnormalized_log_prob: bool = True, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个分类模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `jax.Device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `unnormalized_log_prob` (`bool`, 可选) – 标志，指示如何解释模型的输出 (默认值: `True`)。如果为 `True`，则模型的输出被解释为未归一化的对数概率（可以是任何实数），否则为归一化概率（输出必须是非负、有限且具有非零和）。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   分类模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  `skrl.utils.model_instantiators.jax.deterministic_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | jax.Device | None = None, clip_actions: bool = False, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个确定性模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `jax.Device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `clip_actions` (`bool`, 可选) – 标志，指示是否应裁剪动作 (默认值: `False`)。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   确定性模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  `skrl.utils.model_instantiators.jax.gaussian_model(observation_space: int | Tuple[int] | gymnasium.Space | None = None, action_space: int | Tuple[int] | gymnasium.Space | None = None, device: str | jax.Device | None = None, clip_actions: bool = False, clip_log_std: bool = True, min_log_std: float = -20, max_log_std: float = 2, reduction: str = 'sum', initial_log_std: float = 0, fixed_log_std: bool = False, network: Sequence[Mapping[str, Any]] = [], output: str | Sequence[str] = '', return_source: bool = False, *args, **kwargs) → Model | str`
  
  *   实例化一个高斯模型
  
      **参数:**
  
      *   `observation_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 观察/状态空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_observations` 属性将包含该空间的大小。
  
      *   `action_space` (`int`, `tuple` 或 `list` of `int`, `gymnasium.Space` 或 `None`, 可选) – 动作空间或形状 (默认值: `None`)。如果不是 `None`，则 `num_actions` 属性将包含该空间的大小。
  
      *   `device` (`str` 或 `jax.Device`, 可选) – 张量/数组将被分配到的设备 (默认值: `None`)。如果为 `None`，设备将是 "cuda" (如果可用) 或 "cpu"。
  
      *   `clip_actions` (`bool`, 可选) – 标志，指示是否应裁剪动作 (默认值: `False`)。
  
      *   `clip_log_std` (`bool`, 可选) – 标志，指示是否应裁剪对数标准差 (默认值: `True`)。
  
      *   `min_log_std` (`float`, 可选) – 对数标准差的最小值 (默认值: `-20`)。
  
      *   `max_log_std` (`float`, 可选) – 对数标准差的最大值 (默认值: `2`)。
  
      *   `reduction` (`str`, 可选) – 用于返回对数概率密度函数的缩减方法：(默认值: `"sum"`)。支持的值为 `"mean"`, `"sum"`, `"prod"` 和 `"none"`。如果为 `"none"`，则对数概率密度函数将作为形状为 `(num_samples, num_actions)` 而不是 `(num_samples, 1)` 的张量返回。
  
      *   `initial_log_std` (`float`, 可选) – 对数标准差的初始值 (默认值: `0`)。
  
      *   `fixed_log_std` (`bool`, 可选) – 对数标准差参数是否应固定 (默认值: `False`)。固定的参数将从模型参数中排除。
  
      *   `network` (`list` of `dict`, 可选) – 网络定义 (默认值: `[]`)。
  
      *   `output` (`list` 或 `str`, 可选) – 输出表达式 (默认值: `""`)。
  
      *   `return_source` (`bool`, 可选) – 是否返回包含用于实例化模型的模型类源代码字符串，而不是模型实例 (默认值: `False`)。
  
      **返回:**
  
      *   高斯模型实例或定义源代码
  
      **返回类型:**
  
      *   `Model`
  
  ---
  
  **总结:**
  
  这份文档描述了一个名为 "模型实例化器" 的工具，用于在 PyTorch 和 JAX 框架中快速创建各种类型的强化学习模型。 它提供了以下关键信息：
  
  *   **支持的模型类型**:  列出了表格模型、分类模型、高斯模型、多元高斯模型、确定性模型和共享模型，并区分了离散域和连续域模型。
  *   **网络定义**:  详细介绍了如何使用 YAML 风格的配置来定义神经网络结构，包括容器的概念、层、激活函数以及输入/输出的配置。
  *   **输入和输出配置**:  解释了如何使用令牌 (例如 `STATES`, `ACTIONS`, `ONE`) 和各种操作 (索引、切片、算术运算、连接、维度置换) 来灵活地定义模型的输入和输出。
  *   **激活函数和层**:  提供了支持的激活函数和层类型的表格，并分别列出了在 PyTorch 和 JAX 中的对应实现方式。  对于 `linear` 和 `conv2d` 层，还详细说明了参数和使用注意事项。
  *   **API 文档**:  提供了 PyTorch 和 JAX 版本的 `categorical_model`, `deterministic_model`, `gaussian_model`, `multivariate_gaussian_model`, 和 `shared_model` 函数的 API 文档，包括参数说明和返回值类型。
  
  总而言之，这份文档是 skrl 库中模型实例化器模块的详细使用指南，旨在帮助用户快速上手并灵活配置各种强化学习模型。
-