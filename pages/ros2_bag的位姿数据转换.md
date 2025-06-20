- #ros2 #isaacsim
- ## ros2 topic数据格式
	- 世界坐标系+位置+四元数
- ## 转为SE(3)
	- ```
	  import numpy as np
	  
	  def pose_from_translation_quaternion(translation, quaternion):
	      """
	      从平移向量和四元数构建一个4x4的世界坐标系位姿矩阵 (c2w)。
	  
	      参数:
	      translation (np.ndarray): 形状为 (3,) 的平移向量 [tx, ty, tz]。
	      quaternion (np.ndarray): 形状为 (4,) 的四元数 [w, x, y, z]。
	  
	      返回:
	      np.ndarray: 形状为 (4, 4) 的位姿矩阵。
	      """
	      # 1. 确保四元数是单位四元数
	      norm = np.linalg.norm(quaternion)
	      if not np.isclose(norm, 1.0):
	          print("Warning: Quaternion is not normalized. Normalizing it now.")
	          quaternion = quaternion / norm
	  
	      w, x, y, z = quaternion
	  
	      # 2. 从四元数计算3x3旋转矩阵
	      # https://en.wikipedia.org/wiki/Rotation_matrix#Quaternion
	      R = np.array([
	          [1 - 2*(y**2 + z**2),   2*(x*y - w*z),       2*(x*z + w*y)],
	          [2*(x*y + w*z),       1 - 2*(x**2 + z**2),   2*(y*z - w*x)],
	          [2*(x*z - w*y),       2*(y*z + w*x),       1 - 2*(x**2 + y**2)]
	      ])
	  
	      # 3. 构建4x4位姿矩阵
	      pose_matrix = np.identity(4)  # 创建一个4x4的单位矩阵
	      pose_matrix[:3, :3] = R       # 将旋转部分填入
	      pose_matrix[:3, 3] = translation # 将平移部分填入
	  
	      return pose_matrix
	  
	  # --- 示例 ---
	  # 假设有一个物体/相机
	  # 它的位置在世界坐标系的 (1, 2, 3)
	  t_vec = np.array([1.0, 2.0, 3.0])
	  
	  # 它的姿态是绕Z轴旋转90度
	  # 对应的四元数是 (cos(45), 0, 0, sin(45)) -> (sqrt(2)/2, 0, 0, sqrt(2)/2)
	  q_vec = np.array([np.sqrt(2)/2, 0.0, 0.0, np.sqrt(2)/2])
	  
	  # 构建位姿矩阵
	  c2w_pose = pose_from_translation_quaternion(t_vec, q_vec)
	  
	  print("Translation Vector:\n", t_vec)
	  print("\nQuaternion:\n", q_vec)
	  print("\nResulting 4x4 Pose Matrix (c2w):\n", c2w_pose)
	  
	  # 验证旋转矩阵是否正确 (绕Z轴旋转90度)
	  # 期望结果:
	  # [[ 0, -1,  0],
	  #  [ 1,  0,  0],
	  #  [ 0,  0,  1]]
	  # 输出结果与期望一致（由于浮点数精度问题可能非常接近0）
	  # [[ 0. -1.  0.]
	  #  [ 1.  0.  0.]
	  #  [ 0.  0.  1.]]
	  ```