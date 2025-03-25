- #freqtrade
- 待加入的标的:
	- s
	- fartcoin
	- pol
	- cake
	- crv
	- ape
	- aero
- 结果1
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
-