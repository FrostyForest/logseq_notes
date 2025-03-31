- #freqtrade
- 待加入的标的:
	- s
	- fartcoin
	- pol
	- cake
	- crv
	- ape
	- aero
	- 1000
- **结果1**
	- 140/156:   4750 trades. 1855/0/2895 Wins/Draws/Losses. Avg profit   4.08%. Median profit  -6.10%. Total profit 254158.67004730 USDT (2541.59%). Avg duration 5 days, 13:41:00 min. Objective: -254158.67005
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 56,
	        "entry_long_length": 39,
	        "entry_short_length": 21,
	        "buy_rsi": 30,  # value loaded from strategy
	        "exit_short_rsi": 30,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "exit_long_length": 47,
	        "exit_short_length": 21,
	        "sell_rsi": 70,  # value loaded from strategy
	        "short_rsi": 70,  # value loaded from strategy
	    }
- **结果2**
	- 144/193:   4568 trades. 1807/0/2761 Wins/Draws/Losses. Avg profit   4.27%. Median profit  -6.07%. Total profit 261040.23816446 USDT (2610.40%). Avg duration 5 days, 14:45:00 min. Objective: -261040.23816
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 53,
	        "entry_long_length": 45,
	        "entry_short_length": 21,
	        "buy_rsi": 30,  # value loaded from strategy
	        "exit_short_rsi": 30,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "exit_long_length": 23,
	        "exit_short_length": 21,
	        "sell_rsi": 70,  # value loaded from strategy
	        "short_rsi": 70,  # value loaded from strategy
	    }
- **结果3**
	- 118/217:   4531 trades. 1785/0/2746 Wins/Draws/Losses. Avg profit   4.65%. Median profit  -5.99%. Total profit 74456.36039408 USDT (4963.76%). Avg duration 5 days, 15:37:00 min. Objective: -74456.36039
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 45,
	        "entry_long_length": 44,
	        "buy_rsi": 30,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	        "exit_short_rsi": 30,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "exit_short_length": 21,
	        "exit_long_length": 23,  # value loaded from strategy
	        "sell_rsi": 70,  # value loaded from strategy
	        "short_rsi": 70,  # value loaded from strategy
	    }
	- # Stoploss:
	    stoploss = -0.999  # value loaded from strategy
	- # Trailing stop:
	    trailing_stop = False  # value loaded from strategy
	    trailing_stop_positive = None  # value loaded from strategy
	    trailing_stop_positive_offset = 0.0  # value loaded from strategy
	    trailing_only_offset_is_reached = False  # value loaded from strategy
	- # Max Open Trades:
	    max_open_trades = 30  # value loaded from strategy
-
- 加入slot后继续优化
- **结果1**
	- Best result:
	  
	  *   16/300:   1097 trades. 435/0/662 Wins/Draws/Losses. Avg profit   5.14%. Median profit  -3.96%. Total profit 329560.66479908 USDT (2197.07%). Avg duration 5 days, 16:52:00 min. Objective: -329560.66480
	- # Buy hyperspace params:
	    buy_params = {
	        "entry_long_length": 44,
	        "atr_length": 42,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 3.3,
	        "atr_length_slot": 8,
	        "exit_long_length": 23,  # value loaded from strategy
	        "exit_short_length": 21,  # value loaded from strategy
	        "sell_ratio_factor": 6.0,  # value loaded from strategy
	    }
- **结果2**
	- 52/300:   1265 trades. 517/0/748 Wins/Draws/Losses. Avg profit   6.15%. Median profit  -3.77%. Total profit 466197.10434446 USDT (3107.98%). Avg duration 5 days, 19:01:00 min. Objective: -466197.10434
	- # Buy hyperspace params:
	    buy_params = {
	        "entry_long_length": 45,
	        "atr_length": 42,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 3.9,
	        "atr_length_slot": 16,
	        "exit_long_length": 23,  # value loaded from strategy
	        "exit_short_length": 21,  # value loaded from strategy
	        "sell_ratio_factor": 8.0,  # value loaded from strategy
	    }
- **结果3**
	- 261/300:   1537 trades. 612/0/925 Wins/Draws/Losses. Avg profit   5.67%. Median profit  -4.35%. Total profit 372734.89577205 USDT (2484.90%). Avg duration 5 days, 18:05:00 min. Objective: -372734.89577
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 42,  # value loaded from strategy
	        "entry_long_length": 43,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 4.2,
	        "atr_length_slot": 18,
	        "exit_long_length": 23,  # value loaded from strategy
	        "exit_short_length": 21,  # value loaded from strategy
	        "sell_ratio_factor": 7.5,  # value loaded from strategy
	    }
- **结果4**
	- 61/300:   1524 trades. 615/0/909 Wins/Draws/Losses. Avg profit   5.81%. Median profit  -4.10%. Total profit 419068.00776221 USDT (2793.79%). Avg duration 5 days, 18:24:00 min. Objective: -419068.00776
	- # Buy hyperspace params:
	    buy_params = {
	        "entry_long_length": 44,
	        "atr_length": 42,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 3.9,
	        "sell_ratio_factor": 8.8,
	        "atr_length_slot": 16,  # value loaded from strategy
	        "exit_long_length": 23,  # value loaded from strategy
	        "exit_short_length": 21,  # value loaded from strategy
	    }
- **结果5**
	- 80/300:   1670 trades. 671/0/999 Wins/Draws/Losses. Avg profit   5.14%. Median profit  -4.02%. Total profit 421579.14075422 USDT (2810.53%). Avg duration 4 days, 21:37:00 min. Objective: -421579.14075
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 42,  # value loaded from strategy
	        "entry_long_length": 44,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_length_slot": 17,
	        "exit_short_length": 16,
	        "atr_exit_factor": 3.9,  # value loaded from strategy
	        "exit_long_length": 23,  # value loaded from strategy
	        "sell_ratio_factor": 8.88,  # value loaded from strategy
	    }
- **结果6**
	- 116/300:   2716 trades. 1099/0/1617 Wins/Draws/Losses. Avg profit   5.64%. Median profit  -4.96%. Total profit 63908.65521180 USDT (2556.35%). Avg duration 5 days, 16:01:00 min. Objective: -63908.65521
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 30,
	        "entry_long_length": 44,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 4.5,
	        "sell_ratio_factor": 10.7,
	        "atr_length_slot": 16,  # value loaded from strategy
	        "exit_long_length": 23,  # value loaded from strategy
	        "exit_short_length": 21,  # value loaded from strategy
	    }
	- # Stoploss:
	    stoploss = -2.0  # value loaded from strategy
	- # Trailing stop:
	    trailing_stop = False  # value loaded from strategy
	    trailing_stop_positive = None  # value loaded from strategy
	    trailing_stop_positive_offset = 0.0  # value loaded from strategy
	    trailing_only_offset_is_reached = False  # value loaded from strategy
	- # Max Open Trades:
	    max_open_trades = 30  # value loaded from strategy
- **结果7**
	- *    5/300:   2731 trades. 1105/0/1626 Wins/Draws/Losses. Avg profit   5.42%. Median profit  -4.96%. Total profit 60947.67924095 USDT (2437.91%). Avg duration 5 days, 15:30:00 min. Objective: -60947.67924
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 19,
	        "entry_long_length": 44,  # value loaded from strategy
	        "entry_short_length": 21,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_length_slot": 15,
	        "atr_exit_factor": 4.5,  # value loaded from strategy
	        "exit_long_length": 23,  # value loaded from strategy
	        "exit_short_length": 21,  # value loaded from strategy
	        "sell_ratio_factor": 10.5,  # value loaded from strategy
	    }