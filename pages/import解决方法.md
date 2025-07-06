- #python #bug
- ##
- ## 在代码中动态修改  sys.path
	- ```
	  import sys
	  import os
	  
	  # 获取当前文件 (torch_cube_franka_ppo2.py) 的绝对路径
	  current_file_path = os.path.abspath(__file__)
	  # 获取当前文件所在的目录 (train/)
	  current_dir = os.path.dirname(current_file_path)
	  # 获取项目根目录 (my_code/)，即 train/ 的父目录
	  project_root = os.path.dirname(current_dir)
	  # 将项目根目录添加到 sys.path 的最前面
	  sys.path.insert(0, project_root)
	  ```
	- ### 原理
		- 假设当前要执行的代码在目录a/test.py,python只会搜索当前所在目录的文件，同时该目录之内的文件夹需要加上`__init__.py`，该文件夹内的文件才能被当作包来import
		- 如果要引用的代码在另一个目录b/，且a与b为同级，根目录都为c,则可以通过上述代码解决