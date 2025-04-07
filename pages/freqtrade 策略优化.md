- #freqtrade
- **尝试在低波动的时候加仓,高波动的时候减仓**
	- ```
	          weight0=entry_candle['ratio']
	          if initial_direction=='long' and current_rate<initial_price+initial_price*last_candle[f'change_price_factor_buy_{self.change_position_limit_price_factor_buy.value}_4h']:
	              number_of_whielist=len(self.config['exchange']['pair_whitelist'])
	              initial_max_stake=initial_stake*number_of_whielist*1.5/weight0
	              weight=last_candle['ratio']
	              new_stake_amont=initial_max_stake/number_of_whielist/1.5*weight
	              if new_stake_amont>1.15*current_stake_amount and new_stake_amont>initial_stake:
	                  change_stake_amount=new_stake_amont-current_stake_amount
	                  if abs(change_stake_amount)<max_stake and abs(change_stake_amount)>min_stake:
	                      return (change_stake_amount,'change_long_position')
	          if initial_direction=='short' and current_rate>initial_price-initial_price*last_candle[f'change_price_factor_buy_{self.change_position_limit_price_factor_buy.value}_4h']:
	              number_of_whielist=len(self.config['exchange']['pair_whitelist'])
	              initial_max_stake=initial_stake*number_of_whielist*1.5/weight0
	              weight=last_candle['ratio']
	              new_stake_amont=initial_max_stake/number_of_whielist/1.5*weight
	              if new_stake_amont>1.15*current_stake_amount and new_stake_amont>initial_stake:
	                  change_stake_amount=new_stake_amont-current_stake_amount
	                  if abs(change_stake_amount)<max_stake and abs(change_stake_amount)>min_stake:                
	                      return (change_stake_amount,'change_short_position')
	          
	          if initial_direction=='long' and current_rate>initial_price+initial_price*last_candle[f'change_price_factor_sell_{self.change_position_limit_price_factor_sell.value}_4h']:
	              number_of_whielist=len(self.config['exchange']['pair_whitelist'])
	              initial_max_stake=initial_stake*number_of_whielist*1.5/weight0
	              weight=last_candle['ratio']
	              new_stake_amont=initial_max_stake/number_of_whielist/1.5*weight
	              if new_stake_amont<0.93*current_stake_amount and new_stake_amont>=initial_stake*0.94:
	                  change_stake_amount=new_stake_amont-current_stake_amount
	                  if abs(change_stake_amount)<max_stake and abs(change_stake_amount)>min_stake:  
	                      return (change_stake_amount,'change_long_position')
	                  
	          if initial_direction=='short' and current_rate<initial_price-initial_price*last_candle[f'change_price_factor_sell_{self.change_position_limit_price_factor_sell.value}_4h']:
	              number_of_whielist=len(self.config['exchange']['pair_whitelist'])
	              initial_max_stake=initial_stake*number_of_whielist*1.5/weight0
	              weight=last_candle['ratio']
	              new_stake_amont=initial_max_stake/number_of_whielist/1.5*weight
	              if new_stake_amont<0.93*current_stake_amount and new_stake_amont>=initial_stake*0.94:
	                  change_stake_amount=new_stake_amont-current_stake_amount
	                  if abs(change_stake_amount)<max_stake and abs(change_stake_amount)>min_stake:  
	                      return (change_stake_amount,'change_short_position')
	  ```
-