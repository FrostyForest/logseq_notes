- #isaaclab #[[direct rl env运行逻辑]]
- get_env_local_pose函数
	- *env_pos*:环境局部坐标系原点在世界坐标系的位置
	- 返回：将世界坐标系中的位置转换到环境局部坐标系
- 矩阵的逆如何理解
	- **矩阵的逆：** 矩阵的逆概念与之类似。对于一个 **方阵** (行数和列数相等的矩阵) A，如果存在另一个方阵 A⁻¹，使得它们的乘积等于 **单位矩阵** I，那么 A⁻¹ 就被称为 A 的逆矩阵。  单位矩阵 I 在矩阵乘法中扮演的角色类似于数字 1 在普通乘法中的角色。
	- 已知环境坐标系中的两点a和b,要想求b在以a为原点的坐标系中的位置，需要得到由环境坐标系到a坐标系的变换，该变换如何得到？对a在环境坐标系中的位置矩阵求逆
	-
- panda_link7的环境坐标
  ```
          hand_pose = get_env_local_pose(
              self.scene.env_origins[0],
              UsdGeom.Xformable(stage.GetPrimAtPath("/World/envs/env_0/Robot/panda_link7")),
              self.device,
          )
  ```
- panda_link7的世界坐标
  ```
  self.hand_link_idx = self._robot.find_bodies("panda_link7")[0][0]
  hand_pos = self._robot.data.body_pos_w[env_ids, self.hand_link_idx]
  ```
- hand_pose_inv_rot, hand_pose_inv_pos = tf_inverse(hand_pose[3:7], hand_pose[0:3])
	- 这一步得到了从手部坐标系到环境局部坐标系的变换
-