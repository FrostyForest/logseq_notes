- #crypto #freqtrade
- ## 高级策略
  
  本页介绍了一些可用于策略的高级概念。如果你刚开始，请先熟悉 Freqtrade 基础知识和策略定制中描述的方法。
  
  此处描述的方法的调用顺序在 [bot 执行逻辑](https://www.freqtrade.io/en/stable/bot-basics/#bot-execution-logic)中有介绍。这些文档也有助于确定哪种方法最适合你的定制需求。
  
  **注意**
  
  仅当策略使用它们时才应实现回调方法。
  
  **提示**
  
  通过运行 `freqtrade new-strategy --strategy MyAwesomeStrategy --template advanced` 开始使用包含所有可用回调方法的策略模板。
  
  **存储信息 (持久化)**
  
  Freqtrade 允许在数据库中存储/检索与特定交易关联的用户自定义信息。
  
  使用交易对象，可以使用 `trade.set_custom_data(key='my_key', value=my_value)` 存储信息，并使用 `trade.get_custom_data(key='my_key')` 检索信息。每个数据条目都与一个交易和一个用户提供的键 (字符串类型) 相关联。这意味着这只能在还提供交易对象的回调函数中使用。
  
  为了能够将数据存储在数据库中，freqtrade 必须序列化数据。这是通过将数据转换为 JSON 格式的字符串来完成的。Freqtrade 将尝试在检索时反转此操作，因此从策略的角度来看，这应该无关紧要。
  
  ```python
  from freqtrade.persistence import Trade
  from datetime import timedelta
  
  class AwesomeStrategy(IStrategy):
  
    def bot_loop_start(self, **kwargs) -> None:
        for trade in Trade.get_open_order_trades():
            fills = trade.select_filled_orders(trade.entry_side)
            if trade.pair == 'ETH/USDT':
                trade_entry_type = trade.get_custom_data(key='entry_type')
                if trade_entry_type is None:
                    trade_entry_type = 'breakout' if 'entry_1' in trade.enter_tag else 'dip'
                elif fills > 1:
                    trade_entry_type = 'buy_up'
                trade.set_custom_data(key='entry_type', value=trade_entry_type)
        return super().bot_loop_start(**kwargs)
  
    def adjust_entry_price(self, trade: Trade, order: Order | None, pair: str,
                           current_time: datetime, proposed_rate: float, current_order_rate: float,
                           entry_tag: str | None, side: str, **kwargs) -> float:
        # 在 BTC/USDT 交易对开仓触发后的前 10 分钟内，限价单使用并跟随 SMA200 作为价格目标。
        if (
            pair == 'BTC/USDT'
            and entry_tag == 'long_sma200'
            and side == 'long'
            and (current_time - timedelta(minutes=10)) > trade.open_date_utc
            and order.filled == 0.0
        ):
            dataframe, _ = self.dp.get_analyzed_dataframe(pair=pair, timeframe=self.timeframe)
            current_candle = dataframe.iloc[-1].squeeze()
            # 存储有关开仓调整的信息
            existing_count = trade.get_custom_data('num_entry_adjustments', default=0)
            if not existing_count:
                existing_count = 1
            else:
                existing_count += 1
            trade.set_custom_data(key='num_entry_adjustments', value=existing_count)
  
            # 调整订单价格
            return current_candle['sma_200']
  
        # 默认：保持现有订单
        return current_order_rate
  
    def custom_exit(self, pair: str, trade: Trade, current_time: datetime, current_rate: float, current_profit: float, **kwargs):
  
        entry_adjustment_count = trade.get_custom_data(key='num_entry_adjustments')
        trade_entry_type = trade.get_custom_data(key='entry_type')
        if entry_adjustment_count is None:
            if current_profit > 0.01 and (current_time - timedelta(minutes=100) > trade.open_date_utc):
                return True, 'exit_1'
        else:
            if entry_adjustment_count > 0 and if current_profit > 0.05:
                return True, 'exit_2'
            if trade_entry_type == 'breakout' and current_profit > 0.1:
                return True, 'exit_3
  
        return False, None
  ```
  
  以上是一个简单的示例 - 有更简单的方法来检索交易数据，如开仓调整。
  
  **注意**
  
  建议使用简单的数据类型 [bool, int, float, str] 以确保在序列化需要存储的数据时不会出现问题。存储大数据块可能会导致意外的副作用，例如数据库变大 (因此也会变慢)。
  
  **不可序列化的数据**
  
  如果提供的数据无法序列化，则会记录一条警告，并且指定键的条目将包含 `None` 作为数据。
  
  **所有属性**
  
  **存储信息 (非持久化)**
  
  **已弃用**
  
  此存储信息的方法已弃用，我们建议不要使用非持久化存储。
  请改用[持久化存储](https://www.freqtrade.io/en/stable/strategy-advanced/#storing-information-persistent)。
  
  因此，它的内容已被折叠。
  
  **存储信息**
  
  **数据帧访问**
  
  你可以通过从数据提供程序查询数据帧来访问各种策略函数中的数据帧。
  
  ```python
  from freqtrade.exchange import timeframe_to_prev_date
  
  class AwesomeStrategy(IStrategy):
    def confirm_trade_exit(self, pair: str, trade: 'Trade', order_type: str, amount: float,
                           rate: float, time_in_force: str, exit_reason: str,
                           current_time: 'datetime', **kwargs) -> bool:
        # 获取交易对数据帧。
        dataframe, _ = self.dp.get_analyzed_dataframe(pair, self.timeframe)
  
        # 获取最后一个可用的 K 线。不要使用 current_time 来查找最新的 K 线，因为
        # current_time 指向当前不完整的 K 线，其数据不可用。
        last_candle = dataframe.iloc[-1].squeeze()
        # <...>
  
        # 在模拟/实盘运行中，交易开仓日期将不匹配 K 线开盘日期，因此必须
        # 四舍五入。
        trade_date = timeframe_to_prev_date(self.timeframe, trade.open_date_utc)
        # 查找交易 K 线。
        trade_candle = dataframe.loc[dataframe['date'] == trade_date]
        # 对于刚刚开仓的交易，trade_candle 可能为空，因为它仍然不完整。
        if not trade_candle.empty:
            trade_candle = trade_candle.squeeze()
            # <...>
  ```
  
  **使用 .iloc[-1]**
  
  你可以在此处使用 `.iloc[-1]`，因为 `get_analyzed_dataframe()` 仅返回回测允许看到的 K 线。这在 `populate_*` 方法中不起作用，因此请确保不要在该区域使用 `.iloc[]`。此外，这仅从版本 2021.5 开始有效。
  
  **开仓标签**
  
  当你的策略有多个买入信号时，你可以命名触发的信号。然后你可以在 `custom_exit` 中访问你的买入信号
  
  ```python
  def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe.loc[
        (
            (dataframe['rsi'] < 35) &
            (dataframe['volume'] > 0)
        ),
        ['enter_long', 'enter_tag']] = (1, 'buy_signal_rsi')
  
    return dataframe
  
  def custom_exit(self, pair: str, trade: Trade, current_time: datetime, current_rate: float,
                current_profit: float, **kwargs):
    dataframe, _ = self.dp.get_analyzed_dataframe(pair, self.timeframe)
    last_candle = dataframe.iloc[-1].squeeze()
    if trade.enter_tag == 'buy_signal_rsi' and last_candle['rsi'] > 80:
        return 'sell_signal_rsi'
    return None
  ```
  
  **注意**
  
  `enter_tag` 限制为 100 个字符，其余数据将被截断。
  
  **警告**
  
  只有一个 `enter_tag` 列，用于做多和做空交易。因此，此列必须被视为 “最后写入获胜” (毕竟它只是一个数据帧列)。在复杂的情况下，多个信号发生冲突 (或者如果信号根据不同的条件再次停用)，这可能会导致应用了错误标签的开仓信号的奇怪结果。这些结果是策略覆盖先前标签的结果 - 最后一个标签将 “粘滞” 并将是 freqtrade 将使用的标签。
  
  **平仓标签**
  
  类似于开仓标签，你还可以指定平仓标签。
  
  ```python
  def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    dataframe.loc[
        (
            (dataframe['rsi'] > 70) &
            (dataframe['volume'] > 0)
        ),
        ['exit_long', 'exit_tag']] = (1, 'exit_rsi')
  
    return dataframe
  ```
  
  然后，提供的平仓标签将用作卖出原因 - 并在回测结果中如此显示。
  
  **注意**
  
  `exit_reason` 限制为 100 个字符，其余数据将被截断。
  
  **策略版本**
  
  你可以使用 "version" 方法实现自定义策略版本控制，并返回你希望此策略具有的版本。
  
  ```python
  def version(self) -> str:
    """
    返回策略的版本。
    """
    return "1.1"
  ```
  
  **注意**
  
  你应该确保与此一起实施适当的版本控制 (如 git 仓库)，因为 freqtrade 不会保留你的策略的历史版本，因此用户有责任能够最终回滚到策略的先前版本。
  
  **派生策略**
  
  策略可以从其他策略派生。这避免了自定义策略代码的重复。你可以使用此技术覆盖主策略的一小部分，而其余部分保持不变：
  
  `user_data/strategies/myawesomestrategy.py`
  
  ```python
  class MyAwesomeStrategy(IStrategy):
    ...
    stoploss = 0.13
    trailing_stop = False
    # 所有其他属性和方法都在这里，因为它们
    # 应该在任何自定义策略中...
    ...
  ```
  
  `user_data/strategies/MyAwesomeStrategy2.py`
  
  ```python
  from myawesomestrategy import MyAwesomeStrategy
  class MyAwesomeStrategy2(MyAwesomeStrategy):
    # 覆盖某些内容
    stoploss = 0.08
    trailing_stop = True
  ```
  
  属性和方法都可以被覆盖，以你想要的方式改变原始策略的行为。
  
  虽然将子类保留在同一个文件中在技术上是可行的，但它可能会导致超参数优化参数文件出现一些问题，因此我们建议使用单独的策略文件，并如上所示导入父策略。
  
  **嵌入策略**
  
  Freqtrade 为你提供了一种将策略嵌入到配置文件中的简单方法。这是通过利用 BASE64 编码并在你选择的配置文件中的策略配置字段中提供此字符串来实现的。
  
  **将字符串编码为 BASE64**
  
  这是一个快速示例，说明如何在 python 中生成 BASE64 字符串
  
  ```python
  from base64 import urlsafe_b64encode
  
  with open(file, 'r') as f:
    content = f.read()
  content = urlsafe_b64encode(content.encode('utf-8'))
  ```
  
  变量 'content' 将包含 BASE64 编码形式的策略文件。现在可以在你的配置文件中进行如下设置
  
  ```json
  "strategy": "NameOfStrategy:BASE64String"
  ```
  
  请确保 'NameOfStrategy' 与策略名称相同！
  
  **性能警告**
  
  执行策略时，有时会在日志中看到以下内容
  
  ```
  PerformanceWarning: DataFrame is highly fragmented.
  ```
  
  这是来自 pandas 的警告，正如警告继续说的那样：使用 `pd.concat(axis=1)`。这可能会产生轻微的性能影响，通常只在超参数优化期间 (优化指标时) 可见。
  
  例如：
  
  ```python
  for val in self.buy_ema_short.range:
    dataframe[f'ema_short_{val}'] = ta.EMA(dataframe, timeperiod=val)
  ```
  
  应该改写为
  
  ```python
  frames = [dataframe]
  for val in self.buy_ema_short.range:
    frames.append(DataFrame({
        f'ema_short_{val}': ta.EMA(dataframe, timeperiod=val)
    }))
  
  # 合并所有数据帧，并重新分配原始数据帧列
  dataframe = pd.concat(frames, axis=1)
  ```
  
  然而，Freqtrade 也通过在 `populate_indicators()` 方法之后立即在数据帧上运行 `dataframe.copy()` 来对抗这种情况 - 因此这方面的性能影响应该很小甚至不存在。