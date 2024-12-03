- #github
- 为什么要进行身份验证配置？
	- 为了在每次push时候不需要反复输入用户名和密码
- 如何进行身份验证配置
	- 现阶段一般是使用[[SSH Key]]作为身份验证方式
	- 生成[[SSH Key]]
		- ```
		  ssh-keygen -t ed25519 -C "your_email@example.com"
		  ```
		- 默认保存为 `~/.ssh/id_ed25519`，`~`代表[[用户的主目录]]
	- 添加SSH Key到SSH-Agent
		- ```
		  eval "$(ssh-agent -s)"
		  ssh-add ~/.ssh/id_ed25519
		  ```
	- 将SSH Key 添加到 GitHub
		- 复制公钥内容
			- ```
			  cat ~/.ssh/id_ed25519.pub
			  ```
		- 在个人github的[SSH and GPG keys](https://github.com/settings/keys) 页面添加公钥
	- 替换远程url
		- 将仓库url从[[HTTPS]]替换为 [[SSH]]
		  ```
		  git remote set-url origin git@github.com:YourUsername/YourRepository.git
		  ```
-