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