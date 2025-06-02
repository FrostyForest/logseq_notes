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
			- 初步怀疑是custom_entry_price没起作用，为什么，因为单子的价格竟然是ma*0.5+upband*0.5
			- 问题源头是
				- Modifying entry and exit prices will only work for limit orders. 
				  Depending on the price chosen, this can result in a lot of unfilled 
				  orders. By default the maximum allowed distance between the current 
				  price and the custom price is 2%, this value can be changed in config 
				  with the `custom_price_max_distance_ratio` parameter.
				  **Example**:
				  If the new_entryprice is 97, the proposed_rate is 100 and the `custom_price_max_distance_ratio` is set to 2%, The retained valid custom entry price will be 98, which is 2% below the current (proposed) rate.
			-
	- ### 奇怪的bug
		- ```
		  2025-06-01 12:02:15 ERROR   freqtrade.strategy.strategy_wrapper - Unexpected error Parent instance <Order at 0x7f68012f6c60> is not bound to a Session; lazy load operation of attribute '_trade_live' cannot proceed (Background on this error at: https://sqlalche.me/e/20/bhk3) calling <bound method Trend_Slot_Strategy.adjust_trade_position of <trend_slot_strategy.Trend_Slot_Strategy object at 0x7f68022fad80>>
		  Traceback (most recent call last):
		    File "/freqtrade/freqtrade/strategy/strategy_wrapper.py", line 31, in wrapper
		      return f(*args, **kwargs)
		             ^^^^^^^^^^^^^^^^^^
		    File "/freqtrade/user_data/strategies/trend_slot_strategy.py", line 501, in adjust_trade_position
		      initial_stake = filled_entries[0].stake_amount
		                      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
		    File "/freqtrade/freqtrade/persistence/trade_model.py", line 179, in stake_amount
		      / FtPrecise(self.trade.leverage)
		                  ^^^^^^^^^^
		    File "/freqtrade/freqtrade/persistence/trade_model.py", line 171, in trade
		      return self._trade_bt or self._trade_live
		                               ^^^^^^^^^^^^^^^^
		    File "/home/ftuser/.local/lib/python3.12/site-packages/sqlalchemy/orm/attributes.py", line 566, in __get__
		      return self.impl.get(state, dict_)  # type: ignore[no-any-return]
		             ^^^^^^^^^^^^^^^^^^^^^^^^^^^
		    File "/home/ftuser/.local/lib/python3.12/site-packages/sqlalchemy/orm/attributes.py", line 1086, in get
		      value = self._fire_loader_callables(state, key, passive)
		              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
		    File "/home/ftuser/.local/lib/python3.12/site-packages/sqlalchemy/orm/attributes.py", line 1121, in _fire_loader_callables
		      return self.callable_(state, passive)
		             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
		    File "/home/ftuser/.local/lib/python3.12/site-packages/sqlalchemy/orm/strategies.py", line 922, in _load_for_state
		      raise orm_exc.DetachedInstanceError(
		  sqlalchemy.orm.exc.DetachedInstanceError: Parent instance <Order at 0x7f68012f6c60> is not bound to a Session; lazy load operation of attribute '_trade_live' cannot proceed (Background on this error at: https://sqlalche.me/e/20/bhk3)
		  ```
		-