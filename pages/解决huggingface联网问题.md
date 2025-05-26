- #python #huggingface
- ## 找到文件夹huggingface_hub
	- 结果
		- /home/linhai/.local/lib/python3.10/site-packages/huggingface_hub
		  /home/linhai/app/miniconda3/envs/stock/lib/python3.10/site-packages/huggingface_hub
		  /home/linhai/app/miniconda3/lib/python3.11/site-packages/huggingface_hub
		  /usr/local/lib/python3.10/dist-packages/huggingface_hub
	- ```
	  sudo find / -name huggingface_hub
	  ```
- ## 修改
	- 打开 huggingface_hub 目录下的 constants.py 文件，将其中_HF_DEFAULT_ENDPOINT 的 huggingface.co替换为 hf-mirror.com
-