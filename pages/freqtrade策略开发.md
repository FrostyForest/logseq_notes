- #freqtrade
- 当使用detailed timeframe进行回测的时候，出现异常
	- 原因猜测是在每个细分k线都会执行回调函数，导致开单条件异常
- 先解决开不了单的问题
- 尝试在有仓位的情况下进行插针卖出,主要解决两个问题
	- 什么时候卖:1小时均线+系数x乘以(atr/close)
	- 卖多少:初步想法,(atr/close)越小应该卖的越少,越大应该卖得越多,下界是0,上界是0.5
- ## bug
	- ### 超买挂单卖出价格异常
		- ```
		  2025-05-27 08:36:30 INFO    freqtrade.freqtradebot - Found open order for Trade(id=385, pair=CAKE/USDT:USDT, amount=280.00000000, is_short=False, leverage=3.0, open_rate=2.68560000, open_since=2025-05-27 04:00:42)
		  ```
		- 挂单价格不是前一个小时的upband价格，而是前三个小时的价格，看看到了5点会不会修正
			- 修正了
		- 假如现在有空trade,发现加仓逻辑有问题，加仓挂单的价格还是不对，什么原因呢