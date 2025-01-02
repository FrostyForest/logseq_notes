- #freqtrade
- ## 策略回调函数
  
  虽然主要的策略函数 (`populate_indicators()`, `populate_entry_trend()`, `populate_exit_trend()`) 应该以向量化的方式使用，并且在回测期间只调用一次，但回调函数会在 “需要时” 被调用。
  
  因此，你应该避免在回调函数中进行繁重的计算，以避免在操作过程中出现延迟。根据使用的回调函数，它们可能会在开仓/平仓时被调用，或者在交易的整个持续时间内被调用。
  
  当前可用的回调函数：
  
  *   `bot_start()`
  *   `bot_loop_start()`
  *   `custom_stake_amount()`
  *   `custom_exit()`
  *   `custom_stoploss()`
  *   `custom_entry_price()` 和 `custom_exit_price()`
  *   `check_entry_timeout()` 和 `check_exit_timeout()`
  *   `confirm_trade_entry()`
  *   `confirm_trade_exit()`
  *   `adjust_trade_position()`
  *   `adjust_entry_price()`
  *   `leverage()`
  *   `order_filled()`
  
  **回调函数调用顺序**
  
  你可以在 [bot-basics](https://www.freqtrade.io/en/stable/bot-basics/) 中找到回调函数的调用顺序。
  
  **策略所需的导入**
  
  创建策略时，你需要导入必要的模块和类。策略需要以下导入：
  
  默认情况下，我们建议以下导入作为策略的基准：这将涵盖 Freqtrade 函数正常工作所需的所有导入。显然，你可以根据需要添加更多导入。
  
  ```python
  # flake8: noqa: F401
  # isort: skip_file
  # --- 不要删除这些导入 ---
  import numpy as np
  import pandas as pd
  from datetime import datetime, timedelta, timezone
  from pandas import DataFrame
  from typing import Dict, Optional, Union, Tuple
  
  from freqtrade.strategy import (
    IStrategy,
    Trade,
    Order,
    PairLocks,
    informative,  # @informative 装饰器
    # Hyperopt 参数
    BooleanParameter,
    CategoricalParameter,
    DecimalParameter,
    IntParameter,
    RealParameter,
    # 时间周期助手
    timeframe_to_minutes,
    timeframe_to_next_date,
    timeframe_to_prev_date,
    # 策略助手函数
    merge_informative_pair,
    stoploss_from_absolute,
    stoploss_from_open,
  )
  
  # --------------------------------
  # 在此处添加你的库导入
  import talib.abstract as ta
  from technical import qtpylib
  ```
  
  **Bot 启动**
  
  一个简单的回调函数，在策略加载后只调用一次。这可以用于执行只需执行一次的操作，并在数据提供程序和钱包设置之后运行。
  
  ```python
  import requests
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    def bot_start(self, **kwargs) -> None:
        """
        在机器人实例化后只调用一次。
        :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
        """
        if self.config["runmode"].value in ("live", "dry_run"):
            # 使用 self.* 将其分配给类
            # 然后可以被 populate_* 方法使用
            self.custom_remote_data = requests.get("https://some_remote_source.example.com")
  ```
  
  在超参数优化期间，这只在启动时运行一次。
  
  **Bot 循环开始**
  
  一个简单的回调函数，在模拟/实盘模式下每次机器人节流迭代开始时 (大约每 5 秒，除非另有配置) 或在回测/超参数优化模式下每个 K 线调用一次。这可以用于执行独立于交易对的计算 (适用于所有交易对)，加载外部数据等。
  
  ```python
  # 默认导入
  import requests
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    def bot_loop_start(self, current_time: datetime, **kwargs) -> None:
        """
        在机器人迭代 (一个循环) 开始时调用。
        可能用于执行独立于交易对的任务
        (例如收集一些远程资源进行比较)
        :param current_time: datetime 对象，包含当前日期时间
        :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
        """
        if self.config["runmode"].value in ("live", "dry_run"):
            # 使用 self.* 将其分配给类
            # 然后可以被 populate_* 方法使用
            self.remote_data = requests.get("https://some_remote_source.example.com")
  ```
  
  **下单金额管理**
  
  在开仓之前调用，可以在下单新交易时管理你的仓位大小。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
    def custom_stake_amount(self, pair: str, current_time: datetime, current_rate: float,
                            proposed_stake: float, min_stake: float | None, max_stake: float,
                            leverage: float, entry_tag: str | None, side: str,
                            **kwargs) -> float:
  
        dataframe, _ = self.dp.get_analyzed_dataframe(pair=pair, timeframe=self.timeframe)
        current_candle = dataframe.iloc[-1].squeeze()
  
        if current_candle["fastk_rsi_1h"] > current_candle["fastd_rsi_1h"]:
            if self.config["stake_amount"] == "unlimited":
                # 在有利条件下，当处于复利模式时，使用全部可用钱包。
                return max_stake
            else:
                # 在有利条件下复合利润，而不是使用固定的下单金额。
                return self.wallets.get_total_stake_amount() / self.config["max_open_trades"]
  
        # 使用默认下单金额。
        return proposed_stake
  ```
  
  如果你的代码引发异常，Freqtrade 将回退到 `proposed_stake` 值。异常本身将被记录。
  
  **提示**
  
  你不必确保 `min_stake <= 返回值 <= max_stake`。交易将成功，因为返回值将被限制在支持的范围内，并且此操作将被记录。
  
  **提示**
  
  返回 0 或 `None` 将阻止下单。
  
  **自定义平仓信号**
  
  对未结交易每次节流迭代 (大约每 5 秒) 调用一次，直到交易关闭。
  
  允许定义自定义平仓信号，指示应卖出指定仓位。当我们需要为每个单独的交易自定义平仓条件时，或者如果你需要交易数据来做出平仓决定时，这非常有用。
  
  例如，你可以使用 `custom_exit()` 实现 1:2 的风险回报 ROI。
  
  但是，不建议使用 `custom_exit()` 信号代替止损。在这方面，这是一种不如使用 `custom_stoploss()` 的方法 - 后者还允许你将止损保持在交易所。
  
  **注意**
  
  从此方法返回 (非空) 字符串或 `True` 等同于在指定时间在 K 线上设置平仓信号。如果已经设置了平仓信号，或者如果禁用了平仓信号 (`use_exit_signal=False`)，则不会调用此方法。字符串的最大长度为 64 个字符。超过此限制将导致消息被截断为 64 个字符。`custom_exit()` 将忽略 `exit_profit_only`，并且除非 `use_exit_signal=False`，否则将始终被调用，即使有新的开仓信号。
  
  一个示例，说明我们如何根据当前利润使用不同的指标，以及如何平仓持仓超过一天的交易：
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
    def custom_exit(self, pair: str, trade: Trade, current_time: datetime, current_rate: float,
                    current_profit: float, **kwargs):
        dataframe, _ = self.dp.get_analyzed_dataframe(pair, self.timeframe)
        last_candle = dataframe.iloc[-1].squeeze()
  
        # 利润超过 20% 时，当 rsi < 80 时卖出
        if current_profit > 0.2:
            if last_candle["rsi"] < 80:
                return "rsi_below_80"
  
        # 利润在 2% 到 10% 之间，如果 EMA-long 高于 EMA-short 则卖出
        if 0.02 < current_profit < 0.1:
            if last_candle["emalong"] > last_candle["emashort"]:
                return "ema_long_below_80"
  
        # 如果持仓超过一天，则以亏损卖出任何仓位。
        if current_profit < 0.0 and (current_time - trade.open_date_utc).days >= 1:
            return "unclog"
  ```
  
  有关在策略回调函数中使用数据帧的更多信息，请参阅[数据帧访问](https://www.freqtrade.io/en/stable/strategy-advanced/#dataframe-access)。
  
  **自定义止损**
  
  对未结交易每次迭代 (大约每 5 秒) 调用一次，直到交易关闭。
  
  必须通过在策略对象上设置 `use_custom_stoploss=True` 来启用自定义止损方法的使用。
  
  止损价格只能向上移动 - 如果从 `custom_stoploss` 返回的止损值会导致止损价格低于之前设置的价格，它将被忽略。传统的止损值用作绝对下限，并在第一次为交易调用此方法之前设置为初始止损，并且仍然是强制性的。
  
  由于自定义止损充当常规的、不断变化的止损，它将表现得类似于 `trailing_stop` - 并且由于此原因而平仓的交易将具有 `trailing_stop_loss` 的 `exit_reason`。
  
  该方法必须返回一个止损值 (浮点数/数字) 作为当前价格的百分比。例如，如果 `current_rate` 为 200 美元，则返回 0.02 将设置止损价格为低 2% 的 196 美元。在回测期间，`current_rate` (和 `current_profit`) 是根据 K 线的高点 (或空头交易的低点) 提供的 - 而生成的止损是根据 K 线的低点 (或空头交易的高点) 进行评估的。
  
  返回值的绝对值被使用 (符号被忽略)，因此返回 0.05 或 -0.05 具有相同的结果，即止损比当前价格低 5%。返回 `None` 将被解释为 “不希望更改”，并且是你想不修改止损时返回的唯一安全方式。`NaN` 和 `inf` 值被视为无效，将被忽略 (与 `None` 相同)。
  
  交易平台的止损工作方式类似于 `trailing_stop`，并且交易平台的止损会按照 `stoploss_on_exchange_interval` 中的配置进行更新 (有关交易平台止损的更多详细信息)。
  
  **使用日期**
  
  所有基于时间的计算都应基于 `current_time` - 不鼓励使用 `datetime.now()` 或 `datetime.utcnow()`，因为这将破坏回测支持。
  
  **追踪止损**
  
  建议在使用自定义止损值时禁用 `trailing_stop`。两者可以协同工作，但你可能会遇到追踪止损将价格推高而你的自定义函数不希望这样的情况，从而导致冲突的行为。
  
  **在仓位调整后调整止损**
  
  根据你的策略，你可能会遇到在仓位调整后需要双向调整止损的情况。为此，freqtrade 将在订单成交后使用 `after_fill=True` 进行额外调用，这将允许策略向任何方向移动止损 (也扩大止损和当前价格之间的差距，否则这是禁止的)。
  
  **向后兼容**
  
  只有当 `after_fill` 参数是你的 `custom_stoploss` 函数定义的一部分时，才会进行此调用。因此，这将不会影响 (并因此使之感到意外) 现有的、正在运行的策略。
  
  **自定义止损示例**
  
  下一节将展示一些关于 `custom_stoploss` 函数可能实现的示例。当然，还有更多可能的事情，所有示例都可以随意组合。
  
  **通过自定义止损进行追踪止损**
  
  要模拟 4% 的常规追踪止损 (落后于达到的最高价格 4%)，你可以使用以下非常简单的方法：
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
        """
        自定义止损逻辑，返回相对于 current_rate 的新距离 (作为比率)。
        例如，返回 -0.05 将创建比 current_rate 低 5% 的止损。
        自定义止损永远不能低于 self.stoploss，后者用作硬最大损失。
  
        有关完整文档，请访问 https://www.freqtrade.io/en/latest/strategy-advanced/
  
        当策略未实现时，返回初始止损值。
        仅当 use_custom_stoploss 设置为 True 时调用。
  
        :param pair: 当前分析的交易对
        :param trade: 交易对象。
        :param current_time: datetime 对象，包含当前日期时间
        :param current_rate: 根据 exit_pricing 中的定价设置计算的汇率。
        :param current_profit: 根据 current_rate 计算的当前利润 (作为比率)。
        :param after_fill: 如果在订单成交后调用止损，则为 True。
        :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
        :return float: 新的止损值，相对于 current_rate
        """
        return -0.04
  ```
  
  **基于时间的追踪止损**
  
  最初 60 分钟使用初始止损，此后更改为 10% 的追踪止损，2 小时 (120 分钟) 后我们使用 5% 的追踪止损。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
  
        # 确保你首先拥有最长的间隔 - 这些条件从上到下进行评估。
        if current_time - timedelta(minutes=120) > trade.open_date_utc:
            return -0.05
        elif current_time - timedelta(minutes=60) > trade.open_date_utc:
            return -0.10
        return None
  ```
  
  **具有成交后调整功能的基于时间的追踪止损**
  
  最初 60 分钟使用初始止损，此后更改为 10% 的追踪止损，2 小时 (120 分钟) 后我们使用 5% 的追踪止损。如果成交了额外的订单，则将止损设置为比新的 `open_rate` (所有开仓订单的平均值) 低 -10%。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
  
        if after_fill:
            # 在额外订单之后，从比新开盘价低 10% 的止损开始
            return stoploss_from_open(0.10, current_profit, is_short=trade.is_short, leverage=trade.leverage)
        # 确保你首先拥有最长的间隔 - 这些条件从上到下进行评估。
        if current_time - timedelta(minutes=120) > trade.open_date_utc:
            return -0.05
        elif current_time - timedelta(minutes=60) > trade.open_date_utc:
            return -0.10
        return None
  ```
  
  **每个交易对不同的止损**
  
  根据交易对使用不同的止损。在此示例中，我们将 ETH/BTC 和 XRP/BTC 的最高价格追踪 10% 的追踪止损，LTC/BTC 的追踪止损为 5%，所有其他交易对的追踪止损为 15%。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
  
        if pair in ("ETH/BTC", "XRP/BTC"):
            return -0.10
        elif pair in ("LTC/BTC"):
            return -0.05
        return -0.15
  ```
  
  **带正偏移的追踪止损**
  
  在利润高于 4% 之前使用初始止损，然后使用当前利润的 50% 的追踪止损，最小值为 2.5%，最大值为 5%。
  
  请注意，止损只能增加，低于当前止损的值将被忽略。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
  
        if current_profit < 0.04:
            return None # 返回 None 以保持使用初始止损
  
        # 达到所需偏移后，允许止损追踪利润的一半
        desired_stoploss = current_profit / 2
  
        # 使用最小 2.5% 和最大 5%
        return max(min(desired_stoploss, 0.05), 0.025)
  ```
  
  **阶梯式止损**
  
  此示例根据当前利润设置固定的止损价格水平，而不是持续追踪当前价格。
  
  *   在达到 20% 利润之前使用常规止损
  *   一旦利润 > 20% - 将止损设置为比开盘价高 7%。
  *   一旦利润 > 25% - 将止损设置为比开盘价高 15%。
  *   一旦利润 > 40% - 将止损设置为比开盘价高 25%。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
  
        # 从最高到最低评估，以便使用尽可能高的止损
        if current_profit > 0.40:
            return stoploss_from_open(0.25, current_profit, is_short=trade.is_short, leverage=trade.leverage)
        elif current_profit > 0.25:
            return stoploss_from_open(0.15, current_profit, is_short=trade.is_short, leverage=trade.leverage)
        elif current_profit > 0.20:
            return stoploss_from_open(0.07, current_profit, is_short=trade.is_short, leverage=trade.leverage)
  
        # 返回最大止损值，保持当前止损价格不变
        return None
  ```
  
  **使用数据帧中的指标的自定义止损示例**
  
  绝对止损值可以从存储在数据帧中的指标中导出。示例使用价格下方的抛物线 SAR 作为止损。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        # <...>
        dataframe["sar"] = ta.SAR(dataframe)
  
    use_custom_stoploss = True
  
    def custom_stoploss(self, pair: str, trade: Trade, current_time: datetime,
                        current_rate: float, current_profit: float, after_fill: bool,
                        **kwargs) -> float | None:
  
        dataframe, _ = self.dp.get_analyzed_dataframe(pair, self.timeframe)
        last_candle = dataframe.iloc[-1].squeeze()
  
        # 使用抛物线 SAR 作为绝对止损价格
        stoploss_price = last_candle["sar"]
  
        # 将绝对价格转换为相对于 current_rate 的百分比
        if stoploss_price < current_rate:
            return stoploss_from_absolute(stoploss_price, current_rate, is_short=trade.is_short)
  
        # 返回最大止损值，保持当前止损价格不变
        return None
  ```
  
  有关在策略回调函数中使用数据帧的更多信息，请参阅[数据帧访问](https://www.freqtrade.io/en/stable/strategy-advanced/#dataframe-access)。
  
  **止损计算的常用助手**
  
  **相对于开盘价的止损**
  
  从 `custom_stoploss()` 返回的止损值必须指定相对于 `current_rate` 的百分比，但有时你可能希望指定相对于开盘价的止损。`stoploss_from_open()` 是一个辅助函数，用于计算可以从 `custom_stoploss` 返回的止损值，该止损值将等效于在开仓点上方的所需交易利润。
  
  **从 custom stoploss 函数返回相对于开盘价的止损**
  
  ```python
    def custom_stoploss(...):
        # ...
        return stoploss_from_open(0.02, current_profit, is_short=trade.is_short, leverage=trade.leverage)
  ```
  
  **注意**
  
  向 `stoploss_from_open()` 提供无效输入可能会产生 “CustomStoploss 函数未返回有效止损” 警告。如果 `current_profit` 参数低于指定的 `open_relative_stop`，则可能会发生这种情况。当通过 `confirm_trade_exit()` 方法阻止平仓时，可能会出现这种情况。警告可以通过在 `confirm_trade_exit()` 中检查 `exit_reason` 来从不阻止止损卖出来解决，或者通过使用 `return stoploss_from_open(...) or 1` 惯用法来解决，当 `current_profit < open_relative_stop` 时，这将请求不更改止损。
  
  **从绝对价格计算止损百分比**
  
  从 `custom_stoploss()` 返回的止损值始终指定相对于 `current_rate` 的百分比。为了在指定的绝对价格水平设置止损，我们需要使用 `stop_rate` 来计算相对于 `current_rate` 的百分比，这将产生与从开盘价指定百分比相同的结果。
  
  辅助函数 `stoploss_from_absolute()` 可用于从绝对价格转换为可以从 `custom_stoploss()` 返回的当前价格相对止损。
  
  **从 custom stoploss 函数返回使用绝对价格的止损**
  
  ```python
    def custom_stoploss(...):
        # ...
        dataframe, _ = self.dp.get_analyzed_dataframe(pair, self.timeframe)
        last_candle = dataframe.iloc[-1].squeeze()
  
        # 使用抛物线 SAR 作为绝对止损价格
        stoploss_price = last_candle["sar"]
  
        # 将绝对价格转换为相对于 current_rate 的百分比
        if stoploss_price < current_rate:
            return stoploss_from_absolute(stoploss_price, current_rate, is_short=trade.is_short)
        # ...
  ```
  
  **自定义订单价格规则**
  
  默认情况下，freqtrade 使用订单簿自动设置订单价格 (相关文档)，你还可以选择根据你的策略创建自定义订单价格。
  
  你可以通过在策略文件中创建 `custom_entry_price()` 函数来自定义开仓价格，并创建 `custom_exit_price()` 函数来自定义平仓价格，从而使用此功能。
  
  在交易所下单之前，将立即调用这些方法中的每一个。
  
  **注意**
  
  如果你的自定义定价函数返回 `None` 或无效值，价格将回退到 `proposed_rate`，该价格基于常规定价配置。
  
  **注意**
  
  使用 `custom_entry_price` 时，一旦创建了与交易相关的第一个开仓订单，`Trade` 对象将可用，对于第一个开仓订单，`trade` 参数值将为 `None`。
  
  **自定义订单开仓和平仓价格示例**
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    def custom_entry_price(self, pair: str, trade: Trade | None, current_time: datetime, proposed_rate: float,
                           entry_tag: str | None, side: str, **kwargs) -> float:
  
        dataframe, last_updated = self.dp.get_analyzed_dataframe(pair=pair,
                                                                timeframe=self.timeframe)
        new_entryprice = dataframe["bollinger_10_lowerband"].iat[-1]
  
        return new_entryprice
  
    def custom_exit_price(self, pair: str, trade: Trade,
                          current_time: datetime, proposed_rate: float,
                          current_profit: float, exit_tag: str | None, **kwargs) -> float:
  
        dataframe, last_updated = self.dp.get_analyzed_dataframe(pair=pair,
                                                                timeframe=self.timeframe)
        new_exitprice = dataframe["bollinger_10_upperband"].iat[-1]
  
        return new_exitprice
  ```
  
  **警告**
  
  修改开仓和平仓价格仅适用于限价单。根据选择的价格，这可能会导致大量未成交的订单。默认情况下，当前价格和自定义价格之间允许的最大距离为 2%，可以在配置中使用 `custom_price_max_distance_ratio` 参数更改此值。示例：如果 `new_entryprice` 为 97，`proposed_rate` 为 100，并且 `custom_price_max_distance_ratio` 设置为 2%，则保留的有效自定义开仓价格将为 98，这是低于当前 (建议) 价格的 2%。
  
  **回测**
  
  自定义价格在回测中受支持 (从 2021.12 开始)，如果价格在 K 线的低价/高价范围内，订单将成交。未立即成交的订单将受到常规超时处理，这将在每个 (详细) K 线发生一次。`custom_exit_price()` 仅针对类型为 `exit_signal`、`Custom exit` 和部分平仓的平仓调用。所有其他平仓类型将使用常规回测价格。
  
  **自定义订单超时规则**
  
  可以通过策略或在配置的 `unfilledtimeout` 部分中配置简单的、基于时间的订单超时。
  
  但是，freqtrade 还为两种订单类型提供了自定义回调函数，允许你根据自定义条件决定订单是否超时。
  
  **注意**
  
  回测会在价格落入 K 线的低价/高价范围内时成交订单。对于未立即成交的订单 (使用自定义定价)，将为每个 (详细) K 线调用以下回调函数。
  
  **自定义订单超时示例**
  
  在订单成交或取消之前，将为每个未结订单调用此函数。`check_entry_timeout()` 用于交易开仓，而 `check_exit_timeout()` 用于交易平仓订单。
  
  下面的简单示例根据资产价格应用不同的未成交超时。它对价格较高的资产应用严格的超时，同时允许更多时间在廉价币上成交。
  
  该函数必须返回 `True` (取消订单) 或 `False` (保持订单存活)。
  
  ```python
    # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    # 将 unfilledtimeout 设置为 25 小时，因为下面的最大超时时间为 24 小时。
    unfilledtimeout = {
        "entry": 60 * 25,
        "exit": 60 * 25
    }
  
    def check_entry_timeout(self, pair: str, trade: Trade, order: Order,
                            current_time: datetime, **kwargs) -> bool:
        if trade.open_rate > 100 and trade.open_date_utc < current_time - timedelta(minutes=5):
            return True
        elif trade.open_rate > 10 and trade.open_date_utc < current_time - timedelta(minutes=3):
            return True
        elif trade.open_rate < 1 and trade.open_date_utc < current_time - timedelta(hours=24):
           return True
        return False
  
    def check_exit_timeout(self, pair: str, trade: Trade, order: Order,
                           current_time: datetime, **kwargs) -> bool:
        if trade.open_rate > 100 and trade.open_date_utc < current_time - timedelta(minutes=5):
            return True
        elif trade.open_rate > 10 and trade.open_date_utc < current_time - timedelta(minutes=3):
            return True
        elif trade.open_rate < 1 and trade.open_date_utc < current_time - timedelta(hours=24):
           return True
        return False
  ```
  
  **注意**
  
  对于上面的示例，`unfilledtimeout` 必须设置为大于 24 小时的值，否则该类型的超时将首先应用。
  
  **自定义订单超时示例 (使用附加数据)**
  
  ```python
    # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    # 将 unfilledtimeout 设置为 25 小时，因为下面的最大超时时间为 24 小时。
    unfilledtimeout = {
        "entry": 60 * 25,
        "exit": 60 * 25
    }
  
    def check_entry_timeout(self, pair: str, trade: Trade, order: Order,
                            current_time: datetime, **kwargs) -> bool:
        ob = self.dp.orderbook(pair, 1)
        current_price = ob["bids"][0][0]
        # 如果价格比订单高出 2% 以上，则取消买入订单。
        if current_price > order.price * 1.02:
            return True
        return False
  
    def check_exit_timeout(self, pair: str, trade: Trade, order: Order,
                           current_time: datetime, **kwargs) -> bool:
        ob = self.dp.orderbook(pair, 1)
        current_price = ob["asks"][0][0]
        # 如果价格比订单低 2% 以上，则取消卖出订单。
        if current_price < order.price * 0.98:
            return True
        return False
  ```
  
  **机器人订单确认**
  
  确认交易开仓/平仓。这些是在下单之前将被调用的最后的方法。
  
  **交易开仓 (买入订单) 确认**
  
  `confirm_trade_entry()` 可用于在最后一秒中止交易开仓 (可能是因为价格不是我们预期的)。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    def confirm_trade_entry(self, pair: str, order_type: str, amount: float, rate: float,
                            time_in_force: str, current_time: datetime, entry_tag: str | None,
                            side: str, **kwargs) -> bool:
        """
        在下达开仓订单之前立即调用。
        此函数的时机至关重要，因此请避免在此方法中进行繁重的计算或
        网络请求。
  
        有关完整文档，请访问 https://www.freqtrade.io/en/latest/strategy-advanced/
  
        当策略未实现时，返回 True (始终确认)。
  
        :param pair: 即将买入/做空的交易对。
        :param order_type: 订单类型 (在 order_types 中配置)。通常是 limit 或 market。
        :param amount: 要交易的目标 (基础) 货币数量。
        :param rate: 使用限价单时的汇率
                     或市价单的当前汇率。
        :param time_in_force: 有效时间。默认为 GTC (直到取消)。
        :param current_time: datetime 对象，包含当前日期时间
        :param entry_tag: 如果买入信号提供了可选的 entry_tag (buy_tag)。
        :param side: "long" 或 "short" - 表示建议交易的方向
        :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
        :return bool: 当返回 True 时，买入订单将在交易所下单。
            False 中止该过程
        """
        return True
  ```
  
  **交易平仓 (卖出订单) 确认**
  
  `confirm_trade_exit()` 可用于在最后一秒中止交易平仓 (卖出) (可能是因为价格不是我们预期的)。
  
  如果应用了不同的平仓原因，则 `confirm_trade_exit()` 可能会在一次迭代中为同一交易多次调用。平仓原因 (如果适用) 将按以下顺序排列：
  
  *   `exit_signal` / `custom_exit`
  *   `stop_loss`
  *   `roi`
  *   `trailing_stop_loss`
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
    # ... populate_* 方法
  
    def confirm_trade_exit(self, pair: str, trade: Trade, order_type: str, amount: float,
                           rate: float, time_in_force: str, exit_reason: str,
                           current_time: datetime, **kwargs) -> bool:
        """
        在下达常规平仓订单之前立即调用。
        此函数的时机至关重要，因此请避免在此方法中进行繁重的计算或
        网络请求。
  
        有关完整文档，请访问 https://www.freqtrade.io/en/latest/strategy-advanced/
  
        当策略未实现时，返回 True (始终确认)。
  
        :param pair: 要平仓的交易对。
        :param trade: 交易对象。
        :param order_type: 订单类型 (在 order_types 中配置)。通常是 limit 或 market。
        :param amount: 基础货币数量。
        :param rate: 使用限价单时的汇率
                     或市价单的当前汇率。
        :param time_in_force: 有效时间。默认为 GTC (直到取消)。
        :param exit_reason: 平仓原因。
            可以是 ["roi", "stop_loss", "stoploss_on_exchange", "trailing_stop_loss",
                           "exit_signal", "force_exit", "emergency_exit"] 中的任何一个
        :param current_time: datetime 对象，包含当前日期时间
        :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
        :return bool: 当返回 True 时，平仓订单将在交易所下单。
            False 中止该过程
        """
        if exit_reason == "force_exit" and trade.calc_profit_ratio(rate) < 0:
            # 拒绝利润为负的强制卖出
            # 这只是一个示例，请根据你的需要进行调整
            # (这不一定有意义，假设你知道何时强制卖出)
            return False
        return True
  ```
  
  **警告**
  
  `confirm_trade_exit()` 可以阻止止损平仓，如果忽略止损平仓，则会导致重大损失。`confirm_trade_exit()` 不会针对清
- **警告**
  
  `confirm_trade_exit()` 可以阻止止损平仓，如果忽略止损平仓，则会导致重大损失。`confirm_trade_exit()` 不会针对清算调用 - 因为清算是由交易所强制执行的，因此无法拒绝。
  
  **调整交易仓位**
  
  `position_adjustment_enable` 策略属性启用策略中 `adjust_trade_position()` 回调函数的使用。出于性能原因，它默认禁用，如果启用，freqtrade 将在启动时显示警告消息。`adjust_trade_position()` 可用于执行额外的订单，例如使用 DCA (美元成本平均法) 管理风险或增加或减少仓位。
  
  额外的订单也会产生额外的手续费，并且这些订单不计入 `max_open_trades`。
  
  当存在未结订单 (买入或卖出) 等待执行时，不会调用此回调函数。
  
  `adjust_trade_position()` 在交易期间非常频繁地被调用，因此你必须尽可能保持你的实现性能。
  
  仓位调整将始终应用于交易方向，因此正值将始终增加你的仓位 (负值将减少你的仓位)，无论它是做多还是做空交易。可以通过返回一个包含 2 个元素的元组来为调整订单分配一个标签，第一个元素是调整金额，第二个元素是标签 (例如 `return 250, "increase_favorable_conditions"`)。
  
  无法修改杠杆，并且假定返回的下单金额是在应用杠杆之前的。
  
  **宽松的逻辑**
  
  在模拟和实盘运行中，此函数将在每个 `throttle_process_secs` (默认为 5 秒) 调用一次。如果你有一个宽松的逻辑，例如你进行额外开仓的逻辑只是检查最后一根 K 线的 RSI 是否低于 30，那么当满足此类条件时，你的机器人将每 5 秒进行一次额外的重新开仓，直到资金用完、达到 `max_position_adjustment` 限制或到达 RSI 大于 30 的新 K 线。
  
  平仓也是如此。因此，请务必制定严格的逻辑和/或检查最后一个成交的订单。
  
  **回测**
  
  在回测期间，此回调函数针对时间周期或 `timeframe_detail` 中的每个 K 线调用一次，因此运行时性能将受到影响。这也可能导致实盘和回测之间存在偏差的结果，因为回测只能每根 K 线调整一次交易，而实盘可以在每根 K 线多次调整交易。
  
  **增加仓位**
  
  如果应进行额外的开仓订单 (增加仓位 -> 做多交易的买入订单，做空交易的卖出订单)，则策略应返回介于 `min_stake` 和 `max_stake` 之间的正 `stake_amount` (以本金货币为单位)。
  
  如果钱包中没有足够的资金 (`return` 值高于 `max_stake`)，则该信号将被忽略。`max_entry_position_adjustment` 属性用于限制机器人可以执行的每个交易的额外开仓次数 (在第一个开仓订单之上)。默认情况下，该值为 -1，这意味着机器人对调整开仓次数没有限制。
  
  一旦你达到你在 `max_entry_position_adjustment` 上设置的最大额外开仓次数，就会忽略额外的开仓，但无论如何都会调用回调函数以查找部分平仓。
  
  **关于下单金额**
  
  使用固定下单金额意味着它将是第一个订单使用的金额，就像没有仓位调整一样。如果你希望使用 DCA 购买额外的订单，请确保在钱包中为此留出足够的资金。将 "unlimited" 下单金额与 DCA 订单一起使用需要你同时实现 `custom_stake_amount()` 回调函数，以避免将所有资金分配给初始订单。
  
  **减少仓位**
  
  策略应返回负的 `stake_amount` (以本金货币为单位) 以进行部分平仓。返回此时全部拥有的份额 (`-trade.stake_amount`) 将导致全部平仓。
  返回大于上述值的值 (因此剩余的 `stake_amount` 将变为负值) 将导致机器人忽略该信号。
  
  对于部分平仓，重要的是要知道用于计算部分平仓订单的币种数量的公式是 `要部分平仓的数量 = 负的 stake_amount * trade.amount / trade.stake_amount`，其中 `负的 stake_amount` 是从 `adjust_trade_position` 函数返回的值。正如在公式中看到的，该公式不关心该仓位的当前利润/亏损。它只关心根本不受价格变动影响的 `trade.amount` 和 `trade.stake_amount`。
  
  例如，假设你以 50 的开盘价买入 2 个 SHITCOIN/USDT，这意味着该交易的下单金额为 100 USDT。现在价格上涨到 200，你想卖出一半。在这种情况下，你必须返回 `trade.stake_amount` 的 -50% (0.5 * 100 USDT)，即 -50。机器人将计算它需要卖出的数量，即 50 * 2 / 100，等于 1 个 SHITCOIN/USDT。如果你返回 -200 (2 * 200 的 50%)，机器人将忽略它，因为 `trade.stake_amount` 只有 100 USDT，但你要求卖出 200 USDT，这意味着你要求卖出 4 个 SHITCOIN/USDT。
  
  回到上面的示例，由于当前价格为 200，因此你的交易的当前 USDT 价值现在为 400 USDT。假设你想部分卖出 100 USDT 以取出初始投资，并将利润留在交易中，希望价格继续上涨。在这种情况下，你必须采用不同的方法。首先，你需要计算你需要卖出的确切数量。在这种情况下，由于你想根据当前价格卖出价值 100 USDT 的资产，你需要部分卖出的确切数量是 100 * 2 / 400，等于 0.5 个 SHITCOIN/USDT。由于我们现在知道我们要卖出的确切数量 (0.5)，你在 `adjust_trade_position` 函数中需要返回的值是 `-要部分平仓的数量 * trade.stake_amount / trade.amount`，等于 -25。机器人将卖出 0.5 个 SHITCOIN/USDT，在交易中保留 1.5 个。你将从部分平仓中获得 100 USDT。
  
  **止损计算**
  
  止损仍然根据初始开盘价计算，而不是平均价格。常规止损规则仍然适用 (不能下调)。
  
  虽然 `/stopentry` 命令会阻止机器人开新仓，但仓位调整功能将继续在现有交易中买入新订单。
  
  **具有许多仓位调整的性能**
  
  仓位调整可能是提高策略产出的一种好方法 - 但如果广泛使用此功能，它也可能存在缺点。
  每个订单都将附加到交易对象，直到交易持续时间 - 因此会增加内存使用量。因此，不建议进行持续时间长且有 10 次甚至 100 次仓位调整的交易，并且应定期关闭以不影响性能。
  
  ```python
  # 默认导入
  
  class DigDeeperStrategy(IStrategy):
  
      position_adjustment_enable = True
  
      # 尝试使用 DCA 处理大幅下跌。需要高止损。
      stoploss = -0.30
  
      # ... populate_* 方法
  
      # 示例特定变量
      max_entry_position_adjustment = 3
      # 此数字将在下面进一步说明
      max_dca_multiplier = 5.5
  
      # 这在下达初始订单 (开仓) 时调用
      def custom_stake_amount(self, pair: str, current_time: datetime, current_rate: float,
                              proposed_stake: float, min_stake: float | None, max_stake: float,
                              leverage: float, entry_tag: str | None, side: str,
                              **kwargs) -> float:
  
          # 我们需要为可能的进一步 DCA 订单留出大部分资金
          # 这也适用于固定下单金额
          return proposed_stake / self.max_dca_multiplier
  
      def adjust_trade_position(self, trade: Trade, current_time: datetime,
                                current_rate: float, current_profit: float,
                                min_stake: float | None, max_stake: float,
                                current_entry_rate: float, current_exit_rate: float,
                                current_entry_profit: float, current_exit_profit: float,
                                **kwargs
                                ) -> float | None | tuple[float | None, str | None]:
          """
          自定义交易调整逻辑，返回应增加或减少的交易的下单金额。
          这意味着额外的开仓或平仓订单会产生额外的手续费。
          仅当 `position_adjustment_enable` 设置为 True 时调用。
  
          有关完整文档，请访问 https://www.freqtrade.io/en/latest/strategy-advanced/
  
          当策略未实现时，返回 None
  
          :param trade: 交易对象。
          :param current_time: datetime 对象，包含当前日期时间
          :param current_rate: 当前开仓价 (与 current_entry_profit 相同)
          :param current_profit: 当前利润 (作为比率)，根据 current_rate 计算
                                 (与 current_entry_profit 相同)。
          :param min_stake: 交易所允许的最小下单金额 (开仓和平仓)
          :param max_stake: 允许的最大下单金额 (通过余额或交易所限制)。
          :param current_entry_rate: 使用开仓定价的当前价格。
          :param current_exit_rate: 使用平仓定价的当前价格。
          :param current_entry_profit: 使用开仓定价的当前利润。
          :param current_exit_profit: 使用平仓定价的当前利润。
          :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
          :return float: 调整你的交易的下单金额，
                         正值表示增加仓位，负值表示减少仓位。
                         返回 None 表示不采取任何行动。
                         可选地，返回一个包含第二个元素的元组，其中包含订单原因
          """
  
          if current_profit > 0.05 and trade.nr_of_successful_exits == 0:
              # 在 +5% 时拿走一半的利润
              return -(trade.stake_amount / 2), "half_profit_5%"
  
          if current_profit > -0.05:
              return None
  
          # 获取交易对数据帧 (只是为了展示如何访问它)
          dataframe, _ = self.dp.get_analyzed_dataframe(trade.pair, self.timeframe)
          # 只有在价格没有主动下跌时才买入。
          last_candle = dataframe.iloc[-1].squeeze()
          previous_candle = dataframe.iloc[-2].squeeze()
          if last_candle["close"] < previous_candle["close"]:
              return None
  
          filled_entries = trade.select_filled_orders(trade.entry_side)
          count_of_entries = trade.nr_of_successful_entries
          # 最多允许 3 次额外的、越来越大的买入 (总共 4 次)
          # 初始买入为 1 倍
          # 如果跌至 -5% 的利润，我们再买入 1.25 倍，平均利润应提高到大约 -2.2%
          # 如果再次跌至 -5%，我们再买入 1.5 倍
          # 如果再次跌至 -5%，我们再买入 1.75 倍
          # 此交易的总下单金额将为 1 + 1.25 + 1.5 + 1.75 = 初始允许下单金额的 5.5 倍。
          # 这就是 max_dca_multiplier 为 5.5 的原因
          # 希望你有一个充裕的钱包！
          try:
              # 这将返回第一个订单的下单金额
              stake_amount = filled_entries[0].stake_amount_filled
              # 然后计算当前安全订单大小
              stake_amount = stake_amount * (1 + (count_of_entries * 0.25))
              return stake_amount, "1/3rd_increase"
          except Exception as exception:
              return None
  
          return None
  ```
  
  **仓位调整计算**
  
  *   开仓价格使用加权平均值计算。
  *   平仓不会影响平均开仓价格。
  *   部分平仓的相对利润是相对于此时的平均开仓价格而言的。
  *   最终平仓的相对利润是根据总投资资本计算的。(请参见下面的示例)
  
  **计算示例**
  
  **调整开仓价格**
  
  策略开发人员可以使用 `adjust_entry_price()` 回调函数在新 K 线到达时刷新/替换限价单。请注意，`custom_entry_price()` 仍然是在开仓触发时指示初始开仓限价单价格目标的那个。
  
  可以通过从此回调函数返回 `None` 来取消订单。
  
  返回 `current_order_rate` 将使订单在交易所保持 “原样”。返回任何其他价格将取消现有订单，并用新订单替换它。
  
  交易开仓日期 (`trade.open_date_utc`) 将保持在下达第一个订单的时间。请务必注意这一点 - 并最终调整你在其他回调函数中的逻辑以解决此问题，并改用第一个成交订单的日期。
  
  如果取消原始订单失败，则该订单将不会被替换 - 尽管该订单很可能已在交易所取消。在初始开仓时发生这种情况将导致删除该订单，而在仓位调整订单上，这将导致交易规模保持原样。
  
  **常规超时**
  
  开仓未成交超时机制 (以及 `check_entry_timeout()`) 优先于此。通过上述方法取消的开仓订单将不会调用此回调函数。请务必更新超时值以符合你的预期。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
  
      # ... populate_* 方法
  
      def adjust_entry_price(self, trade: Trade, order: Order | None, pair: str,
                             current_time: datetime, proposed_rate: float, current_order_rate: float,
                             entry_tag: str | None, side: str, **kwargs) -> float:
          """
          开仓价格重新调整逻辑，返回用户所需的限价。
          仅当订单已经下单、仍然未结 (未完全或部分成交) 且在开仓触发后的后续 K 线未超时时执行。
  
          当策略未实现时，默认返回 current_order_rate。
          如果返回 current_order_rate，则维持现有订单。
          如果返回 None，则订单将被取消，但不会被新订单替换。
  
          :param pair: 当前分析的交易对
          :param trade: 交易对象。
          :param order: 订单对象
          :param current_time: datetime 对象，包含当前日期时间
          :param proposed_rate: 根据 entry_pricing 中的定价设置计算的汇率。
          :param current_order_rate: 现有订单的汇率。
          :param entry_tag: 如果买入信号提供了可选的 entry_tag (buy_tag)。
          :param side: "long" 或 "short" - 表示建议交易的方向
          :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
          :return float: 如果提供，则返回新的开仓价格值
  
          """
          # 在 BTC/USDT 交易对开仓触发后的前 10 分钟内，限价单使用并跟随 SMA200 作为价格目标。
          if (
              pair == "BTC/USDT"
              and entry_tag == "long_sma200"
              and side == "long"
              and (current_time - timedelta(minutes=10)) <= trade.open_date_utc
          ):
              # 如果订单已成交超过一半的数量，则只需取消订单
              if order.filled > order.remaining:
                  return None
              else:
                  dataframe, _ = self.dp.get_analyzed_dataframe(pair=pair, timeframe=self.timeframe)
                  current_candle = dataframe.iloc[-1].squeeze()
                  # 期望价格
                  return current_candle["sma_200"]
          # 默认：保持现有订单
          return current_order_rate
  ```
  
  **杠杆回调函数**
  
  当在允许杠杆的市场中交易时，此方法必须返回所需的杠杆 (默认为 1 -> 无杠杆)。
  
  假设资金为 500USDT，杠杆为 3 的交易将导致仓位为 500 x 3 = 1500 USDT。
  
  高于 `max_leverage` 的值将被调整为 `max_leverage`。对于不支持杠杆的市场/交易所，此方法将被忽略。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
      def leverage(self, pair: str, current_time: datetime, current_rate: float,
                   proposed_leverage: float, max_leverage: float, entry_tag: str | None, side: str,
                   **kwargs) -> float:
          """
          自定义每个新交易的杠杆。仅在期货模式下调用此方法。
  
          :param pair: 当前分析的交易对
          :param current_time: datetime 对象，包含当前日期时间
          :param current_rate: 根据 exit_pricing 中的定价设置计算的汇率。
          :param proposed_leverage: 机器人建议的杠杆。
          :param max_leverage: 此交易对允许的最大杠杆
          :param entry_tag: 如果买入信号提供了可选的 entry_tag (buy_tag)。
          :param side: "long" 或 "short" - 表示建议交易的方向
          :return: 杠杆数量，介于 1.0 和 max_leverage 之间。
          """
          return 1.0
  ```
  
  所有利润计算都包括杠杆。止损/ROI 也包括杠杆在它们的计算中。在 10 倍杠杆下定义 10% 的止损将在下跌 1% 时触发止损。
  
  **订单成交回调函数**
  
  订单成交回调函数 (`order_filled()`) 可用于根据当前交易状态在订单成交后执行特定操作。它将独立于订单类型 (开仓、平仓、止损或仓位调整) 被调用。
  
  假设你的策略需要在交易开仓时存储 K 线的高点值，这可以通过此回调函数实现，如以下示例所示。
  
  ```python
  # 默认导入
  
  class AwesomeStrategy(IStrategy):
      def order_filled(self, pair: str, trade: Trade, order: Order, current_time: datetime, **kwargs) -> None:
          """
          在订单成交后立即调用。
          将针对所有订单类型 (开仓、平仓、止损、仓位调整) 调用。
          :param pair: 交易的交易对
          :param trade: 交易对象。
          :param order: 订单对象。
          :param current_time: datetime 对象，包含当前日期时间
          :param **kwargs: 确保保留此参数，以便此更新不会破坏你的策略。
          """
          # 获取交易对数据帧 (只是为了展示如何访问它)
          dataframe, _ = self.dp.get_analyzed_dataframe(trade.pair, self.timeframe)
          last_candle = dataframe.iloc[-1].squeeze()
  
          if (trade.nr_of_successful_entries == 1) and (order.ft_order_side == trade.entry_side):
              trade.set_custom_data(key="entry_candle_high", value=last_candle["high"])
  
          return None
  ```