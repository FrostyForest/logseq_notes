- #ubuntu #linux
- 快速复制当前时间
	- ```
	  date +%Y-%m-%d_%H:%M:%S | xsel -i -b
	  ```
- ## 代理
	- ```
	  export HTTP_PROXY="http://127.0.0.1:7890"
	  export HTTPS_PROXY="http://127.0.0.1:7890"
	  ```
- ## 禁止休眠
	- ### 查看休眠模式情况
		- ```
		  systemctl status sleep.target
		  ```
	- ### 使用gnome-tweak管理
		- ```sudo apt install gnome-tweak-tool
		  sudo apt install gnome-tweaks
		  ```
	- ### 使用gnome-tweaks禁止休眠
		- ```
		  gnome-tweaks
		  ```
		- 会弹出一个窗口，将“笔记本电脑盖子关闭时挂起”选项取消选择