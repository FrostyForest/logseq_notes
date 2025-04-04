- #freqtrade
- **实验1**
	- **pair**
		- "BTC/USDT:USDT",
		- "XRP/USDT:USDT",
		- "DOGE/USDT:USDT",
		- "SOL/USDT:USDT",
		- "ADA/USDT:USDT",
		- "AVAX/USDT:USDT",
		- "DOT/USDT:USDT",
		- "XLM/USDT:USDT",
		- "HBAR/USDT:USDT",
		- "FIL/USDT:USDT",
		- "MKR/USDT:USDT"
	- **parameter**
		- "buy": {
		- "atr_length": 18,
		- "entry_long_length": 44,
		- "entry_short_length": 21,
		- "trend_entry_weight": 1.2
		- },
		- "sell": {
		- "atr_exit_factor": 4.5,
		- "atr_length_slot": 15,
		- "exit_long_length": 23,
		- "exit_short_length": 21,
		- "sell_ratio_factor": 10.5
		- },
	- **result**
		- | Backtesting from | 2021-03-15 08:00:00 |
		  | Backtesting to | 2025-03-28 18:00:00 |
		- | Metric | Value |
		  | ---- | ---- | ---- |
		  | Total Profit | 26721.833% | 2672183.312 USDT |
		  | CAGR | 299.352% |
		  | Sortino | 5.82 |
		  | Sharpe | 2.14 |
		  | Calmar | 2027.48 |
		  | Expectancy (ratio) | 1111.56 (0.49) |
		  | Profit factor | 1.825 |
		  | Total trades / Daily Avg Trades | 2404 / 1.63 |
		  | Best day | 2133.42% | 949902.096 USDT |
		  | Worst day | -203.97% | -140644.387 USDT |
		  | Win/Draw/Loss | 967 / 0 / 1437 (WR: 40.22%) |
		  | Days win/draw/loss | 357 / 578 / 539 |
		  | Avg. Duration winners | 1 week, 2 days, 33 minutes |
		  | Avg. Duration Losers | 3 days, 11 hours, 2 minutes |
		  | Max Consecutive Wins / Loss | 12 / 20 |
- **实验2**:回测时间20221001开始,回测对象实际策略
	- 结果1:
		- 61/500:   3107 trades. 1249/0/1858 Wins/Draws/Losses. Avg profit   4.84%. Median profit  -4.62%. Total profit 461084.91379728 USDT (4610.85%). Avg duration 4 days, 19:03:00 min. Objective: -461084.91380
		- # Buy hyperspace params:
		    buy_params = {
		        "atr_length": 15,
		        "entry_long_length": 45,  # value loaded from strategy
		        "entry_short_length": 21,  # value loaded from strategy
		        "trend_entry_weight": 1.2,  # value loaded from strategy
		    }
		- # Sell hyperspace params:
		    sell_params = {
		        "atr_length_slot": 12,
		        "exit_short_length": 16,
		        "atr_exit_factor": 4.5,  # value loaded from strategy
		        "exit_long_length": 23,  # value loaded from strategy
		        "sell_ratio_factor": 10.5,  # value loaded from strategy
		    }
		- | Metric | Value |
		  | ---- | ---- | ---- |
		  | Total Profit | 4610.849% | 461084.914 USDT |
		  | CAGR | 368.103% |
		  | Sortino | 17.04 |
		  | Sharpe | 5.31 |
		  | Calmar | 822.93 |
		  | Expectancy (ratio) | 148.40 (0.53) |
		  | Profit factor | 1.883 |
		  | Total trades / Daily Avg Trades | 3107 / 3.41 |
		  | Best day | 2041.32% | 120738.484 USDT |
		  | Worst day | -310.59% | -21850.436 USDT |
		  | Win/Draw/Loss | 1249 / 0 / 1858 (WR: 40.20%) |
		  | Days win/draw/loss | 289 / 203 / 418 |
		  | Avg. Duration winners | 1 week, 14 hours, 48 minutes |
		  | Avg. Duration Losers | 2 days, 21 hours, 30 minutes |
		  | Max Consecutive Wins / Loss | 23 / 36 |
	- **结果2**
		- "buy": {
		- "entry_long_length": 45,
		- "entry_short_length": 26,
		- "trend_entry_weight": 1.2,
		- "atr_length": 16
		- },
		- "sell": {
		- "atr_exit_factor": 4.7,
		- "exit_long_length": 27,
		- "sell_ratio_factor": 9.7,
		- "atr_length_slot": 12,
		- "exit_short_length": 16
		- },
		- | Metric | Value |
		  | ---- | ---- | ---- |
		  | Total Profit | 5102.201% | 510220.123 USDT |
		  | CAGR | 387.085% |
		  | Sortino | 15.39 |
		  | Sharpe | 4.18 |
		  | Calmar | 1081.81 |
		  | Expectancy (ratio) | 183.73 (0.57) |
		  | Profit factor | 1.958 |
		  | Total trades / Daily Avg Trades | 2777 / 3.05 |
		  | Best day | 2243.90% | 116567.24 USDT |
		  | Worst day | -370.93% | -21417.744 USDT |
		  | Win/Draw/Loss | 1138 / 0 / 1639 (WR: 40.98%) |
		  | Days win/draw/loss | 264 / 239 / 407 |
		  | Avg. Duration winners | 1 week, 1 day, 6 hours, 41 minutes |
		  | Avg. Duration Losers | 3 days, 6 hours, 33 minutes |
		  | Max Consecutive Wins / Loss | 22 / 36 |
	- **结果3**
		- "buy": {
		- "entry_long_length": 44,
		- "entry_short_length": 21,
		- "trend_entry_weight": 1.2,
		- "atr_length": 18
		- },
		- "sell": {
		- "atr_exit_factor": 4.5,
		- "exit_long_length": 21,
		- "sell_ratio_factor": 10.5,
		- "atr_length_slot": 15,
		- "exit_short_length": 16
		- },
		- | Metric | Value |
		  | ---- | ---- | ---- |
		  | Total Profit | 4581.966% | 458196.603 USDT |
		  | CAGR | 366.951% |
		  | Sortino | 15.99 |
		  | Sharpe | 5.18 |
		  | Calmar | 692.81 |
		  | Expectancy (ratio) | 146.44 (0.52) |
		  | Profit factor | 1.868 |
		  | Total trades / Daily Avg Trades | 3129 / 3.43 |
		  | Best day | 2056.23% | 123942.381 USDT |
		  | Worst day | -310.59% | -23169.031 USDT |
		  | Win/Draw/Loss | 1254 / 0 / 1875 (WR: 40.08%) |
		  | Days win/draw/loss | 288 / 205 / 417 |
		  | Avg. Duration winners | 1 week, 14 hours, 51 minutes |
		  | Avg. Duration Losers | 2 days, 21 hours, 56 minutes |
		  | Max Consecutive Wins / Loss | 23 / 36 |
		-
	-
-