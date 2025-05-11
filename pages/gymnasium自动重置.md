- #reinforcement_learning #gymnasium
- 为了提高向量化环境的效率，**自动重置 (autoreset)** 是一项常见功能，它允许子环境在不需要所有子环境都完成之前就进行重置。以前在 Gym 和 Gymnasium 中，自动重置是在环境 episode 结束的同一步骤中完成的，因此最终的观察 (observation) 和信息 (info) 会存储在 step 返回的 info 中，即 info["final_observation"] 和 info["final_info"]，而标准的 obs 和 info 则包含子环境重置后的观察和信息。因此，准确地从向量化环境中采样观察需要以下代码（请注意，如果子环境终止 (terminated) 或截断 (truncated)，则需要提取 infos["next_obs"][j]）。此外，对于使用 rollout 的 on-policy 算法，需要额外的一次前向传播来计算正确的下一个观察（这通常作为一种优化手段而不执行，假设环境只会终止而不会截断）。
- ## 旧的自动重置采样逻辑
	- ```
	  replay_buffer = []	
	  obs, _ = envs.reset()
	  for _ in range(total_timesteps):
	    next_obs, rewards, terminations, truncations, infos = envs.step(envs.action_space.sample())
	  
	    for j in range(envs.num_envs):
	        if not (terminations[j] or truncations[j]): # 如果环境没有结束
	            replay_buffer.append((
	                obs[j], rewards[j], terminations[j], truncations[j], next_obs[j]
	            ))
	        else: # 如果环境结束了
	            replay_buffer.append((
	                obs[j], rewards[j], terminations[j], truncations[j], infos["next_obs"][j] # 使用 info 中的下一个观察
	            ))
	  
	    obs = next_obs
	  ```
- ## 新的采样逻辑
	- ```
	  # 新的自动重置采样逻辑 (v1.0.0)
	  replay_buffer = []
	  obs, _ = envs.reset()
	  autoreset = np.zeros(envs.num_envs) # 用于跟踪哪些环境在上一步已经结束
	  for _ in range(total_timesteps):
	      next_obs, rewards, terminations, truncations, _ = envs.step(envs.action_space.sample()) # 注意这里忽略了 info
	  
	      for j in range(envs.num_envs):
	          if not autoreset[j]: # 如果上一步环境没有结束，则记录这条经验
	              replay_buffer.append((
	                  obs[j], rewards[j], terminations[j], truncations[j], next_obs[j]
	              ))
	  
	      obs = next_obs
	      autoreset = np.logical_or(terminations, truncations) # 更新哪些环境在这一步结束了
	  ```
-