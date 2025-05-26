- #pytorch #python
- 是的，**`torch.stack()` 会创建一个新的维度**。
  
  它的主要作用是将一系列具有相同形状的张量沿着一个新的维度堆叠起来。
  
  **工作原理：**
  
  1.  **输入**：`torch.stack()` 接收一个张量序列（例如，一个列表或元组的张量）。
  2.  **形状要求**：输入序列中的**所有张量都必须具有完全相同的形状**。
  3.  **`dim` 参数**：你通过 `dim` 参数指定新维度插入的位置。
  4.  **输出**：
      *   输出张量的维度会比输入张量的维度多一个。
      *   新创建的这个维度的大小等于输入序列中张量的数量。
      *   原始张量的数据会沿着这个新维度排列。
  
  **示例：**
  
  假设你有两个1D张量：
  ```python
  import torch
  
  t1 = torch.tensor([1, 2, 3])  # shape (3,)
  t2 = torch.tensor([4, 5, 6])  # shape (3,)
  ```
  
  *   **`torch.stack([t1, t2], dim=0)`**:
      *   新维度（大小为2，因为有两个张量）被插入到位置 `0`。
      *   输出形状：`(2, 3)`
      ```python
      stacked_dim0 = torch.stack([t1, t2], dim=0)
      print("Stacked with dim=0:\n", stacked_dim0)
      print("Shape:", stacked_dim0.shape)
      # 输出:
      # Stacked with dim=0:
      #  tensor([[1, 2, 3],
      #          [4, 5, 6]])
      # Shape: torch.Size([2, 3])
      ```
  
  *   **`torch.stack([t1, t2], dim=1)`**:
      *   新维度（大小为2）被插入到位置 `1`。
      *   输出形状：`(3, 2)`
      ```python
      stacked_dim1 = torch.stack([t1, t2], dim=1)
      print("\nStacked with dim=1:\n", stacked_dim1)
      print("Shape:", stacked_dim1.shape)
      # 输出:
      # Stacked with dim=1:
      #  tensor([[1, 4],
      #          [2, 5],
      #          [3, 6]])
      # Shape: torch.Size([3, 2])
      ```
  
  再看一个2D张量的例子：
  ```python
  t3 = torch.tensor([[1, 2], [3, 4]]) # shape (2, 2)
  t4 = torch.tensor([[5, 6], [7, 8]]) # shape (2, 2)
  
  # Stack along a new dimension 0
  stacked_2d_dim0 = torch.stack([t3, t4], dim=0)
  print("\n2D tensors stacked with dim=0:\n", stacked_2d_dim0)
  print("Shape:", stacked_2d_dim0.shape) # Shape: torch.Size([2, 2, 2])
  # 第一个2代表堆叠了2个张量，后面两个2是原始张量的形状
  
  # Stack along a new dimension 1
  stacked_2d_dim1 = torch.stack([t3, t4], dim=1)
  print("\n2D tensors stacked with dim=1:\n", stacked_2d_dim1)
  print("Shape:", stacked_2d_dim1.shape) # Shape: torch.Size([2, 2, 2])
  # 第一个2是原始张量的第一个维度，中间的2代表堆叠了2个张量，最后的2是原始张量的第二个维度
  
  # Stack along a new dimension 2 (or -1)
  stacked_2d_dim2 = torch.stack([t3, t4], dim=2)
  print("\n2D tensors stacked with dim=2:\n", stacked_2d_dim2)
  print("Shape:", stacked_2d_dim2.shape) # Shape: torch.Size([2, 2, 2])
  # 前两个2是原始张量的维度，最后的2代表堆叠了2个张量
  ```
  
  **与 `torch.cat()` 的区别：**
  
  *   **`torch.stack()`**: 创建一个**新**的维度，然后沿着这个新维度堆叠张量。输入张量必须形状完全相同。
  *   **`torch.cat()`**: 沿着一个**已存在**的维度连接（拼接）张量。它不会创建新维度，而是扩展指定维度的大小。输入张量在非连接维度上必须形状相同。
  
  因此，回答你的问题：**是的，`torch.stack()` 的核心功能之一就是创建一个新的维度来组织输入张量。**