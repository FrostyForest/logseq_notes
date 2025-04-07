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
- **结果8** 该结果为20221001对超过20个pair进行优化得到
	- 210/276:   2768 trades. 1135/0/1633 Wins/Draws/Losses. Avg profit   6.13%. Median profit  -4.40%. Total profit 554789.47696396 USDT (5547.89%). Avg duration 5 days, 7:32:00 min. Objective: -554789.47696
	- # Buy hyperspace params:
	    buy_params = {
	        "atr_length": 16,
	        "entry_long_length": 46,
	        "entry_short_length": 26,
	        "trend_entry_weight": 1.2,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 4.7,
	        "exit_long_length": 27,
	        "exit_short_length": 16,
	        "sell_ratio_factor": 9.7,
	        "atr_length_slot": 15,  # value loaded from strategy
	    }
- **结果9**
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
- **结果10**
	- Best result:
	  
	     109/276:   3644 trades. 1472/0/2172 Wins/Draws/Losses. Avg profit   4.26%. Median profit  -3.80%. Total profit 653545.83023738 USDT (6535.46%). Avg duration 3 days, 20:39:00 min. Objective: -653545.83024
	- # Buy hyperspace params:
	    buy_params = {
	        "entry_short_length": 19,
	        "atr_length": 15,  # value loaded from strategy
	        "entry_long_length": 45,  # value loaded from strategy
	        "trend_entry_weight": 1.2888,  # value loaded from strategy
	    }
	- # Sell hyperspace params:
	    sell_params = {
	        "atr_exit_factor": 4.8,
	        "exit_short_length": 12,
	        "atr_length_slot": 12,  # value loaded from strategy
	        "exit_long_length": 21,  # value loaded from strategy
	        "sell_ratio_factor": 10,  # value loaded from strategy
	    }
- **对策略开仓修正后的优化结果**
	- **第一次优化**
		- 485/500:   1400 trades. 591/0/809 Wins/Draws/Losses. Avg profit   6.53%. Median profit  -3.41%. Total profit 284534.71354696 USDT (11381.39%). Avg duration 5 days, 8:53:00 min. Objective: -284534.71355
		- # Buy hyperspace params:
		    buy_params = {
		        "atr_length": 16,
		        "entry_long_length": 34,
		        "entry_short_length": 31,
		        "trend_entry_weight": 1,  # value loaded from strategy
		    }
		- # Sell hyperspace params:
		    sell_params = {
		        "atr_exit_factor": 4.7,
		        "exit_short_length": 16,
		        "sell_ratio_factor": 11.0,
		        "atr_length_slot": 15,  # value loaded from strategy
		        "exit_long_length": 24,  # value loaded from strategy
		    }
- **顺势加仓版本优化结果**
	- 第一次优化
		- 193/228:   3566 trades. 1316/0/2250 Wins/Draws/Losses. Avg profit   2.41%. Median profit  -4.94%. Total profit 1059861.49984011 USDT (10598.61%). Avg duration 3 days, 21:20:00 min. Objective: -930702.65647
		- # Buy hyperspace params:
		    buy_params = {
		        "breakout_adjust_downlimit": 1.6,
		        "breakout_adjust_uplimit": 3.0,
		        "atr_length": 15,  # value loaded from strategy
		        "entry_long_length": 45,  # value loaded from strategy
		        "entry_short_length": 19,  # value loaded from strategy
		        "trend_entry_weight": 1.25,  # value loaded from strategy
		    }
		- # Sell hyperspace params:
		    sell_params = {
		        "sell_ratio_factor": 13.0,
		        "atr_exit_factor": 4.55,  # value loaded from strategy
		        "atr_length_slot": 12,  # value loaded from strategy
		        "exit_long_length": 21,  # value loaded from strategy
		        "exit_short_length": 12,  # value loaded from strategy
		    }
	-