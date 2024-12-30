- #crypto #freqtrade
- ## Freqtrade 基础
  
  本页面提供了一些关于 Freqtrade 如何工作和运作的基本概念。
  
  **Freqtrade 术语**
  
  *   **策略 (Strategy):** 你的交易策略，告诉机器人做什么。
  *   **交易 (Trade):**  已开仓的仓位。
  *   **未成交订单 (Open Order):** 当前已在交易所下单，但尚未完全成交的订单。
  *   **交易对 (Pair):** 可交易的货币对，通常格式为 基础货币/报价货币 (例如，现货交易的 XRP/USDT，期货交易的 XRP/USDT:USDT)。
  *   **时间周期 (Timeframe):** 使用的 K 线时长 (例如 "5m", "1h" 等)。
  *   **指标 (Indicators):** 技术指标 (SMA, EMA, RSI 等)。
  *   **限价单 (Limit order):** 以指定限价或更优价格成交的订单。
  *   **市价单 (Market order):** 保证成交，但根据订单大小可能会导致价格变动。
  *   **当前利润 (Current Profit):** 当前未实现 (unrealized) 的交易利润。主要在机器人和 UI 中使用。
  *   **已实现利润 (Realized Profit):** 已经实现的利润。仅在结合部分平仓时才有意义 - 这也解释了它的计算逻辑。
  *   **总利润 (Total Profit):** 已实现利润和未实现利润的总和。相对数字 (%) 是根据该交易的总投资额计算的。
  
  **手续费处理**
  
  Freqtrade 的所有利润计算都包含手续费。对于回测/超参数优化/模拟交易模式，使用交易所默认手续费 (交易所的最低等级)。对于实盘交易，手续费按照交易所实际收取计算 (包括 BNB 折扣等)。
  
  **交易对命名**
  
  Freqtrade 遵循 ccxt 的货币命名规范。在错误的交易对上使用错误的命名规范通常会导致机器人无法识别交易对，通常会导致 "this pair is not available" 之类的错误。
  
  **现货交易对命名**
  
  对于现货交易对，命名方式为 基础货币/报价货币 (例如 ETH/USDT)。
  
  **期货交易对命名**
  
  对于期货交易对，命名方式为 基础货币/报价货币:结算货币 (例如 ETH/USDT:USDT)。
  
  **机器人执行逻辑**
  
  以模拟交易或实盘模式启动 Freqtrade (使用 `freqtrade trade` 命令) 将启动机器人并开始机器人循环迭代。这还将运行 `bot_start()` 回调函数。
  
  默认情况下，机器人循环每隔几秒运行一次 (internals.process_throttle_secs) 并执行以下操作：
  
  1. 从持久化存储中获取未结交易。
  2. 计算当前可交易的交易对列表。
  3. 下载交易对列表的所有信息性交易对的 OHLCV 数据。
    *   此步骤每个 K 线只执行一次，以避免不必要的网络流量。
  4. 调用 `bot_loop_start()` 策略回调函数。
  5. 分析每个交易对的策略。
    *   调用 `populate_indicators()`
    *   调用 `populate_entry_trend()`
    *   调用 `populate_exit_trend()`
  6. 从交易所更新交易的未成交订单状态。
    *   对已成交的订单调用 `order_filled()` 策略回调函数。
    *   检查未成交订单的超时情况。
        *   对未成交的开仓订单调用 `check_entry_timeout()` 策略回调函数。
        *   对未成交的平仓订单调用 `check_exit_timeout()` 策略回调函数。
        *   对未成交的开仓订单调用 `adjust_entry_price()` 策略回调函数。
  7. 验证现有仓位，并最终下达平仓订单。
    *   考虑止损、ROI 和出场信号、`custom_exit()` 和 `custom_stoploss()`。
    *   根据 `exit_pricing` 配置设置或使用 `custom_exit_price()` 回调函数确定平仓价格。
    *   在下达平仓订单之前，将调用 `confirm_trade_exit()` 策略回调函数。
  8. 如果启用，检查未结交易的仓位调整，调用 `adjust_trade_position()` 并根据需要下达额外的订单。
  9. 检查交易槽是否仍然可用 (如果达到 `max_open_trades`)。
  10. 验证开仓信号，尝试建立新的仓位。
    *   根据 `entry_pricing` 配置设置或使用 `custom_entry_price()` 回调函数确定开仓价格。
    *   在杠杆和期货模式下，调用 `leverage()` 策略回调函数来确定所需的杠杆。
    *   调用 `custom_stake_amount()` 回调函数确定下单金额。
    *   在下达开仓订单之前，将调用 `confirm_trade_entry()` 策略回调函数。
  
  这个循环将不断重复，直到机器人停止。
  
  **回测/超参数优化执行逻辑**
  
  回测或超参数优化只执行上述逻辑的一部分，因为大多数交易操作都是完全模拟的。
  
  1. 加载已配置交易对的历史数据。
  2. 调用 `bot_start()` 一次。
  3. 计算指标 (每个交易对调用一次 `populate_indicators()`)。
  4. 计算开仓/平仓信号 (每个交易对调用一次 `populate_entry_trend()` 和 `populate_exit_trend()`)。
  5. 按 K 线循环模拟开仓和平仓点。
    *   调用 `bot_loop_start()` 策略回调函数。
    *   通过 `unfilledtimeout` 配置或 `check_entry_timeout()` / `check_exit_timeout()` 策略回调函数检查订单超时。
    *   对未成交的开仓订单调用 `adjust_entry_price()` 策略回调函数。
    *   检查交易开仓信号 (`enter_long` / `enter_short` 列)。
    *   确认交易开仓/平仓 (如果策略中实现了 `confirm_trade_entry()` 和 `confirm_trade_exit()`，则调用它们)。
    *   调用 `custom_entry_price()` (如果策略中已实现) 来确定开仓价格 (价格被移动到开盘 K 线之内)。
    *   在杠杆和期货模式下，调用 `leverage()` 策略回调函数来确定所需的杠杆。
    *   调用 `custom_stake_amount()` 回调函数确定下单金额。
    *   如果启用，检查未结交易的仓位调整，并调用 `adjust_trade_position()` 来确定是否需要额外的订单。
    *   对已成交的开仓订单调用 `order_filled()` 策略回调函数。
    *   调用 `custom_stoploss()` 和 `custom_exit()` 寻找自定义平仓点。
    *   对于基于出场信号、custom-exit 和部分平仓的平仓：调用 `custom_exit_price()` 确定平仓价格 (价格被移动到收盘 K 线之内)。
    *   对已成交的平仓订单调用 `order_filled()` 策略回调函数。
  6. 生成回测报告输出。
  
  **注意**
  
  回测和超参数优化都在计算中包括了交易所默认手续费。可以通过指定 `--fee` 参数将自定义手续费传递给回测/超参数优化。
  
  **回调函数调用频率**
  
  回测将最多每根 K 线调用每个回调函数一次 (`--timeframe-detail` 将此行为修改为每个详细 K 线一次)。大多数回调函数在实盘交易中每次迭代都会被调用 (通常每隔约 5 秒) - 这可能导致回测结果不匹配。