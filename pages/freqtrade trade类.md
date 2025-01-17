- #freqtrade
- **翻译：**
- ## 交易对象
- ### Trade
  
  Freqtrade 进入的仓位存储在一个 Trade 对象中 - 该对象被持久化到数据库中。它是 freqtrade 的一个核心概念 - 你将在文档的许多部分遇到它，这些部分很可能会将你引导到这个位置。
  
  它将在许多策略回调函数中传递给策略。传递给策略的对象不能直接修改。可能会根据回调结果发生间接修改。
- ### Trade - 可用属性
  
  以下属性/属性可用于每个单独的交易 - 并且可以使用 `trade.<property>` (例如 `trade.pair`) 访问。
  
  | 属性                   | 数据类型   | 描述                                                                                                                                                                     |
  | :--------------------- | :--------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | pair                   | string     | 此交易的交易对。                                                                                                                                                         |
  | is_open                | boolean    | 交易当前是未结状态，还是已结束。                                                                                                                                         |
  | open_rate              | float      | 此交易的开仓价格 (如果是加仓，则为平均开仓价格)。                                                                                                                         |
  | close_rate             | float      | 平仓价格 - 仅在 `is_open = False` 时设置。                                                                                                                               |
  | stake_amount           | float      | 本金货币 (Stake Currency) 金额。                                                                                                                                         |
  | amount                 | float      | 当前拥有的基础货币 (Base Currency) 数量。在初始订单成交之前将为 0.0。                                                                                                     |
  | open_date              | datetime   | 交易开仓的时间戳，请改用 `open_date_utc`                                                                                                                                 |
  | open_date_utc          | datetime   | 交易开仓的时间戳 - UTC 时间。                                                                                                                                            |
  | close_date             | datetime   | 交易平仓的时间戳，请改用 `close_date_utc`                                                                                                                                |
  | close_date_utc         | datetime   | 交易平仓的时间戳 - UTC 时间。                                                                                                                                            |
  | close_profit           | float      | 交易平仓时的相对利润。0.01 == 1%                                                                                                                                          |
  | close_profit_abs       | float      | 交易平仓时的绝对利润 (以本金货币计价)。                                                                                                                                   |
  | leverage               | float      | 此交易使用的杠杆 - 在现货市场中默认为 1.0。                                                                                                                             |
  | enter_tag              | string     | 通过数据帧中的 `enter_tag` 列提供的开仓标签。                                                                                                                             |
  | is_short               | boolean    | 对于做空交易为 `True`，否则为 `False`。                                                                                                                                 |
  | orders                 | Order[]    | 附加到此交易的订单对象列表 (包括已成交和已取消的订单)。                                                                                                                   |
  | date_last_filled_utc   | datetime   | 最后成交订单的时间。                                                                                                                                                     |
  | entry_side             | "buy" / "sell" | 交易开仓的订单方向。                                                                                                                                                   |
  | exit_side              | "buy" / "sell" | 将导致交易平仓/仓位减少的订单方向。                                                                                                                                     |
  | trade_direction        | "long" / "short" | 交易方向，文本形式 - 做多或做空。                                                                                                                                        |
  | nr_of_successful_entries | int        | 成功 (已成交) 开仓订单的数量。                                                                                                                                            |
  | nr_of_successful_exits  | int        | 成功 (已成交) 平仓订单的数量。                                                                                                                                            |
