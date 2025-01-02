- #crypto #freqtrade
- 下载数据
	- ```
	  freqtrade download-data --exchange binance --pairs  ETH/USDT:USDT -t 1h --prepend --timerange 20200101-
	  ```
- 查看下载的数据的时间范围
	- ```
	  freqtrade list-data --exchange binance --pairs  ETH/USDT:USDT --show-timerange
	  ```
- 启用webui来回测
	- ```
	  freqtrade webserver
	  ```