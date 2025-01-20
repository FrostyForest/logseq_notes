- #crypto #freqtrade
- 下载数据
	- ```
	  freqtrade download-data --exchange binance --pairs  ETH/USDT:USDT -t 1h --prepend --timerange 20200101-
	  ```
- 查看下载的数据的时间范围
	- ```
	  freqtrade list-data --exchange binance --pairs  ETH/USDT:USDT --show-timerange
	  ```
- 展示已下载的数据列表
	- ```
	  freqtrade list-data --userdir /home/linhai/code/freqtrade/user_data
	  ```
- 启用webui来回测
	- ```
	  freqtrade webserver
	  ```
- 优化
	- ```
	  freqtrade hyperopt --config /home/linhai/code/freqtrade/user_data/config.json --hyperopt-loss SharpeHyperOptLoss --strategy EnvelopeStrategy -e 200 --spaces buy sell
	  ```
- 回测命令行
	- ```
	  freqtrade backtesting --strategy EnvelopeStrategy --timeframe 1h --pairs ETH/USDT:USDT --timerange 20240311- --export trades --export-filename=backtest_samplestrategy.json --timeframe-detail 1m
	  ```