- ### 类方法
  
  以下是类方法 - 返回通用信息，通常会导致对数据库的显式查询。它们可以用作 `Trade.<method>` - 例如 `open_trades = Trade.get_open_trade_count()`
  
  **回测/超参数优化**
  
  大多数方法在回测/超参数优化和实盘/模拟模式下都可以使用。在回测期间，它仅限于在策略回调函数中使用。不支持在 `populate_*()` 方法中使用，并且会导致错误的结果。
  
  **`get_trades_proxy`**
  
  当你的策略需要有关现有 (未结或已平仓) 交易的一些信息时 - 最好使用 `Trade.get_trades_proxy()`。
  
  用法：
  
  ```python
  from freqtrade.persistence import Trade
  from datetime import timedelta
  
  # ...
  trade_hist = Trade.get_trades_proxy(pair='ETH/USDT', is_open=False, open_date=current_date - timedelta(days=2))
  ```
  
  `get_trades_proxy()` 支持以下关键字参数。所有参数都是可选的 - 不带参数调用 `get_trades_proxy()` 将返回数据库中所有交易的列表。
  
  *   `pair` 例如 `pair='ETH/USDT'`
  *   `is_open` 例如 `is_open=False`
  *   `open_date` 例如 `open_date=current_date - timedelta(days=2)`
  *   `close_date` 例如 `close_date=current_date - timedelta(days=5)`
  
  **`get_open_trade_count`**
  
  获取当前未结交易的数量
  
  ```python
  from freqtrade.persistence import Trade
  # ...
  open_trades = Trade.get_open_trade_count()
  ```
  
  **`get_total_closed_profit`**
  
  检索机器人迄今为止产生的总利润。汇总所有已平仓交易的 `close_profit_abs`。
  
  ```python
  from freqtrade.persistence import Trade
  
  # ...
  profit = Trade.get_total_closed_profit()
  ```
  
  **`total_open_trades_stakes`**
  
  检索当前交易中已投资的总金额 (以本金货币计价)。
  
  ```python
  from freqtrade.persistence import Trade
  
  # ...
  profit = Trade.total_open_trades_stakes()
  ```
  
  **`get_overall_performance`**
  
  检索总体绩效 - 类似于 `/performance` 电报命令。
  
  ```python
  from freqtrade.persistence import Trade
  
  # ...
  if self.config['runmode'].value in ('live', 'dry_run'):
    performance = Trade.get_overall_performance()
  ```
  
  示例返回值：ETH/BTC 有 5 笔交易，总利润为 1.5% (比率为 0.015)。
  
  ```json
  {"pair": "ETH/BTC", "profit": 0.015, "count": 5}
  ```
  
  **订单对象**
  
  `Order` 对象表示交易平台上的订单 (或模拟运行模式下的模拟订单)。`Order` 对象始终与其对应的 `Trade` 相关联，并且只有在交易的上下文中才有意义。
  
  **订单 - 可用属性**
  
  订单对象通常附加到交易中。此处的属性大多可以为 `None`，因为它们取决于交易所的响应。
  
  | 属性                | 数据类型   | 描述                                                                                                       |
  | :------------------ | :--------- | :--------------------------------------------------------------------------------------------------------- |
  | trade               | Trade      | 此订单所附加到的交易对象                                                                                   |
  | ft_pair             | string     | 此订单的交易对                                                                                             |
  | ft_is_open          | boolean    | 订单是否已成交？                                                                                           |
  | order_type          | string     | 交易平台定义的订单类型 - 通常是 `market`、`limit` 或 `stoploss`                                                |
  | status              | string     | 由 ccxt 定义的状态。通常是 `open`、`closed`、`expired` 或 `canceled`                                           |
  | side                | string     | 买入或卖出                                                                                                 |
  | price               | float      | 下单的价格                                                                                                 |
  | average             | float      | 订单成交的平均价格                                                                                         |
  | amount              | float      | 基础货币的数量                                                                                             |
  | filled              | float      | 已成交的数量 (以基础货币计价)                                                                               |
  | remaining           | float      | 剩余数量                                                                                                   |
  | cost                | float      | 订单的成本 - 通常是 `average * filled` (期货交易所可能会有所不同，可能会包含有或没有杠杆的成本，并且可能以合约为单位。) |
  | stake_amount        | float      | 用于此订单的本金货币数量。在 2023.7 版本中添加。                                                                 |
  | stake_amount_filled | float      | 用于此订单的已成交本金货币数量。在 2024.11 版本中添加。                                                              |
  | order_date          | datetime   | 订单创建日期，请改用 `order_date_utc`                                                                       |
  | order_date_utc      | datetime   | 订单创建日期 (UTC 时间)                                                                                    |
  | order_fill_date     | datetime   | 订单成交日期，请改用 `order_fill_utc`                                                                       |
  | order_fill_date_utc | datetime   | 订单成交日期                                                                                               |
-