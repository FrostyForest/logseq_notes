- #freqtrade
- 这段代码是一个名为 `GridV6_tmp7` 的 Freqtrade 策略，它实现了一个网格交易策略，并且支持 DCA (Dollar Cost Averaging，即定投) 加仓。代码比较复杂，我将分段解释其核心逻辑：
  
  **1. 类的定义和初始化:**
  
  *   `class GridV6_tmp7(IStrategy):` 定义了一个名为 `GridV6_tmp7` 的类，继承自 `IStrategy` 接口。
  *   `INTERFACE_VERSION = 2`：指定策略接口版本为 2。
  
  **2. 常量和参数定义:**
  
  *   定义了一些常量，用于访问 `custom_info` 字典中的数据：
  
      ```python
      DATESTAMP = 0
      GRID = 1
      BOT_STATE = 2
      LIVE_DATE = 3
      INIT_COUNT = 4
      AVG_PRICE = 5
      UNITS = 6
      ```
  *   定义了一些交易参数，例如网格上下间距百分比、触发百分比、偏移百分比等：
  
      ```python
      grid_up_spacing_pct = 1.6
      grid_down_spacing_pct = 4.0
      grid_trigger_pct = 2.0
      grid_shift_pct = 0.4
      live_candles = 200
      stake_to_wallet_ratio = 0.75
      ```
  *   定义了是否开启 DCA 以及 DCA 相关的参数：
  
      ```python
      position_adjustment_enable = True 
      max_dca_orders = 7  
      dca_scale = 1.3
      max_dca_multiplier = (1 - pow(dca_scale,(max_dca_orders + 1)))/(1 - dca_scale)
      ```
  *   定义了 ROI、止损、追踪止损等参数。
  *   定义了 `timeframe` 为 `5m`，`process_only_new_candles` 为 `True`，`startup_candle_count` 为 `0`。
  *   定义了 `plot_config` 用于绘图。
  *   定义了 `custom_info` 字典用于存储自定义信息，每个交易对对应一个列表，列表元素分别是: [日期, 网格价格, 机器人状态, 启动日期, 初始化计数, 平均价格, 数量]。
  *   定义了 `sell_params` 字典。
  *   使用 `DecimalParameter` 定义了可优化的参数 `grid_down_spacing_pct` 和 `grid_up_spacing_pct`。
  
  **3. `calculate_state` 方法:**
  
  这个方法是策略的核心，它负责计算当前机器人的状态、网格价格等信息。
  
  *   **初始化 `custom_info`:** 如果 `custom_info` 中不存在当前交易对的信息，则创建一个空列表进行初始化。
  *   **针对不同交易对设置参数(可选):** 代码中针对 `ELA/USDT`, `ATOM/USDT`, `PRE/USDT`, `PBX/USDT` 四个交易对设置了不同的参数，你可以根据需要修改或删除这部分代码。
  *   **获取最后一个 K 线和初始化计数:** 获取数据帧的最后一行作为最后一个 K 线，并获取当前交易对的初始化计数。
  *   **判断运行模式 (实盘/模拟 或 回测/超参数优化):**
      *   **实盘/模拟:**
          *   如果 `custom_info` 中的 `LIVE_DATE` 为空，表示是第一次运行，将 `init_count` 设置为 0，`bot_state` 设置为 0，`grid`、`avg_price` 设置为当前收盘价，`units` 设置为 0。然后进行一次写死的赋值（可能是用于测试，建议删除）。
          *   如果 `custom_info` 中的 `LIVE_DATE` 不为空，表示不是第一次运行，根据 `LIVE_DATE` 查找对应的 K 线，并从 `custom_info` 中恢复 `bot_state`、`grid`、`avg_price` 和 `units`。
          *   如果没有找到 `LIVE_DATE` 对应的 K 线，则进行类似第一次运行的初始化操作。
      *   **回测/超参数优化:** 将 `row` 设置为 0，`bot_state` 设置为 0，`grid`、`avg_price` 设置为第一个 K 线的收盘价，`units` 设置为 0。
  *   **计算网格上下轨、触发价格:** 根据当前 `bot_state`、`grid`、`avg_price` 以及预设的参数计算网格的上轨、下轨、向上触发价格和向下触发价格。
  *   **创建用于存储中间变量的 NumPy 数组:** 创建 `Buy_1`、`Buy_2`、`Sell_1`、`Grid_up`、`Grid_down`、`Bot_state` 等 NumPy 数组，用于存储每个 K 线的计算结果。`Grid` 和 `Avg_price` 用于调试。
  *   **遍历数据帧:** 从 `row` 开始遍历数据帧，直到最后一个 K 线。
      *   **获取当前 K 线的收盘价:** `close = Close[row]`。
      *   **更新机器人状态、网格价格、持仓数量和平均价格:** 根据当前 `bot_state`、`close`、`grid_up`、`grid_down`、`grid_trigger_up` 和 `grid_trigger_down` 的值，更新 `new_bot_state`、`new_grid`、`new_units` 和 `new_avg_price`。
          *   `bot_state` 为 0 时，表示空仓，如果 `close` 大于 `grid_trigger_up`，则更新 `grid`；如果 `close` 小于等于 `grid_trigger_down`，则进入 `bot_state` 1，并标记 `Buy_1` 信号，更新 `units` 和 `avg_price`。
          *   `bot_state` 为 1 到 `max_dca_orders` 之间时，表示持仓，如果 `close` 大于 `grid_up`，则进入 `bot_state` 0，并标记 `Sell_1` 信号，清空仓位，更新 `grid`；如果 `close` 小于等于 `grid_down`，则进入 `bot_state` + 1，并标记 `Buy_2` 信号，更新 `units` 和 `avg_price`，根据 `dca_scale` 计算加仓数量。
          *   `bot_state` 等于 `max_dca_orders + 1` 时，表示达到最大加仓次数，如果 `close` 大于 `grid_up` 或 `close` 小于等于 `grid_down`，则进入 `bot_state` 0，并标记 `Sell_1` 信号，清空仓位，更新 `grid`。
      *   **更新变量:** 将 `new_bot_state`、`new_grid`、`new_units` 和 `new_avg_price` 的值赋给 `bot_state`、`grid`、`units` 和 `avg_price`。
      *   **更新网格上下轨:** 根据更新后的 `bot_state`、`grid` 和 `avg_price` 重新计算 `grid_up` 和 `grid_down`。
      *   **保存中间变量:** 将 `grid_up`、`grid_down`、`bot_state`、`avg_price` 和 `grid` 的值保存到对应的 NumPy 数组中。
      *   **更新 `init_count`:** 如果 `init_count` 小于 `live_candles`，则加 1。
      *   **更新 `custom_info`:** 将 `init_count` 保存到 `custom_info` 中。
      *   **更新 `row`:** `row += 1`，继续处理下一个 K 线。
  *   **将 NumPy 数组转换为 DataFrame:** 将 `Buy_1`、`Buy_2`、`Sell_1`、`Grid_up`、`Grid_down`、`Bot_state` 等 NumPy 数组转换为 DataFrame。
  *   **合并 DataFrame:** 将转换后的 DataFrame 与原始数据帧 `dataframe` 进行合并。
  *   **返回数据帧:** 返回合并后的数据帧。
  
  **4. `populate_indicators` 方法:**
  
  *   调用 `calculate_state` 方法计算机器人状态等信息，并将结果合并到数据帧中。
  *   返回数据帧。
  
  **5. `custom_stake_amount` 方法:**
  
  *   根据 `max_dca_multiplier` 和 `stake_to_wallet_ratio` 计算自定义的下单金额。
  
  **6. `adjust_trade_position` 方法:**
  
  *   获取当前交易对的最新数据帧。
  *   如果当前是新 K 线, 并且 `buy_2` 信号为 1，且当前仓位小于最大允许的加仓次数:
      *   计算加仓的金额。
      *   返回加仓金额。
  
  **7. `populate_buy_trend` 方法:**
  
  *   如果不是实盘或模拟交易，则调用 `calculate_state` 方法计算机器人状态等信息。
  *   设置 `buy` 列为 0。
  *   如果 `buy_1` 信号为 1，则设置 `buy` 列为 1。
  *   返回数据帧。
  
  **8. `populate_sell_trend` 方法:**
  
  *   设置 `sell` 列为 0。
  *   如果 `sell_1` 信号为 1，则设置 `sell` 列为 1。
  *   返回数据帧。
  
  **总结:**
  
  这段代码实现了一个基于网格交易的策略，并支持 DCA 加仓。策略的核心逻辑在 `calculate_state` 方法中，它根据当前价格和预设的参数计算机器人状态、网格价格等信息，并根据这些信息生成买卖信号。`populate_indicators`、`populate_entry_trend` 和 `populate_sell_trend` 方法分别负责计算指标、生成买入信号和卖出信号。`custom_stake_amount` 和 `adjust_trade_position` 方法分别负责自定义下单金额和调整仓位。
  
  **该策略的主要特点:**
  
  *   **网格交易:** 根据预设的价格网格进行交易。
  *   **DCA 加仓:** 支持在价格下跌时进行多次加仓，以降低平均持仓成本。
  *   **动态参数:** 可以通过 `DecimalParameter` 对网格参数进行优化。
  *   **自定义下单金额:** 可以根据钱包余额和 `max_dca_multiplier` 动态调整下单金额。
  *   **仓位调整:** 支持根据 `buy_2` 信号进行加仓。
  *   **支持实盘/模拟和回测/超参数优化:** 通过 `self.dp.runmode.value` 判断当前运行模式，并根据不同的模式执行不同的逻辑。
  *   **自定义信息存储:** 使用 `custom_info` 字典存储每个交易对的自定义信息。
  
  **需要改进的地方:**
  
  *   代码中存在一些针对特定交易对的硬编码参数，可以将其提取到配置文件中。
  *   `calculate_state` 方法中的逻辑比较复杂，可以考虑将其拆分成多个更小的函数，以提高代码的可读性和可维护性。
  *   缺少对 `custom_info` 的持久化支持，在机器人重启后，`custom_info` 中的数据会丢失。
  *   该策略没有使用传统的技术指标, 可以按需添加.
  *   该策略使用了非常激进的资金管理, 需要根据个人情况修改.
  
  总的来说，这段代码实现了一个相对复杂的网格交易策略，并支持 DCA 加仓和仓位调整。代码的逻辑比较清晰，但也存在一些可以改进的地方。如果你想使用这个策略，建议你仔细理解其逻辑，并根据自己的需求进行修改和优化。同时，强烈建议你在使用该策略进行实盘交易之前，先进行充分的回测和模拟交易，以验证其有效性和风险。