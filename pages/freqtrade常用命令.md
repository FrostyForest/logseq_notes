- #crypto #freqtrade
- 下载数据
	- 下载之前的数据要加上--prepend
	- ```
	  freqtrade download-data --exchange bybit --pairs "BTC/USDT:USDT" "XRP/USDT:USDT" "DOGE/USDT:USDT" "SOL/USDT:USDT" "ADA/USDT:USDT" "AVAX/USDT:USDT" "DOT/USDT:USDT" "XLM/USDT:USDT" "HBAR/USDT:USDT" "FIL/USDT:USDT" "MKR/USDT:USDT" "CAKE/USDT:USDT" "VET/USDT:USDT" "ALGO/USDT:USDT" "SUI/USDT:USDT" "WLD/USDT:USDT" "GRT/USDT:USDT" "STX/USDT:USDT" "INJ/USDT:USDT" "CRV/USDT:USDT" "SHIB1000/USDT:USDT" "RSR/USDT:USDT" -t 1h --timerange 20220901- --prepend
	  ```
	- ```
	  freqtrade download-data --exchange binance --pairs BTC/USDT:USDT XRP/USDT:USDT DOGE/USDT:USDT SOL/USDT:USDT ADA/USDT:USDT AVAX/USDT:USDT DOT/USDT:USDT XLM/USDT:USDT HBAR/USDT:USDT FIL/USDT:USDT MKR/USDT:USDT CAKE/USDT:USDT VET/USDT:USDT ALGO/USDT:USDT SUI/USDT:USDT WLD/USDT:USDT GRT/USDT:USDT STX/USDT:USDT INJ/USDT:USDT CRV/USDT:USDT 1000SHIB/USDT:USDT RSR/USDT:USDT ICP/USDT:USDT S/USDT:USDT MEW/USDT:USDT W/USDT:USDT AI16Z/USDT:USDT POL/USDT:USDT JUP/USDT:USDT AKT/USDT:USDT AERO/USDT:USDT NEAR/USDT:USDT APE/USDT:USDT BCH/USDT:USDT -t 4h --timerange 20220901- --prepend
	  ```
	- ```
	  freqtrade download-data --exchange bybit --pairs SUI/USDT:USDT ALPACA/USDT:USDT 1000PEPE/USDT:USDT WLD/USDT:USDT NKN/USDT:USDT SEI/USDT:USDT TON/USDT:USDT ARB/USDT:USDT TIA/USDT:USDT ORDI/USDT:USDT MEME/USDT:USDT ARK/USDT:USDT LEVER/USDT:USDT HIFI/USDT:USDT KAS/USDT:USDT BIGTIME/USDT:USDT PENDLE/USDT:USDT 1000FLOKI/USDT:USDT PERP/USDT:USDT GAS/USDT:USDT ARKM/USDT:USDT AUCTION/USDT:USDT 1000BONK/USDT:USDT AAVE/USDT:USDT ADA/USDT:USDT ALGO/USDT:USDT APE/USDT:USDT APT/USDT:USDT ATOM/USDT:USDT AVAX/USDT:USDT BCH/USDT:USDT BNB/USDT:USDT BSW/USDT:USDT BTC/USDT:USDT CAKE/USDT:USDT CRV/USDT:USDT DOGE/USDT:USDT DOT/USDT:USDT ENJ/USDT:USDT EOS/USDT:USDT ETH/USDT:USDT FIL/USDT:USDT FLM/USDT:USDT GALA/USDT:USDT GMT/USDT:USDT GRT/USDT:USDT HBAR/USDT:USDT ICP/USDT:USDT IMX/USDT:USDT INJ/USDT:USDT JASMY/USDT:USDT LINK/USDT:USDT LTC/USDT:USDT MAGIC/USDT:USDT MKR/USDT:USDT NEAR/USDT:USDT OP/USDT:USDT PEOPLE/USDT:USDT RSR/USDT:USDT SHIB1000/USDT:USDT SOL/USDT:USDT STX/USDT:USDT TRX/USDT:USDT UNI/USDT:USDT VET/USDT:USDT XCN/USDT:USDT XLM/USDT:USDT XRP/USDT:USDT S/USDT:USDT MEW/USDT:USDT W/USDT:USDT AI16Z/USDT:USDT JUP/USDT:USDT AKT/USDT:USDT AERO/USDT:USDT POL/USDT:USDT -t 4h --timerange 20221001- --prepend
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
	  保留一个处理器
	  freqtrade hyperopt --config /home/linhai/code/freqtrade/user_data/config.json --hyperopt-loss SuperHyperOptLoss --strategy Trend_Slot_Strategy_rsi_test -e 300 --spaces buy --timerange 20221001- --print-all -j -2
	  ```
	- ```
	  freqtrade hyperopt --config /home/linhai/code/freqtrade/user_data/config.json --hyperopt-loss ProfitDrawDownHyperOptLoss --strategy Trend_Slot_Strategy -e 300 --spaces buy sell --timerange 20221001- --print-all
	  ```
	- ```
	  freqtrade hyperopt --config /home/linhai/code/freqtrade/user_data/config.json --hyperopt-loss SuperHyperOptLoss --strategy Trend_Slot_Strategy_test -e 300 --spaces buy sell --timerange 20221001- --print-all --analyze-per-epoch
	  ```
- 回测命令行
	- ```
	  freqtrade backtesting --strategy EnvelopeStrategy --timeframe 1h --pairs ETH/USDT:USDT --timerange 20250101- --export trades --export-filename=backtest_samplestrategy.json --timeframe-detail 5m
	  ```
	- ```
	  freqtrade backtesting --strategy Trend_Slot_Strategy_rsi_test --timerange=20221001-20250417
	  ```
- 分析优化结果
	- ```
	  freqtrade hyperopt-show --best -n -1
	  ```
	- ```
	  freqtrade hyperopt-list --profitable
	  ```
- dryrun
	- ``freqtrade trade --strategy Trend_Slot_Strategy_test``
		- freqtrade trade --strategy Trend_Slot_Strategy_test
	-
- **测试当前动态交易列表所包含的pair**
	- ```
	  freqtrade test-pairlist --exchange bybit
	  ```
-