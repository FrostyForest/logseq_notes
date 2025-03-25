- #crypto #freqtrade
- 下载数据
	- 下载之前的数据要加上--prepend
	- ```
	  freqtrade download-data --exchange binance --pairs  ETH/USDT:USDT -t 1h --timerange 20200101-
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
	  freqtrade hyperopt --config /home/linhai/code/freqtrade/user_data/config.json --hyperopt-loss OnlyProfitHyperOptLoss --strategy TrendStrategy -e 500 --spaces buy sell --timerange 20210301- --print-all --analyze-per-epoch
	  ```
- 回测命令行
	- ```
	  freqtrade backtesting --strategy EnvelopeStrategy --timeframe 1h --pairs ETH/USDT:USDT --timerange 20250101- --export trades --export-filename=backtest_samplestrategy.json --timeframe-detail 5m
	  ```
	- ```
	  freqtrade backtesting --strategy EnvelopeStrategy --timeframe 1h --pairs ETH/USDT:USDT --timerange 20250101-  --timeframe-detail 5m
	  ```
- 分析优化结果
	- ```
	  freqtrade hyperopt-list --profitable
	  ```
	-