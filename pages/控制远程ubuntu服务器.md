- #linux #freqtrade #ubuntu
- 常用路径
	- sftp://ubuntu@152.32.144.141
	- /home/ubuntu/ft_userdata/user_data
	- http://152.32.144.141:8080
- 常用命令
	- 展示当前路径
		- pwd
	- 展示子文件
		- ls
	- **使用 SFTP**传送文件
		- ```
		  put /home/linhai/code/freqtrade/user_data/strategies/my_strategy.py /home/ubuntu/ft_userdata/user_data/strategies/
		  ```
	- **从服务器下载**
		- 直接使用ubuntu自带文件管理器
	- 启动docker
		- ```
		  docker compose up -d
		  ```
		- ```
		  docker compose down
		  ```
-