- #freqtrade
- ## 配置机器人
  
  Freqtrade 有许多可配置的功能和可能性。默认情况下，这些设置通过配置文件进行配置 (参见下文)。
  
  **Freqtrade 配置文件**
  
  机器人在其运行过程中使用一组配置参数，这些参数共同构成了机器人配置。它通常从文件 (Freqtrade 配置文件) 中读取其配置。
  
  默认情况下，机器人从当前工作目录中的 `config.json` 文件加载配置。
  
  你可以使用 `-c/--config` 命令行选项指定机器人使用的不同配置文件。
  
  如果你使用快速入门方法安装机器人，安装脚本应该已经为你创建了默认配置文件 (`config.json`)。
  
  如果未创建默认配置文件，我们建议使用 `freqtrade new-config --config user_data/config.json` 生成一个基本的配置文件。
  
  Freqtrade 配置文件应以 JSON 格式编写。
  
  除了标准的 JSON 语法之外，你还可以在配置文件中使用单行 `// ...` 和多行 `/* ... */` 注释以及参数列表中的尾随逗号。
  
  如果你不熟悉 JSON 格式，请不要担心 - 只需使用你选择的编辑器打开配置文件，对你需要更改的参数进行一些更改，保存更改，最后，重新启动机器人，或者如果它之前已停止，则使用你对配置所做的更改再次运行它。机器人在启动时会验证配置文件的语法，如果你在编辑时出现任何错误，它会警告你，并指出有问题的行。
  
  **环境变量**
  
  通过环境变量在 Freqtrade 配置中设置选项。这优先于配置或策略中的相应值。
  
  环境变量必须以 `FREQTRADE__` 为前缀才能加载到 Freqtrade 配置中。
  
  `__` 用作级别分隔符，因此使用的格式应对应于 `FREQTRADE__{section}__{key}`。因此 - 定义为 `export FREQTRADE__STAKE_AMOUNT=200` 的环境变量将导致 `{stake_amount: 200}`。
  
  一个更复杂的示例可能是 `export FREQTRADE__EXCHANGE__KEY=<yourExchangeKey>` 以保持你的交易所密钥私密。这将把值移动到配置的 `exchange.key` 部分。使用此方案，所有配置设置也将作为环境变量提供。
  
  请注意，环境变量将覆盖配置中的相应设置，但命令行参数将始终优先。
  
  常见示例：
  
  ```bash
  FREQTRADE__TELEGRAM__CHAT_ID=<telegramchatid>
  FREQTRADE__TELEGRAM__TOKEN=<telegramToken>
  FREQTRADE__EXCHANGE__KEY=<yourExchangeKey>
  FREQTRADE__EXCHANGE__SECRET=<yourExchangeSecret>
  ```
  
  Json 列表被解析为 json - 因此你可以使用以下命令设置交易对列表：
  
  ```bash
  export FREQTRADE__EXCHANGE__PAIR_WHITELIST='["BTC/USDT", "ETH/USDT"]'
  ```
  
  **注意**
  
  检测到的环境变量在启动时会被记录 - 因此，如果你根据配置找不到为什么某个值不是你认为的那样，请确保它不是从环境变量加载的。
  
  **验证组合结果**
  
  你可以使用 `show-config` 子命令查看最终的组合配置。
  
  **加载顺序**
  
  **多个配置文件**
  
  可以指定多个配置文件并由机器人使用，或者机器人可以从进程标准输入流中读取其配置参数。
  
  你可以在 `add_config_files` 中指定其他配置文件。此参数中指定的文件将被加载并与初始配置文件合并。这些文件是相对于初始配置文件解析的。这类似于使用多个 `--config` 参数，但使用起来更简单，因为你不必为所有命令指定所有文件。
  
  **验证组合结果**
  
  你可以使用 `show-config` 子命令查看最终的组合配置。
  
  **使用多个配置文件来保持机密信息机密**
  
  你可以使用包含机密信息的第二个配置文件。这样，你可以共享你的 “主要” 配置文件，同时仍然保留你自己的 API 密钥。第二个文件应该只指定你打算覆盖的内容。如果一个键位于多个配置中，则 “最后指定的配置” 获胜 (在上面的示例中，`config-private.json`)。
  
  对于一次性命令，你还可以通过指定多个 `--config` 参数来使用以下语法。
  
  ```bash
  freqtrade trade --config user_data/config1.json --config user_data/config-private.json <...>
  ```
  
  以下内容与上面的示例等效 - 但在配置中有 2 个配置文件，以便于重用。
  
  `user_data/config.json`
  
  ```json
  "add_config_files": [
    "config1.json",
    "config-private.json"
  ]
  ```
  
  ```bash
  freqtrade trade --config user_data/config.json <...>
  ```
  
  **配置冲突处理**
  
  **编辑器自动完成和验证**
  
  如果你使用的编辑器支持 JSON 架构，你可以使用 Freqtrade 提供的架构，通过将以下行添加到配置文件的顶部来获取配置文件的自动完成和验证：
  
  ```json
  {
    "$schema": "https://schema.freqtrade.io/schema.json",
  }
  ```
  
  **开发版本**
  
  **配置参数**
  
  下表将列出所有可用的配置参数。
  
  Freqtrade 还可以通过命令行 (CLI) 参数加载许多选项 (查看命令的 `--help` 输出以获取详细信息)。
  
  **配置选项优先级**
  
  所有选项的优先级如下：
  
  1. CLI 参数覆盖任何其他选项
  2. 环境变量
  3. 配置文件按顺序使用 (最后一个文件获胜) 并覆盖策略配置。
  4. 仅当未通过配置或命令行参数设置策略配置时，才使用策略配置。下表中标记为 “策略覆盖” 的选项。
  
  参数表
  
  必填参数标记为**必填**，这意味着它们需要以某种可能的方式设置。
  
  | 参数                                  | 描述                                                                                                                                                                                                                                                            | 数据类型                                    |
  | :------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------ |
  | `max_open_trades`                     | **必填**。你的机器人允许进行的未结交易数量。每个交易对只能进行一笔未结交易，因此你的交易对列表的长度是可以应用的另一个限制。如果为 -1，则忽略它 (即可能无限的未结交易，受交易对列表的限制)。有关更多信息，请参见下文。策略覆盖。                                         | 正整数或 -1                                 |
  | `stake_currency`                      | **必填**。用于交易的加密货币。                                                                                                                                                                                                                                      | 字符串                                      |
  | `stake_amount`                        | **必填**。你的机器人将用于每笔交易的加密货币数量。将其设置为 "unlimited" 以允许机器人使用所有可用余额。有关更多信息，请参见下文。                                                                                                                                   | 正浮点数或 "unlimited"                        |
  | `tradable_balance_ratio`              | 机器人允许交易的总账户余额的比率。有关更多信息，请参见下文。 默认为 0.99 (99%)。                                                                                                                                                                                          | 0.1 到 1.0 之间的正浮点数                   |
  | `available_capital`                   | 机器人的可用起始资金。在同一交易所账户上运行多个机器人时很有用。有关更多信息，请参见下文。                                                                                                                                                                          | 正浮点数                                    |
  | `amend_last_stake_amount`              | 如有必要，使用减少的最后下单金额。有关更多信息，请参见下文。 默认为 `false`。                                                                                                                                                                                        | 布尔值                                      |
  | `last_stake_amount_min_ratio`          | 定义必须保留并执行的最小下单金额。仅在最后下单金额被修改为减少的值时适用 (即，如果 `amend_last_stake_amount` 设置为 `true`)。有关更多信息，请参见下文。 默认为 0.5。                                                                                                  | 浮点数 (作为比率)                          |
  | `amount_reserve_percent`              | 在最小交易对下单金额中保留一些金额。机器人在计算最小交易对下单金额时将保留 `amount_reserve_percent` + 止损值，以避免可能的交易拒绝。 默认为 0.05 (5%)。                                                                                                         | 正浮点数作为比率                            |
  | `timeframe`                           | 要使用的 时间周期 (例如 1m, 5m, 15m, 30m, 1h ...)。通常在配置中缺失，并在策略中指定。策略覆盖。                                                                                                                                                                 | 字符串                                      |
  | `fiat_display_currency`               | 用于显示利润的法定货币。有关更多信息，请参见下文。                                                                                                                                                                                                                   | 字符串                                      |
  | `dry_run`                             | **必填**。定义机器人必须处于模拟运行还是生产模式。 默认为 `true`。                                                                                                                                                                                                    | 布尔值                                      |
  | `dry_run_wallet`                      | 定义在模拟运行模式下运行的机器人使用的模拟钱包的以本金货币为单位的起始金额。有关更多信息，请参见下文 默认为 1000。                                                                                                                                                  | 浮点数或字典                                |
  | `cancel_open_orders_on_exit`          | 发出 `/stop` RPC 命令、按下 Ctrl+C 或机器人意外死亡时取消未结订单。设置为 `true` 时，这允许你在市场崩溃时使用 `/stop` 取消未成交和部分成交的订单。它不影响未结仓位。 默认为 `false`。                                                                                 | 布尔值                                      |
  | `process_only_new_candles`             | 仅在新 K 线到达时启用指标处理。如果为 `false`，则每个循环都会填充指标，这将意味着处理相同的 K 线多次，从而造成系统负载，但如果你的策略依赖于不仅是 K 线数据的行情数据，则可能很有用。策略覆盖。 默认为 `true`。                                                          | 布尔值                                      |
  | `minimal_roi`                         | **必填**。设置机器人将用于平仓交易的最小 ROI 阈值作为比率。有关更多信息，请参见下文。策略覆盖。                                                                                                                                                                       | 字典                                        |
  | `stoploss`                            | **必填**。机器人使用的止损值作为比率。有关止损文档中的更多详细信息。策略覆盖。                                                                                                                                                                                          | 浮点数 (作为比率)                          |
  | `trailing_stop`                       | 启用追踪止损 (基于配置或策略文件中的 `stoploss`)。有关止损文档中的更多详细信息。策略覆盖。                                                                                                                                                                                  | 布尔值                                      |
  | `trailing_stop_positive`              | 在达到利润后更改止损。有关止损文档中的更多详细信息。策略覆盖。                                                                                                                                                                                                      | 浮点数                                      |
  | `trailing_stop_positive_offset`       | 何时应用 `trailing_stop_positive` 的偏移量。应该是正值的百分比值。有关止损文档中的更多详细信息。策略覆盖。 默认为 0.0 (无偏移)。                                                                                                                                  | 浮点数                                      |
  | `trailing_only_offset_is_reached`     | 仅在达到偏移量时应用追踪止损。止损文档。策略覆盖。 默认为 `false`。                                                                                                                                                                                               | 布尔值                                      |
  | `fee`                                 | 回测/模拟运行期间使用的手续费。通常不应配置，这使得 freqtrade 回退到交易所默认手续费。设置为比率 (例如 0.001 = 0.1%)。每笔交易收取两次手续费，一次在买入时，一次在卖出时。                                                                                                  | 浮点数 (作为比率)                          |
  | `futures_funding_rate`                | 当交易所未提供历史资金费率时使用的用户指定的资金费率。这不会覆盖真实的历史费率。建议将其设置为 0，除非你正在测试特定的币种并且了解资金费率将如何影响 freqtrade 的利润计算。有关更多信息，请参见[此处](https://www.freqtrade.io/en/stable/exchange/#funding-rates)。 默认为 `None`。 | 浮点数                                      |
  | `trading_mode`                        | 指定你是要进行常规交易、杠杆交易还是交易价格来自匹配加密货币价格的合约。[杠杆文档](https://www.freqtrade.io/en/stable/leverage/)。 默认为 `"spot"`。                                                                                                                 | 字符串                                      |
  | `margin_mode`                         | 使用杠杆交易时，这将确定交易者拥有的抵押品是共享的还是隔离到每个交易对。[杠杆文档](https://www.freqtrade.io/en/stable/leverage/)。                                                                                                                         | 字符串                                      |
  | `liquidation_buffer`                  | 指定在清算价格和止损之间放置多大的安全网的比率，以防止仓位达到清算价格[杠杆文档](https://www.freqtrade.io/en/stable/leverage/)。 默认为 0.05。                                                                                                                  | 浮点数                                      |
  | `unfilledtimeout.entry`               | **必填**。机器人将等待未成交的开仓订单完成多长时间 (以分钟或秒为单位)，之后订单将被取消。策略覆盖。                                                                                                                                                                   | 整数                                        |
  | `unfilledtimeout.exit`                | **必填**。机器人将等待未成交的平仓订单完成多长时间 (以分钟或秒为单位)，之后订单将被取消并在当前 (新) 价格重复，只要有信号。策略覆盖。                                                                                                                                 | 整数                                        |
  | `unfilledtimeout.unit`                | 在 `unfilledtimeout` 设置中使用的单位。注意：如果你将 `unfilledtimeout.unit` 设置为 "seconds"，则 "internals.process_throttle_secs" 必须小于或等于超时 策略覆盖。 默认为 "minutes"。                                                                                  | 字符串                                      |
  | `unfilledtimeout.exit_timeout_count`  | 平仓订单可以超时的次数。达到此超时次数后，将触发紧急平仓。0 表示禁用并允许无限次订单取消。策略覆盖。 默认为 0。                                                                                                                                                 | 整数                                        |
  | `entry_pricing.price_side`            | 选择机器人应该查看价差的哪一侧来获取开仓价格。有关更多信息，请参见下文。 默认为 "same"。                                                                                                                                                                                  | 字符串 (ask、bid、same 或 other)。            |
  | `entry_pricing.price_last_balance`    | **必填**。插值买入价格。有关更多信息，请参见下文。                                                                                                                                                                                                                      |                                              |
  | `entry_pricing.use_order_book`        | 启用使用订单簿开仓中的价格进行开仓。 默认为 `true`。                                                                                                                                                                                                                   | 布尔值                                      |
  | `entry_pricing.order_book_top`        | 机器人将使用订单簿 "price_side" 中的前 N 个价格来开仓。即，值为 2 将允许机器人选择订单簿开仓中的第二个条目。 默认为 1。                                                                                                                                               | 正整数                                      |
  | `entry_pricing. check_depth_of_market.enabled` | 如果买单和卖单的差异在订单簿中得到满足，则不要开仓。检查市场深度。 默认为 `false`。                                                                                                                                                                             | 布尔值                                      |
  | `entry_pricing. check_depth_of_market.bids_to_ask_delta` | 在订单簿中找到的买单和卖单的差异比率。低于 1 的值表示卖单规模更大，而大于 1 的值表示买单规模更高。检查市场深度 默认为 0。                                                                                                                                     | 浮点数 (作为比率)                          |
  | `exit_pricing.price_side`             | 选择机器人应该查看价差的哪一侧来获取平仓价格。有关更多信息，请参见下文。 默认为 "same"。                                                                                                                                                                                  | 字符串 (ask、bid、same 或 other)。            |
  | `exit_pricing.price_last_balance`     | 插值平仓价格。有关更多信息，请参见下文。                                                                                                                                                                                                                              |                                              |
  | `exit_pricing.use_order_book`         | 启用使用订单簿平仓中的价格进行平仓。 默认为 `true`。                                                                                                                                                                                                                   | 布尔值                                      |
  | `exit_pricing.order_book_top`         | 机器人将使用订单簿 "price_side" 中的前 N 个价格进行平仓。即，值为 2 将允许机器人选择订单簿平仓中的第二个卖出价 默认为 1。                                                                                                                                               | 正整数                                      |
  | `custom_price_max_distance_ratio`      | 配置当前价格和自定义开仓或平仓价格之间的最大距离比率。 默认为 0.02 (2%)。                                                                                                                                                                                          | 正浮点数                                    |
  | `use_exit_signal`                     | 除了 `minimal_roi` 之外，还使用策略产生的平仓信号。 将此设置为 `false` 将禁用使用 "exit_long" 和 "exit_short" 列。不会影响其他平仓方法 (止损、ROI、回调函数)。策略覆盖。 默认为 `true`。                                                                                 | 布尔值                                      |
  | `exit_profit_only`                    | 等到机器人达到 `exit_profit_offset` 后再做出平仓决定。策略覆盖。 默认为 `false`。                                                                                                                                                                                        | 布尔值                                      |
  | `exit_profit_offset`                  | 只有在此值之上才激活平仓信号。仅在 `exit_profit_only=True` 时有效。策略覆盖。 默认为 0.0。                                                                                                                                                                               | 浮点数 (作为比率)                          |
  | `ignore_roi_if_entry_signal`          | 如果开仓信号仍然有效，则不要平仓。此设置优先于 `minimal_roi` 和 `use_exit_signal`。策略覆盖。 默认为 `false`。                                                                                                                                                            | 布尔值                                      |
  | `ignore_buying_expired_candle_after`  | 指定买入信号不再使用之前的秒数。                                                                                                                                                                                                                                  | 整数                                        |
  | `order_types`                         | 根据操作 ("entry", "exit", "stoploss", "stoploss_on_exchange") 配置订单类型。有关更多信息，请参见下文。策略覆盖。                                                                                                                                                      | 字典                                        |
  | `order_time_in_force`                 | 为开仓和出仓订单配置 time in force。有关更多信息，请参见下文。策略覆盖。                                                                                                                                                                                                  | 字典                                        |
  | `position_adjustment_enable`          | 使策略能够使用仓位调整 (额外的买入或卖出)。有关更多信息，请参见[此处](https://www.freqtrade.io/en/stable/strategy-advanced/#adjust-trade-position)。 策略覆盖。 默认为 `false`。                                                                                                | 布尔值                                      |
  | `max_entry_position_adjustment`       | 每个未结交易在第一个开仓订单之上的最大额外订单数。将其设置为 -1 表示不限制额外订单的数量。有关更多信息，请参见[此处](https://www.freqtrade.io/en/stable/strategy-advanced/#adjust-trade-position)。 策略覆盖。 默认为 -1。                                                | 正整数或 -1                                 |
  | `exchange.name`                       | **必填**。要使用的交易所类的名称。                                                                                                                                                                                                                                | 字符串                                      |
  | `exchange.key`                        | 用于交易所的 API 密钥。仅在生产模式下需要。 **请保密，不要公开泄露。**                                                                                                                                                                                                | 字符串                                      |
  | `exchange.secret`                     | 用于交易所的 API 密钥。仅在生产模式下需要。 **请保密，不要公开泄露。**                                                                                                                                                                                                | 字符串                                      |
  | `exchange.password`                   | 用于交易所的 API 密码。仅在生产模式下需要，并且仅适用于使用 API 请求密码的交易所。 **请保密，不要公开泄露。**                                                                                                                                                             | 字符串                                      |
  | `exchange.uid`                         | 用于交易所的 API uid。仅在生产模式下需要，并且仅适用于使用 API 请求 uid 的交易所。 **请保密，不要公开泄露。**                                                                                                                                                               | 字符串                                      |
  | `exchange.pair_whitelist`              | 机器人用于交易和检查回测期间潜在交易的交易对列表。支持正则表达式交易对作为 `.*/BTC`。`VolumePairList` 不使用。更多信息。                                                                                                                                              | 列表                                        |
  | `exchange.pair_blacklist`              | 机器人必须绝对避免用于交易和回测的交易对列表。更多信息。                                                                                                                                                                                                                 | 列表                                        |
  | `exchange.ccxt_config`                | 传递给 ccxt 实例 (同步和异步) 的其他 CCXT 参数。这通常是附加 ccxt 配置的正确位置。参数可能因交易所而异，并在 [ccxt 文档](https://docs.ccxt.com/#/README?id=user-defined-options) 中有记录。请避免在此处添加交易所密钥 (请改用专用字段)，因为它们可能包含在日志中。 | 字典                                        |
  | `exchange.ccxt_sync_config`            | 传递给常规 (同步) ccxt 实例的其他 CCXT 参数。参数可能因交易所而异，并在 [ccxt 文档](https://docs.ccxt.com/#/README?id=user-defined-options) 中有记录                                                                                                           | 字典                                        |
  | `exchange.ccxt_async_config`           | 传递给异步 ccxt 实例的其他 CCXT 参数。参数可能因交易所而异，并在 [ccxt 文档](https://docs.ccxt.com/#/README?id=user-defined-options) 中有记录                                                                                                           | 字典                                        |
  | `exchange.enable_ws`                  | 启用交易所使用 Websocket。 更多信息。 默认为 `true`。                                                                                                                                                                                                                | 布尔值                                      |
  | `exchange.markets_refresh_interval`    | 重新加载市场的间隔 (以分钟为单位)。 默认为 60 分钟。                                                                                                                                                                                                                  | 正整数                                      |
  | `exchange.skip_open_order_update`     | 如果交易所导致问题，则跳过启动时的未结订单更新。仅与实盘条件相关。 默认为 `false`                                                                                                                                                                                     | 布尔值                                      |
  | `exchange.unknown_fee_rate`            | 计算交易费用时使用的回退值。这对于以非交易货币收取费用的交易所很有用。此处提供的值将乘以 “费用成本”。 默认为 `None`                                                                                                                                                | 浮点数                                      |
  | `exchange.log_responses`              | 记录相关的交易所响应。仅限调试模式 - 谨慎使用。 默认为 `false`                                                                                                                                                                                                      | 布尔值                                      |
  | `exchange.only_from_ccxt`              | 阻止从 `data.binance.vision` 下载数据。将此保留为 `false` 可以大大加快下载速度，但如果该站点不可用，则可能会出现问题。 默认为 `false`                                                                                                                                       | 布尔值                                      |
  | `experimental.block_bad_exchanges`    | 阻止已知无法与 freqtrade 一起使用的交易所。除非你想测试该交易所现在是否有效，否则请保留默认值。 默认为 `true`。                                                                                                                                                        | 布尔值                                      |
  | `edge.*`                              | 有关所有可能的配置选项的详细说明，请参阅 [edge 配置文档](https://www.freqtrade.io/en/stable/edge/)。                                                                                                                                                                      |                                              |
  | `pairlists`                           | 定义一个或多个要使用的交易对列表。更多信息。 默认为 `StaticPairList`。                                                                                                                                                                                                    | 字典列表                                    |
  | `telegram.enabled`                    | 启用 Telegram 的使用。                                                                                                                                                                                                                                          | 布尔值                                      |
  | `telegram.token`                      | 你的 Telegram 机器人令牌。仅当 `telegram.enabled` 为 `true` 时才需要。 **请保密，不要公开泄露。**                                                                                                                                                                    | 字符串                                      |
  | `telegram.chat_id`                    | 你的个人 Telegram 账户 ID。仅当 `telegram.enabled` 为 `true` 时才需要。 **请保密，不要公开泄露。**                                                                                                                                                                    | 字符串                                      |
  | `telegram.balance_dust_level`          | 以本金货币为单位的粉尘水平 - 余额低于此值的货币将不会被 `/balance` 显示。                                                                                                                                                                                              | 浮点数                                      |
  | `telegram.reload`                     | 在 Telegram 消息上允许 "reload" 按钮。 默认为 `true`。                                                                                                                                                                                                              | 布尔值                                      |
  | `telegram.notification_settings.*`    | 详细的通知设置。有关详细信息，请参阅 [Telegram 文档](https://www.freqtrade.io/en/stable/telegram/#notification-settings)。                                                                                                                                               | 字典                                        |
  | `telegram.allow_custom_messages`       | 允许通过 `dataprovider.send_msg()` 函数从策略发送 Telegram 消息。                                                                                                                                                                                                       | 布尔值                                      |
  | `webhook.enabled`                     | 启用 Webhook 通知的使用                                                                                                                                                                                                                                          | 布尔值                                      |
  | `webhook.url`                         | Webhook 的 URL。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                      | 字符串                                      |
  | `webhook.entry`                       | 开仓时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                      | 字符串                                      |
  | `webhook.entry_cancel`                | 取消开仓订单时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                | 字符串                                      |
  | `webhook.entry_fill`                  | 开仓订单成交时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                | 字符串                                      |
  | `webhook.exit`                        | 平仓时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                      | 字符串                                      |
  | `webhook.exit_cancel`                 | 取消平仓订单时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                | 字符串                                      |
  | `webhook.exit_fill`                   | 平仓订单成交时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                | 字符串                                      |
  | `webhook.status`                      | 状态调用时发送的负载。仅当 `webhook.enabled` 为 `true` 时才需要。有关更多详细信息，请参阅 [Webhook 文档](https://www.freqtrade.io/en/stable/webhook/)。                                                                                                                      | 字符串                                      |
  | `webhook.allow_custom_messages`        | 允许通过 `dataprovider.send_msg()` 函数从策略发送 Webhook 消息。                                                                                                                                                                                                     | 布尔值                                      |
  | `api_server.enabled`                  | 启用 API 服务器的使用。有关更多详细信息，请参阅 [API 服务器文档](https://www.freqtrade.io/en/stable/rest-api/)。                                                                                                                                                            | 布尔值                                      |
  | `api_server.listen_ip_address`         | 绑定 IP 地址。有关更多详细信息，请参阅 [API 服务器文档](https://www.freqtrade.io/en/stable/rest-api/)。                                                                                                                                                                   | IPv4                                        |
  | `api_server.listen
- | 参数                                  | 描述                                                                                                                                                                                                                                                            | 数据类型                                    |
  | :------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------ |
  | `api_server.listen_port`               | 绑定端口。有关更多详细信息，请参阅 [API 服务器文档](https://www.freqtrade.io/en/stable/rest-api/)。                                                                                                                                                                   | 1024 到 65535 之间的整数                   |
  | `api_server.verbosity`                 | 日志详细程度。`info` 将打印所有 RPC 调用，而 "error" 将仅显示错误。                                                                                                                                                                                                      | 枚举，`info` 或 `error`。默认为 `info`。   |
  | `api_server.username`                  | API 服务器的用户名。有关更多详细信息，请参阅 [API 服务器文档](https://www.freqtrade.io/en/stable/rest-api/)。 **请保密，不要公开泄露。**                                                                                                                                  | 字符串                                      |
  | `api_server.password`                  | API 服务器的密码。有关更多详细信息，请参阅 [API 服务器文档](https://www.freqtrade.io/en/stable/rest-api/)。 **请保密，不要公开泄露。**                                                                                                                                  | 字符串                                      |
  | `api_server.ws_token`                  | Message WebSocket 的 API 令牌。有关更多详细信息，请参阅 [API 服务器文档](https://www.freqtrade.io/en/stable/rest-api/)。 **请保密，不要公开泄露。**                                                                                                              | 字符串                                      |
  | `bot_name`                            | 机器人的名称。通过 API 传递给客户端 - 可以显示以区分/命名机器人。 默认为 `freqtrade`                                                                                                                                                                                  | 字符串                                      |
  | `external_message_consumer`            | 启用生产者/消费者模式 [更多详细信息](https://www.freqtrade.io/en/stable/advanced-message-handling/)。                                                                                                                                                               | 字典                                        |
  | `initial_state`                       | 定义初始应用程序状态。如果设置为 `stopped`，则必须通过 `/start` RPC 命令显式启动机器人。 默认为 `stopped`。                                                                                                                                                              | 枚举，`stopped` 或 `running`                |
  | `force_entry_enable`                  | 启用通过 Telegram 和 REST API 强制开仓的 RPC 命令 (`/forcelong`, `/forceshort`)。有关更多信息，请参见下文。                                                                                                                                                            | 布尔值                                      |
  | `disable_dataframe_checks`            | 禁用检查从策略方法返回的 OHLCV 数据帧的正确性。仅在有意更改数据帧并了解你在做什么时使用。策略覆盖。 默认为 `False`。                                                                                                                                                   | 布尔值                                      |
  | `internals.process_throttle_secs`      | 设置进程限制或一次机器人迭代循环的最小循环持续时间。以秒为单位的值。 默认为 5 秒。                                                                                                                                                                                         | 正整数                                      |
  | `internals.heartbeat_interval`         | 每 N 秒打印心跳消息。设置为 0 以禁用心跳消息。 默认为 60 秒。                                                                                                                                                                                                          | 正整数或 0                                  |
  | `internals.sd_notify`                  | 启用使用 sd_notify 协议向 systemd 服务管理器通知机器人状态的变化并发起保持活动 ping。有关更多详细信息，请参见[此处](https://www.freqtrade.io/en/stable/systemd/)。                                                                                                     | 布尔值                                      |
  | `strategy`                            | **必填** 定义要使用的策略类。建议通过 `--strategy NAME` 设置。                                                                                                                                                                                                    | 类名                                        |
  | `strategy_path`                       | 添加额外的策略查找路径 (必须是目录)。                                                                                                                                                                                                                              | 字符串                                      |
  | `recursive_strategy_search`           | 设置为 `true` 以递归搜索 `user_data/strategies` 内的子目录中的策略。                                                                                                                                                                                                 | 布尔值                                      |
  | `user_data_dir`                       | 包含用户数据的目录。 默认为 `./user_data/`。                                                                                                                                                                                                                          | 字符串                                      |
  | `db_url`                              | 声明要使用的数据库 URL。**注意**：如果 `dry_run` 为 `true`，则默认为 `sqlite:///tradesv3.dryrun.sqlite`，对于生产实例，则默认为 `sqlite:///tradesv3.sqlite`。                                                                                                       | 字符串，SQLAlchemy 连接字符串              |
  | `logfile`                             | 指定日志文件名。对日志文件轮换使用 10 个文件、每个文件限制为 1MB 的滚动策略。                                                                                                                                                                                         | 字符串                                      |
  | `add_config_files`                    | 其他配置文件。这些文件将被加载并与当前配置文件合并。这些文件是相对于初始文件解析的。 默认为 `[]`。                                                                                                                                                                   | 字符串列表                                  |
  | `dataformat_ohlcv`                    | 用于存储历史 K 线 (OHLCV) 数据的数据格式。 默认为 `feather`。                                                                                                                                                                                                            | 字符串                                      |
  | `dataformat_trades`                   | 用于存储历史交易数据的数据格式。 默认为 `feather`。                                                                                                                                                                                                                | 字符串                                      |
  | `reduce_df_footprint`                 | 将所有数字列重新转换为 float32/int32，目的是减少 ram/磁盘使用量 (并减少 FreqAI 中的训练/推理时间)。(当前仅影响 FreqAI 用例)                                                                                                                                           | 布尔值。默认值：`False`。                    |
  
  **策略中的参数**
  
  可以在配置文件或策略中设置以下参数。配置文件中设置的值始终会覆盖策略中设置的值。
  
  *   `minimal_roi`
  *   `timeframe`
  *   `stoploss`
  *   `max_open_trades`
  *   `trailing_stop`
  *   `trailing_stop_positive`
  *   `trailing_stop_positive_offset`
  *   `trailing_only_offset_is_reached`
  *   `use_custom_stoploss`
  *   `process_only_new_candles`
  *   `order_types`
  *   `order_time_in_force`
  *   `unfilledtimeout`
  *   `disable_dataframe_checks`
  *   `use_exit_signal`
  *   `exit_profit_only`
  *   `exit_profit_offset`
  *   `ignore_roi_if_entry_signal`
  *   `ignore_buying_expired_candle_after`
  *   `position_adjustment_enable`
  *   `max_entry_position_adjustment`
  
  **配置每笔交易的金额**
  
  有几种方法可以配置机器人用于进行交易的本金货币数量。所有方法都遵守下述的可用余额配置。
  
  **最小交易本金**
  
  最小下单金额将取决于交易所和交易对，通常列在交易所支持页面中。
  
  假设 XRP/USD 的最小可交易数量为 20 XRP (由交易所提供)，价格为 0.6 美元，则购买此交易对的最小下单金额为 20 * 0.6 ~= 12。该交易所对美元也有一个限制 - 所有订单必须 > 10 美元 - 但是在这种情况下不适用。
  
  为了保证安全执行，freqtrade 将不允许以 10.1 美元的下单金额进行购买，相反，它将确保有足够的空间在该交易对下方设置止损 (+ 一个偏移量，由 `amount_reserve_percent` 定义，默认为 5%)。
  
  储备金为 5% 时，最小下单金额为 ~12.6 美元 (12 * (1 + 0.05))。如果在此基础上再加上 10% 的止损 - 我们最终将得到 ~14 美元 (12.6 / (1 - 0.1)) 的值。
  
  为了限制此计算，在止损值较大的情况下，计算出的最小下单限额永远不会超过实际限额的 50%。
  
  **警告**
  
  由于交易所的限制通常是稳定的并且不经常更新，因此某些交易对可能会显示相当高的最低限额，这仅仅是因为自交易所上次调整限额以来价格大幅上涨。Freqtrade 会将下单金额调整为此值，除非它比计算/期望的下单金额高出 > 30% - 在这种情况下，交易将被拒绝。
  
  **模拟钱包**
  
  在模拟运行模式下，机器人将使用模拟钱包来执行交易。此钱包的起始余额由 `dry_run_wallet` 定义 (默认为 1000)。对于更复杂的场景，你还可以为 `dry_run_wallet` 分配一个字典来定义每种货币的起始余额。
  
  ```json
  "dry_run_wallet": {
      "BTC": 0.01,
      "ETH": 2,
      "USDT": 1000
  }
  ```
  
  可以使用命令行选项 (`--dry-run-wallet`) 覆盖配置值，但仅适用于浮点值，不适用于字典。如果你想使用字典，请调整配置文件。
  
  **注意**
  
  非本金货币的余额将不会用于交易，但会显示为钱包余额的一部分。在跨市场保证金交易所，钱包余额可用于计算可用于交易的抵押品。
  
  **可交易余额**
  
  默认情况下，机器人假定全部金额 - 1% 可供其使用，并且当使用动态下单金额时，它会将全部余额分成 `max_open_trades` 个桶，每个交易一个。Freqtrade 将保留 1% 用于开仓时的最终费用，因此默认情况下不会触及该金额。
  
  你可以使用 `tradable_balance_ratio` 设置配置 “未触及” 的金额。
  
  例如，如果你在交易所的钱包中有 10 个 ETH 可用，并且 `tradable_balance_ratio=0.5` (即 50%)，那么机器人将最多使用 5 个 ETH 进行交易，并将其视为可用余额。钱包的其余部分不受交易影响。
  
  **危险**
  
  在同一账户上运行多个机器人时不应使用此设置。请改为将可用资金分配给机器人。
  
  **警告**
  
  `tradable_balance_ratio` 设置适用于当前余额 (自由余额 + 交易中绑定的余额)。因此，假设起始余额为 1000，配置为 `tradable_balance_ratio=0.99` 将不能保证 10 个货币单位始终在交易所可用。例如，如果总余额减少到 500 (由于连续亏损或提取余额)，则可用金额可能会减少到 5 个单位。
  
  **分配可用资金**
  
  要在使用同一交易所账户的多个机器人之间充分利用复合利润，你需要将每个机器人限制为一定的起始余额。这可以通过将 `available_capital` 设置为所需的起始余额来实现。
  
  假设你的账户有 10000 USDT，并且你想在此交易所上运行 2 个不同的策略。你可以设置 `available_capital=5000` - 授予每个机器人 5000 USDT 的初始资金。然后，机器人会将此起始余额平均分配到 `max_open_trades` 个桶中。获利的交易将导致此机器人的下单规模增加 - 而不会影响另一个机器人的下单规模。
  
  调整 `available_capital` 需要重新加载配置才能生效。调整 `available_capital` 会增加先前 `available_capital` 和新的 `available_capital` 之间的差额。当交易未结时减少可用资金不会退出交易。差额在交易结束时返还给钱包。这的结果取决于调整和平仓交易之间的价格变动。
  
  **与 `tradable_balance_ratio` 不兼容**
  
  设置此选项将替换 `tradable_balance_ratio` 的任何配置。
  
  **修改最后下单金额**
  
  假设我们的可交易余额为 1000 USDT，`stake_amount=400`，`max_open_trades=3`。机器人将开仓 2 个交易，并且将无法填补最后一个交易槽位，因为所请求的 400 USDT 不再可用，因为 800 USDT 已经绑定在其他交易中。
  
  为了克服这个问题，可以将选项 `amend_last_stake_amount` 设置为 `True`，这将使机器人能够将 `stake_amount` 减少到可用余额以填补最后一个交易槽位。
  
  在上面的示例中，这意味着：
  
  *   交易 1：400 USDT
  *   交易 2：400 USDT
  *   交易 3：200 USDT
  
  **注意**
  
  此选项仅适用于静态下单金额 - 因为动态下单金额会将余额平均分配。
  
  **注意**
  
  可以使用 `last_stake_amount_min_ratio` 配置最小的最后下单金额 - 默认为 0.5 (50%)。这意味着使用的最小下单金额为 `stake_amount * 0.5`。这避免了非常低的下单金额，这些金额接近该交易对的最小可交易金额，并且可能会被交易所拒绝。
  
  **静态下单金额**
  
  `stake_amount` 配置静态配置了你的机器人将用于每笔交易的本金货币数量。
  
  最小配置值为 0.0001，但是，请检查你使用的交易所的本金货币的交易最小值，以避免出现问题。
  
  此设置与 `max_open_trades` 结合使用。交易中使用的最大资金为 `stake_amount * max_open_trades`。例如，如果机器人最多使用 (0.05 BTC x 3) = 0.15 BTC，假设配置为 `max_open_trades=3` 且 `stake_amount=0.05`。
  
  **注意**
  
  此设置遵循[可用余额配置](https://www.freqtrade.io/en/stable/configuration/#tradable-balance)。
  
  **动态下单金额**
  
  或者，你可以使用动态下单金额，它将使用交易所的可用余额，并将其平均分配给允许的交易数量 (`max_open_trades`)。
  
  要配置此项，请设置 `stake_amount="unlimited"`。我们还建议设置 `tradable_balance_ratio=0.99` (99%) - 以保留最低余额用于最终费用。
  
  在这种情况下，交易金额计算如下：
  
  `currency_balance / (max_open_trades - current_open_trades)`
  
  要允许机器人交易你账户中所有可用的 `stake_currency` (减去 `tradable_balance_ratio`)，请设置
  
  ```json
  "stake_amount" : "unlimited",
  "tradable_balance_ratio": 0.99,
  ```
  
  **复合利润**
  
  此配置将允许根据机器人的表现增加/减少下单金额 (如果机器人亏损，则下单金额较低，如果机器人有获胜记录，则下单金额较高，因为可用余额较高)，并将导致利润复合。
  
  **使用模拟运行模式时**
  
  当将 `"stake_amount" : "unlimited"` 与模拟运行、回测或超参数优化结合使用时，余额将以 `dry_run_wallet` 的本金模拟，该本金将不断变化。因此，将 `dry_run_wallet` 设置为一个合理的值 (例如，BTC 为 0.05 或 0.01，USDT 为 1000 或 100) 很重要，否则，它可能会模拟一次使用 100 BTC (或更多) 或 0.05 USDT (或更少) 的交易 - 这可能与你的实际可用余额不对应，或者小于交易所该本金货币的订单金额的最小限制。
  
  **具有仓位调整的动态下单金额**
  
  当你想将无限下单金额与仓位调整一起使用时，你还必须将 `custom_stake_amount` 实现为返回一个取决于你的策略的值。典型值将在建议下单金额的 25% - 50% 范围内，但这在很大程度上取决于你的策略以及你希望在钱包中留出多少作为仓位调整缓冲区。
  
  例如，如果你的仓位调整假定它可以进行 2 次额外买入，且下单金额相同，则你的缓冲区应为初始建议的无限下单金额的 66.6667%。
  
  或者，如果你的仓位调整假定它可以进行 1 次额外买入，且原始下单金额为 3 倍，则 `custom_stake_amount` 应返回建议下单金额的 25%，并保留 75% 用于以后可能的仓位调整。
  
  **订单使用的价格**
  
  常规订单的价格可以通过交易开仓的 `entry_pricing` 和交易平仓的 `exit_pricing` 参数结构进行控制。价格总是在下单之前获取，可以通过查询交易所行情数据或使用订单簿数据来获取。
  
  **注意**
  
  Freqtrade 使用的订单簿数据是由 ccxt 的函数 `fetch_order_book()` 从交易所检索的数据，即通常是来自 L2 聚合订单簿的数据，而行情数据是由 ccxt 的 `fetch_ticker()`/`fetch_tickers()` 函数返回的结构。有关更多详细信息，请参阅 [ccxt 库文档](https://docs.ccxt.com/#/README?id=order-book-structure)。
  
  **使用市价单**
  
  使用市价单时，请阅读[市价单定价](https://www.freqtrade.io/en/stable/strategy-advanced/#market-order-pricing)部分。
  
  **开仓价格**
  
  **开仓价格方**
  
  配置设置 `entry_pricing.price_side` 定义了机器人在买入时查看订单簿的哪一侧。
  
  以下显示了一个订单簿。
  
  ```
  ...
  103
  102
  101  # 卖单
  -------------当前价差
  99   # 买单
  98
  97
  ...
  ```
  
  如果 `entry_pricing.price_side` 设置
- 如果 `entry_pricing.price_side` 设置为 "bid"，则机器人将使用 99 作为开仓价格。
  同样，如果 `entry_pricing.price_side` 设置为 "ask"，则机器人将使用 101 作为开仓价格。
  
  根据订单方向 (做多/做空)，这将导致不同的结果。因此，我们建议将此配置设置为 "same" 或 "other"。这将导致以下定价矩阵：
  
  | 方向    | 订单  | 设置   | 价格 | 跨越价差 |
  | :------ | :---- | :----- | :--- | :------- |
  | 做多    | 买入  | ask    | 101  | 是       |
  | 做多    | 买入  | bid    | 99   | 否       |
  | 做多    | 买入  | same   | 99   | 否       |
  | 做多    | 买入  | other  | 101  | 是       |
  | 做空    | 卖出  | ask    | 101  | 否       |
  | 做空    | 卖出  | bid    | 99   | 是       |
  | 做空    | 卖出  | same   | 101  | 否       |
  | 做空    | 卖出  | other  | 99   | 是       |
  
  使用订单簿的另一侧通常可以保证更快地成交订单，但机器人最终也可能支付比必要价格更高的价格。即使使用限价买单，也很可能适用吃单方费用而不是挂单方费用。此外，价差 “另一侧” 的价格高于订单簿中 “买单” 侧的价格，因此订单的行为类似于市价单 (但具有最高价格)。
  
  **启用订单簿的开仓价格**
  
  当启用订单簿进行开仓时 (`entry_pricing.use_order_book=True`)，Freqtrade 会从订单簿中获取 `entry_pricing.order_book_top` 条目，并使用在订单簿的配置侧 (`entry_pricing.price_side`) 上指定为 `entry_pricing.order_book_top` 的条目。1 指定订单簿中的最顶层条目，而 2 将使用订单簿中的第二个条目，依此类推。
  
  **未启用订单簿的开仓价格**
  
  以下部分使用 `side` 作为配置的 `entry_pricing.price_side` (默认为 "same")。
  
  当不使用订单簿 (`entry_pricing.use_order_book=False`) 时，如果 `side` 价格低于行情数据中的最后交易价格，Freqtrade 将使用行情数据中的最佳 `side` 价格。否则 (当 `side` 价格高于最后价格时)，它会根据 `entry_pricing.price_last_balance` 计算 `side` 和最后价格之间的价格。
  
  `entry_pricing.price_last_balance` 配置参数控制此行为。值 0.0 将使用 `side` 价格，而 1.0 将使用最后价格，介于两者之间的值在卖价和最后价格之间进行插值。
  
  **检查市场深度**
  
  当启用检查市场深度 (`entry_pricing.check_depth_of_market.enabled=True`) 时，开仓信号会根据每个订单簿侧的订单簿深度 (所有数量的总和) 进行过滤。
  
  然后将订单簿买单 (买入) 侧深度除以订单簿卖单 (卖出) 侧深度，并将得到的差值与 `entry_pricing.check_depth_of_market.bids_to_ask_delta` 参数的值进行比较。仅当订单簿差值大于或等于配置的差值时，才会执行开仓订单。
  
  **注意**
  
  低于 1 的差值表示卖单 (卖出) 订单簿侧深度大于买单 (买入) 订单簿侧的深度，而大于 1 的值表示相反 (买入侧的深度高于卖出侧的深度)。
  
  **平仓价格**
  
  **平仓价格方**
  
  配置设置 `exit_pricing.price_side` 定义了机器人在平仓交易时查看价差的哪一侧。
  
  以下显示了一个订单簿：
  
  ```
  ...
  103
  102
  101  # 卖单
  -------------当前价差
  99   # 买单
  98
  97
  ...
  ```
  
  如果 `exit_pricing.price_side` 设置为 "ask"，则机器人将使用 101 作为平仓价格。
  同样，如果 `exit_pricing.price_side` 设置为 "bid"，则机器人将使用 99 作为平仓价格。
  
  根据订单方向 (做多/做空)，这将导致不同的结果。因此，我们建议将此配置设置为 "same" 或 "other"。这将导致以下定价矩阵：
  
  | 方向    | 订单  | 设置   | 价格 | 跨越价差 |
  | :------ | :---- | :----- | :--- | :------- |
  | 做多    | 卖出  | ask    | 101  | 否       |
  | 做多    | 卖出  | bid    | 99   | 是       |
  | 做多    | 卖出  | same   | 101  | 否       |
  | 做多    | 卖出  | other  | 99   | 是       |
  | 做空    | 买入  | ask    | 101  | 是       |
  | 做空    | 买入  | bid    | 99   | 否       |
  | 做空    | 买入  | same   | 99   | 否       |
  | 做空    | 买入  | other  | 101  | 是       |
  
  **启用订单簿的平仓价格**
  
  当启用订单簿进行平仓时 (`exit_pricing.use_order_book=True`)，Freqtrade 会获取订单簿中的 `exit_pricing.order_book_top` 条目，并使用在配置侧 (`exit_pricing.price_side`) 指定为 `exit_pricing.order_book_top` 的条目作为交易平仓价格。
  
  1 指定订单簿中的最顶层条目，而 2 将使用订单簿中的第二个条目，依此类推。
  
  **未启用订单簿的平仓价格**
  
  以下部分使用 `side` 作为配置的 `exit_pricing.price_side` (默认为 "ask")。
  
  当不使用订单簿 (`exit_pricing.use_order_book=False`) 时，如果 `side` 价格高于行情数据中的最后交易价格，Freqtrade 将使用行情数据中的最佳 `side` 价格。否则 (当 `side` 价格低于最后价格时)，它会根据 `exit_pricing.price_last_balance` 计算 `side` 和最后价格之间的价格。
  
  `exit_pricing.price_last_balance` 配置参数控制此行为。值 0.0 将使用 `side` 价格，而 1.0 将使用最后价格，介于两者之间的值在 `side` 和最后价格之间进行插值。
  
  **市价单定价**
  
  使用市价单时，应将价格配置为使用订单簿的 “正确” 一侧，以允许进行实际的定价检测。假设开仓和平仓都使用市价单，则必须使用类似于以下的配置
  
  ```json
    "order_types": {
      "entry": "market",
      "exit": "market"
      // ...
    },
    "entry_pricing": {
      "price_side": "other",
      // ...
    },
    "exit_pricing":{
      "price_side": "other",
      // ...
    },
  ```
  
  显然，如果只有一侧使用限价单，则可以使用不同的定价组合。
  
  **更多配置详情**
  
  **了解 minimal_roi**
  
  `minimal_roi` 配置参数是一个 JSON 对象，其中键是以分钟为单位的持续时间，值是最小 ROI 作为比率。请参见以下示例：
  
  ```json
  "minimal_roi": {
      "40": 0.0,    // 40 分钟后，如果利润不为负则平仓
      "30": 0.01,   // 30 分钟后，如果有至少 1% 的利润则平仓
      "20": 0.02,   // 20 分钟后，如果有至少 2% 的利润则平仓
      "0":  0.04    // 如果有至少 4% 的利润则立即平仓
  },
  ```
  
  大多数策略文件已经包括了最佳的 `minimal_roi` 值。此参数可以在策略或配置文件中设置。如果你在配置文件中使用它，它将覆盖策略文件中的 `minimal_roi` 值。如果策略和配置中均未设置，则默认使用 `{"0": 10}` (1000% 的利润)，除非你的交易产生 1000% 的利润，否则将禁用最小 ROI。
  
  **强制在特定时间后平仓的特殊情况**
  
  使用 `"<N>": -1` 作为 ROI 是一种特殊情况。这将强制机器人在 N 分钟后平仓，无论它是正收益还是负收益，因此表示时间限制的强制平仓。
  
  **了解 force_entry_enable**
  
  `force_entry_enable` 配置参数启用通过 Telegram 和 REST API 使用强制开仓 (`/forcelong`, `/forceshort`) 命令。出于安全原因，它默认禁用，如果启用，freqtrade 将在启动时显示警告消息。例如，你可以向机器人发送 `/forceenter ETH/BTC`，这将导致 freqtrade 买入该交易对并持有它，直到出现常规的平仓信号 (ROI、止损、`/forceexit`)。
  
  这对于某些策略可能很危险，因此请谨慎使用。
  
  有关用法的详细信息，请参阅 [Telegram 文档](https://www.freqtrade.io/en/stable/telegram/#force-entry)。
  
  **忽略过期的 K 线**
  
  当使用较大的时间周期 (例如 1 小时或更长) 并且使用较低的 `max_open_trades` 值时，一旦交易槽位可用，就可以处理最后一根 K 线。处理最后一根 K 线时，可能会出现不希望在该 K 线上使用买入信号的情况。例如，当你在策略中使用交叉条件时，该点可能已经过去太久而无法在该点开始交易。
  
  在这种情况下，你可以通过将 `ignore_buying_expired_candle_after` 设置为正数来启用忽略超过指定时间段的 K 线的功能，该数字表示买入信号过期之前的秒数。
  
  例如，如果你的策略使用 1 小时时间周期，并且你只想在前 5 分钟内买入新 K 线，则可以将以下配置添加到你的策略中：
  
  ```json
    {
      //...
      "ignore_buying_expired_candle_after": 300,
      // ...
    }
  ```
  
  **注意**
  
  此设置会在每根新 K 线重置，因此它不会阻止粘性信号在它们处于活动状态的第 2 或第 3 根 K 线执行。最好使用仅对一根 K 线有效的 “触发器” 选择器进行买入信号。
  
  **了解 order_types**
  
  `order_types` 配置参数将操作 (开仓、平仓、止损、紧急平仓、强制平仓、强制开仓) 映射到订单类型 (市价、限价等)，并配置交易平台止损，并定义以秒为单位的交易平台止损更新间隔。
  
  这允许使用限价单开仓，使用限价单平仓，并使用市价单创建止损。它还允许将止损设置 “在交易所”，这意味着一旦买单成交，就会立即下达止损订单。
  
  在配置文件中设置的 `order_types` 会覆盖策略中设置的整个值，因此你需要在同一个地方配置整个 `order_types` 字典。
  
  如果配置了此项，则以下 4 个值 (开仓、平仓、止损和 `stoploss_on_exchange`) 必须存在，否则机器人将无法启动。
  
  有关 (`emergency_exit`、`force_exit`、`force_entry`、`stoploss_on_exchange`、`stoploss_on_exchange_interval`、`stoploss_on_exchange_limit_ratio`) 的信息，请参阅[止损文档](https://www.freqtrade.io/en/stable/stoploss/#stoploss-on-exchange)中的[交易平台止损](https://www.freqtrade.io/en/stable/stoploss/#stoploss-on-exchange)
  
  策略语法：
  
  ```python
  order_types = {
      "entry": "limit",
      "exit": "limit",
      "emergency_exit": "market",
      "force_entry": "market",
      "force_exit": "market",
      "stoploss": "market",
      "stoploss_on_exchange": False,
      "stoploss_on_exchange_interval": 60,
      "stoploss_on_exchange_limit_ratio": 0.99,
  }
  ```
  
  配置：
  
  ```json
  "order_types": {
      "entry": "limit",
      "exit": "limit",
      "emergency_exit": "market",
      "force_entry": "market",
      "force_exit": "market",
      "stoploss": "market",
      "stoploss_on_exchange": false,
      "stoploss_on_exchange_interval": 60
  }
  ```
  
  **市价单支持**
  
  并非所有交易所都支持 “市价” 订单。如果你的交易所不支持市价单，将显示以下消息："Exchange <yourexchange> does not support market orders." 并且机器人将拒绝启动。
  
  **使用市价单**
  
  使用市价单时，请仔细阅读[市价单定价](https://www.freqtrade.io/en/stable/strategy-advanced/#market-order-pricing)部分。
  
  **交易平台止损**
  
  `order_types.stoploss_on_exchange_interval` 不是强制性的。如果你不确定自己在做什么，请不要更改其值。有关止损工作原理的更多信息，请参阅[止损文档](https://www.freqtrade.io/en/stable/stoploss/)。
  
  如果启用了 `order_types.stoploss_on_exchange` 并且在交易所手动取消了止损，则机器人将创建一个新的止损订单。
  
  **警告：`order_types.stoploss_on_exchange` 失败**
  
  如果由于某种原因创建交易平台止损失败，则会启动 “紧急平仓”。默认情况下，这将使用市价单平仓交易。可以通过在 `order_types` 字典中设置 `emergency_exit` 值来更改紧急平仓的订单类型 - 但是，不建议这样做。
  
  **了解 order_time_in_force**
  
  `order_time_in_force` 配置参数定义了订单在交易所执行的策略。常用的三种 time in force 是：
  
  **GTC (Good Till Canceled):**
  
  这通常是默认的 time in force。这意味着订单将保留在交易所，直到它被用户取消。它可以完全或部分成交。如果部分成交，剩余部分将保留在交易所，直到取消。
  
  **FOK (Fill Or Kill):**
  
  这意味着如果订单没有立即且完全执行，则它将被交易所取消。
  
  **IOC (Immediate Or Canceled):**
  
  它与 FOK ( উপরে) 相同，只是它可以部分成交。剩余部分由交易所自动取消。
  
  **PO (Post only):**
  
  仅挂单订单。订单要么作为挂单订单下单，要么被取消。这意味着订单必须至少在未成交状态下在订单簿上放置一段时间。
  
  **`time_in_force` 配置**
  
  `order_time_in_force` 参数包含一个带有开仓和出仓 time in force 策略值的字典。这可以在配置文件或策略中设置。在配置文件中设置的值将覆盖策略中设置的值。
  
  可能的值为：GTC (默认)、FOK 或 IOC。
  
  ```json
  "order_time_in_force": {
      "entry": "GTC",
      "exit": "GTC"
  },
  ```
  
  **警告**
  
  这是一项正在进行的工作。目前，它仅支持 binance、gate 和 kucoin。除非你知道自己在做什么并且已经研究了对你的特定交易所使用不同值的影响，否则请不要更改默认值。
  
  **法币转换**
  
  Freqtrade 使用 Coingecko API 将币值转换为其对应的法定货币值，以便在 Telegram 报告中显示。法定货币可以在配置文件中设置为 `fiat_display_currency`。
  
  从配置中完全删除 `fiat_display_currency` 将跳过初始化 coingecko，并且不会显示任何法定货币转换。这对机器人的正常运行没有影响。
  
  **`fiat_display_currency` 可以使用哪些值？**
  
  `fiat_display_currency` 配置参数设置用于将币值转换为机器人的 Telegram 报告中的法币的基础货币。
  
  有效值为：
  
  `"AUD", "BRL", "CAD", "CHF", "CLP", "CNY", "CZK", "DKK", "EUR", "GBP", "HKD", "HUF", "IDR", "ILS", "INR", "JPY", "KRW", "MXN", "MYR", "NOK", "NZD", "PHP", "PKR", "PLN", "RUB", "SEK", "SGD", "THB", "TRY", "TWD", "ZAR", "USD"`
  
  除了法定货币之外，还支持一系列加密货币。
  
  有效值为：
  
  `"BTC", "ETH", "XRP", "LTC", "BCH", "BNB"`
  
  **Coingecko 速率限制问题**
  
  在某些 IP 范围内，coingecko 严重限制了速率。在这种情况下，你可能需要将你的 coingecko API 密钥添加到配置中。
  
  ```json
  {
      "fiat_display_currency": "USD",
      "coingecko": {
          "api_key": "your-api",
          "is_demo": true
      }
  }
  ```
  
  Freqtrade 支持演示版和专业版 coingecko API 密钥。
  
  Coingecko API 密钥**不是**机器人正常运行所必需的。它仅用于在 Telegram 报告中将币值转换为法定货币，这通常在没有 API 密钥的情况下也可以使用。
  
  **使用交易所 Websocket**
  
  Freqtrade 可以通过 ccxt.pro 使用 websocket。
  
  Freqtrade 旨在确保数据始终可用。如果 websocket 连接失败 (或被禁用)，机器人将回退到 REST API 调用。
  
  如果你遇到你怀疑是由 websocket 引起的问题，你可以通过 `exchange.enable_ws` 设置禁用它们，该设置默认为 `true`。
  
  ```json
  "exchange": {
      // ...
      "enable_ws": false,
      // ...
  }
  ```
  
  如果你需要使用代理，请参阅[代理](https://www.freqtrade.io/en/stable/configuration/#using-a-proxy-with-freqtrade)部分了解更多信息。
  
  **推出**
  
  我们正在缓慢地实施此功能，以确保你的机器人的稳定性。目前，使用仅限于 ohlcv 数据流。它也仅限于少数交易所，新交易所将持续添加。
  
  **使用模拟运行模式**
  
  我们建议在模拟运行模式下启动机器人，以查看你的机器人的行为方式以及你的策略的性能。在模拟运行模式下，机器人不会动用你的资金。它只运行实时模拟，而不会在交易所创建交易。
  
  *   编辑你的 `config.json` 配置文件。
  *   将 `dry_run` 切换为 `true` 并指定 `db_url` 作为持久性数据库。
  
  ```json
  "dry_run": true,
  "db_url": "sqlite:///tradesv3.dryrun.sqlite",
  ```
  
  *   删除你的交易所 API 密钥和密钥 (将它们更改为空值或伪造的凭据)：
  
  ```json
  "exchange": {
      "name": "binance",
      "key": "key",
      "secret": "secret",
      ...
  }
  ```
  
  一旦你对在模拟运行模式下运行的机器人性能感到满意，你就可以将其切换到生产模式。
  
  **注意**
  
  在模拟运行模式下可以使用模拟钱包，并将假定起始资金为 `dry_run_wallet` (默认为 1000)。
  
  **模拟运行的注意事项**
  
  *   可以提供也可以不提供 API 密钥。在模拟运行模式下，仅在交易所执行只读操作 (即不改变账户状态的操作)。
  *   钱包 (/balance) 是根据 `dry_run_wallet` 模拟的。
  *   订单是模拟的，不会发布到交易所。
  *   市价单在下单时根据订单簿成交量成交，最大滑点为 5%。
  *   限价单一旦价格达到定义的水平就会成交 - 或者根据 `unfilledtimeout` 设置超时。
  *   如果限价单的价格超过 1%，限价单将被转换为市价单，并将根据常规市价单规则立即成交 (请参阅上面关于市价单的说明)。
  *   结合 `stoploss_on_exchange`，假定止损价格已成交。
  *   在机器人重新启动后，未结订单 (不是交易，它们存储在数据库中) 将保持未结状态，并假设它们在离线时未成交。
  
  **切换到生产模式**
  
  在生产模式下，机器人将动用你的资金。请小心，因为错误的策略可能会损失你所有的资金。在生产模式下运行时，请注意你在做什么。
  
  当切换到生产模式时，请确保使用不同/新的数据库，以避免模拟运行交易扰乱你的交易所资金并最终影响你的统计数据。
  
  **设置你的交易所账户**
  
  你将需要从交易所网站创建 API 密钥 (通常你会获得密钥和密钥，某些交易所需要额外的密码)，你将需要将其插入到配置中的相应字段中，或者在 `freqtrade new-config` 命令询问时插入。API 密钥通常仅用于实盘交易 (用真钱交易，机器人以 “生产模式” 运行，在交易所执行实际订单)，并且在机器人以模拟运行 (交易模拟) 模式运行时不需要。当你在模拟运行模式下设置机器人时，你可以用空值填写这些字段。
  
  **要将你的机器人切换到生产模式**
  
  编辑你的 `config.json` 文件。
  
  将 `dry_run` 切换为 `false`，如果设置了数据库 URL，请不要忘记调整它：
  
  ```json
  "dry_run": false,
  ```
  
  插入你的交易所 API 密钥 (将它们更改为伪造的 API 密钥)：
  
  ```json
  {
      "exchange": {
          "name": "binance",
          "key": "af8ddd35195e9dc500b9a6f799f6f5c93d89193b",
          "secret": "08a9dc6db3d7b53e1acebd9275677f4b0a04f1a5",
          //"password": "", // 可选，并非所有交易所都需要)
          // ...
      }
      //...
  }
  ```
  
  你还应该确保阅读文档的[交易所](https://www.freqtrade.io/en/stable/exchange/)部分，以了解特定于你的交易所的潜在配置详细信息。
  
  **保守你的机密信息**
  
  为了保守你的机密信息，我们建议使用第二个配置来保存你的 API 密钥。只需在新配置文件 (例如 `config-private.json`) 中使用上面的代码片段，并将你的设置保存在此文件中。然后，你可以使用 `freqtrade trade --config user_data/config.json --config user_data/config-private.json <...>` 启动机器人，以加载你的密钥。
  
  **切勿**与任何人共享你的私有配置文件或你的交易所密钥！
  
  **将代理与 Freqtrade 一起使用**
  
  要将代理与 freqtrade 一起使用，请将环境变量 "HTTP_PROXY" 和 "HTTPS_PROXY" 设置为适当的值来导出你的代理设置。这将把代理设置应用于所有内容 (电报、coingecko 等)，交易所请求除外。
  
  ```bash
  export HTTP_PROXY="http://addr:port"
  export HTTPS_PROXY="http://addr:port"
  freqtrade
  ```
  
  **代理交易所请求**
  
  要将代理用于交易所连接 - 你将必须将代理定义为 ccxt 配置的一部分。
  
  ```json
  {
    "exchange": {
      "ccxt_config": {
        "httpsProxy": "http://addr:port",
        "wsProxy": "http://addr:port",
      }
    }
  }
  ```
  
  有关可用代理类型的更多信息，请参阅 [ccxt 代理文档](https://docs.ccxt.com/#/README?id=proxy)。