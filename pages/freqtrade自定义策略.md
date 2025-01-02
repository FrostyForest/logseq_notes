- #freqtrade
- ## 策略定制
  
  本页说明如何定制你的策略，添加新指标并设置交易规则。
  
  如果你还没有熟悉以下内容，请先阅读：
  
  *   [Freqtrade 策略 101](https://www.freqtrade.io/en/stable/strategy-101/)，它提供了策略开发的快速入门
  *   [Freqtrade 机器人基础](https://www.freqtrade.io/en/stable/bot-basics/)，它提供了有关机器人如何运作的总体信息
  
  **开发你自己的策略**
  
  机器人包含一个默认策略文件。
  
  此外，[策略仓库](https://github.com/freqtrade/freqtrade-strategies)中还提供了其他几个策略。
  
  然而，你很可能有自己的策略想法。
  
  本文档旨在帮助你将你的想法转化为可用的策略。
  
  **生成策略模板**
  
  首先，你可以使用以下命令：
  
  ```bash
  freqtrade new-strategy --strategy AwesomeStrategy
  ```
  
  这将从模板创建一个名为 `AwesomeStrategy` 的新策略，该策略将位于 `user_data/strategies/AwesomeStrategy.py` 文件中。
  
  **注意**
  
  策略名称和文件名之间存在差异。在大多数命令中，Freqtrade 使用策略名称，而不是文件名。
  
  **注意**
  
  `new-strategy` 命令生成的是入门示例，这些示例本身并不能盈利。
  
  **不同的模板级别**
  
  **策略剖析**
  
  策略文件包含构建策略逻辑所需的所有信息：
  
  *   OHLCV 格式的 K 线数据
  *   指标
  *   开仓逻辑
    *   信号
  *   平仓逻辑
    *   信号
    *   最小 ROI
    *   回调函数 ("自定义函数")
  *   止损
    *   固定/绝对
    *   追踪
    *   回调函数 ("自定义函数")
  *   定价 [可选]
  *   仓位调整 [可选]
  
  机器人包含一个名为 `SampleStrategy` 的示例策略，你可以将其用作基础：`user_data/strategies/sample_strategy.py`。你可以使用参数 `--strategy SampleStrategy` 对其进行测试。请记住，你使用的是策略类名，而不是文件名。
  
  此外，还有一个名为 `INTERFACE_VERSION` 的属性，它定义了机器人应该使用的策略接口版本。当前版本是 3 - 这也是未在策略中显式设置时的默认版本。
  
  你可能会看到设置为接口版本 2 的旧策略，这些策略需要更新为 v3 术语，因为将来的版本将要求设置此项。
  
  使用 `trade` 命令以模拟或实盘模式启动机器人：
  
  ```bash
  freqtrade trade --strategy AwesomeStrategy
  ```
  
  **机器人模式**
  
  Freqtrade 策略可以通过 Freqtrade 机器人在 5 种主要模式下进行处理：
  
  *   回测
  *   超参数优化
  *   模拟 ("前向测试")
  *   实盘
  *   FreqAI (此处未涵盖)
  
  查看有关如何将机器人设置为模拟或实盘模式的[配置文档](https://www.freqtrade.io/en/stable/configuration/#dry-run-live-modes)。
  
  测试时始终使用模拟模式，因为这可以让你了解你的策略在现实中将如何运作，而无需冒资金风险。
  
  **深入研究**
  
  在以下部分中，我们将使用 `user_data/strategies/sample_strategy.py` 文件作为参考。
  
  **策略和回测**
  
  为了避免回测和模拟/实盘模式之间出现问题和意外差异，请注意，在回测期间，完整的时间范围将一次性传递给 `populate_*()` 方法。因此，最好使用向量化操作 (跨整个数据帧，而不是循环) 并避免索引引用 (df.iloc[-1])，而是使用 df.shift() 获取前一个 K 线。
  
  **警告：使用未来数据**
  
  由于回测会将完整的时间范围传递给 `populate_*()` 方法，因此策略作者需要注意避免让策略利用未来的数据。本文档的[常见错误](https://www.freqtrade.io/en/stable/strategy-customization/#common-mistakes-when-developing-strategies)部分列出了这种情况的一些常见模式。
  
  **提前查看和递归分析**
  
  **数据帧 (Dataframe)**
  
  Freqtrade 使用 pandas 来存储/提供 K 线 (OHLCV) 数据。Pandas 是一个为处理大量表格格式数据而开发的优秀库。
  
  数据帧中的每一行对应图表上的一根 K 线，最新的完整 K 线始终是数据帧中的最后一根 (按日期排序)。
  
  如果我们使用 pandas 的 `head()` 函数查看主数据帧的前几行，我们将看到：
  
  ```
  > dataframe.head()
                       date      open      high       low     close     volume
  0 2021-11-09 23:25:00+00:00  67279.67  67321.84  67255.01  67300.97   44.62253
  1 2021-11-09 23:30:00+00:00  67300.97  67301.34  67183.03  67187.01   61.38076
  2 2021-11-09 23:35:00+00:00  67187.02  67187.02  67031.93  67123.81  113.42728
  3 2021-11-09 23:40:00+00:00  67123.80  67222.40  67080.33  67160.48   78.96008
  4 2021-11-09 23:45:00+00:00  67160.48  67160.48  66901.26  66943.37  111.39292
  ```
  
  数据帧是一个表格，其中列不是单个值，而是一系列数据值。因此，像下面这样的简单 Python 比较将不起作用：
  
  ```python
    if dataframe['rsi'] > 30:
        dataframe['enter_long'] = 1
  ```
  
  上述代码段将失败，并显示 `The truth value of a Series is ambiguous [...]`。
  
  必须以 pandas 兼容的方式编写此代码段，以便在整个数据帧上执行操作，即向量化。
  
  ```python
    dataframe.loc[
        (dataframe['rsi'] > 30)
    , 'enter_long'] = 1
  ```
  
  使用此代码段，你的数据帧中将有一个新列，只要 RSI 大于 30，该列就会分配 1。
  
  Freqtrade 使用这个新列作为开仓信号，假设随后将在下一根开盘 K 线上开仓。
  
  Pandas 提供了计算指标的快速方法，即 “向量化”。为了从这种速度中受益，建议不要使用循环，而是使用向量化方法。
  
  向量化操作在整个数据范围内执行计算，因此与循环遍历每一行相比，计算指标的速度要快得多。
  
  **信号与交易**
  
  **交易订单假设**
  
  在回测中，信号是在 K 线收盘时生成的。然后在下一根 K 线开盘时立即启动交易。
  
  在模拟和实盘中，这可能会延迟，因为需要首先分析所有交易对的数据帧，然后对每个交易对进行交易处理。这意味着在模拟/实盘中，你需要注意尽可能降低计算延迟，通常通过运行较少数量的交易对并使用具有良好时钟速度的 CPU 来实现。
  
  **为什么我看不到 “实时” K 线数据？**
  
  Freqtrade 不会在数据帧中存储不完整/未完成的 K 线。
  
  使用不完整的数据来制定策略决策称为 “重新粉刷”，你可能会看到其他平台允许这样做。
  
  Freqtrade 不允许。数据帧中只有完整/已完成的 K 线数据可用。
  
  **自定义指标**
  
  开仓和平仓信号需要指标。你可以通过扩展策略文件中的 `populate_indicators()` 方法中包含的列表来添加更多指标。
  
  你应该只添加在 `populate_entry_trend()`、`populate_exit_trend()` 中使用的指标，或者用于填充另一个指标的指标，否则性能可能会受到影响。
  
  始终从这三个函数返回数据帧而不删除/修改列 “open”、“high”、“low”、“close”、“volume” 非常重要，否则这些字段将包含意外的内容。
  
  示例：
  
  ```python
  def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    """
    向给定的 DataFrame 添加几个不同的 TA 指标
  
    性能说明：为了获得最佳性能，请节约使用的指标数量。
    只取消注释你在策略或超参数优化配置中使用的指标，否则你将浪费你的内存和 CPU 使用率。
    :param dataframe: 包含交易所数据的 Dataframe
    :param metadata: 其他信息，例如当前交易的交易对
    :return: 包含策略所有必要指标的 Dataframe
    """
    dataframe['sar'] = ta.SAR(dataframe)
    dataframe['adx'] = ta.ADX(dataframe)
    stoch = ta.STOCHF(dataframe)
    dataframe['fastd'] = stoch['fastd']
    dataframe['fastk'] = stoch['fastk']
    dataframe['bb_lower'] = ta.BBANDS(dataframe, nbdevup=2, nbdevdn=2)['lowerband']
    dataframe['sma'] = ta.SMA(dataframe, timeperiod=40)
    dataframe['tema'] = ta.TEMA(dataframe, timeperiod=9)
    dataframe['mfi'] = ta.MFI(dataframe)
    dataframe['rsi'] = ta.RSI(dataframe)
    dataframe['ema5'] = ta.EMA(dataframe, timeperiod=5)
    dataframe['ema10'] = ta.EMA(dataframe, timeperiod=10)
    dataframe['ema50'] = ta.EMA(dataframe, timeperiod=50)
    dataframe['ema100'] = ta.EMA(dataframe, timeperiod=100)
    dataframe['ao'] = awesome_oscillator(dataframe)
    macd = ta.MACD(dataframe)
    dataframe['macd'] = macd['macd']
    dataframe['macdsignal'] = macd['macdsignal']
    dataframe['macdhist'] = macd['macdhist']
    hilbert = ta.HT_SINE(dataframe)
    dataframe['htsine'] = hilbert['sine']
    dataframe['htleadsine'] = hilbert['leadsine']
    dataframe['plus_dm'] = ta.PLUS_DM(dataframe)
    dataframe['plus_di'] = ta.PLUS_DI(dataframe)
    dataframe['minus_dm'] = ta.MINUS_DM(dataframe)
    dataframe['minus_di'] = ta.MINUS_DI(dataframe)
  
    # 永远记住返回数据帧
    return dataframe
  ```
  
  想要更多指标示例？
  
  查看 `user_data/strategies/sample_strategy.py`。然后取消注释你需要的指标。
  
  **指标库**
  
  开箱即用，freqtrade 安装了以下技术库：
  
  *   [ta-lib](https://www.ta-lib.org/)
  *   [pandas-ta](https://github.com/twopirllc/pandas-ta)
  *   [technical](https://github.com/tiagoshibata/technical-indicators)
  
  可以根据需要安装其他技术库，或者策略作者可以编写/发明自定义指标。
  
  **策略启动周期**
  
  某些指标具有不稳定的启动周期，在该周期中没有足够的 K 线数据来计算任何值 (NaN)，或者计算不正确。这可能会导致不一致，因为 Freqtrade 不知道这个不稳定周期有多长，并使用数据帧中的任何指标值。
  
  为了解决这个问题，可以为策略分配 `startup_candle_count` 属性。
  
  这应该设置为策略计算稳定指标所需的最大 K 线数量。在使用信息性交易对包含更高时间周期的情况下，`startup_candle_count` 不一定会更改。该值是任何信息性时间周期计算稳定指标所需的最大周期 (以 K 线为单位)。
  
  你可以使用[递归分析](https://www.freqtrade.io/en/stable/strategy-customization/#identifying-problems)来检查并找到要使用的正确 `startup_candle_count`。当递归分析显示方差为 0% 时，那么你可以确定你有足够的启动 K 线数据。
  
  在此示例策略中，这应该设置为 400 (`startup_candle_count = 400`)，因为 ema100 计算确保值正确的最低所需历史记录是 400 根 K 线。
  
  ```python
    dataframe['ema100'] = ta.EMA(dataframe, timeperiod=100)
  ```
  
  通过让机器人知道需要多少历史记录，回测交易可以在回测和超参数优化期间在指定的 `timerange` 开始。
  
  **使用 x 次调用获取 OHLCV**
  
  如果你收到类似 `WARNING - Using 3 calls to get OHLCV.` 的警告。这可能会导致机器人操作变慢。请检查你是否真的需要 1500 根 K 线用于你的策略 - 你应该考虑你是否真的需要这么多历史数据来获取你的信号。这样做将导致 Freqtrade 对同一交易对进行多次调用，这显然比一次网络请求要慢。因此，Freqtrade 将需要更长的时间来刷新 K 线 - 因此如果可能，应该避免这种情况。这被限制为总共 5 次调用，以避免使交易所过载或使 freqtrade 太慢。
  
  **警告**
  
  `startup_candle_count` 应低于 `ohlcv_candle_limit * 5` (对于大多数交易所来说是 500 * 5) - 因为在模拟交易/实盘交易操作期间只有这么多 K 线可用。
  
  **示例**
  
  让我们尝试使用 EMA100 的示例策略回测 1 个月 (2019 年 1 月) 的 5m K 线，如上所述。
  
  ```bash
  freqtrade backtesting --timerange 20190101-20190201 --timeframe 5m
  ```
  
  假设 `startup_candle_count` 设置为 400，回测知道它需要 400 根 K 线来生成有效的开仓信号。它将从 20190101 - (400 * 5m) 加载数据 - 大约是 2018-12-30 11:40:00。
  
  如果此数据可用，将使用此扩展时间范围计算指标。然后将在执行回测之前删除不稳定的启动周期 (直到 2019-01-01 00:00:00)。
  
  **不可用的启动 K 线数据**
  
  如果启动周期的数据不可用，则将调整 `timerange` 以考虑此启动周期。在我们的示例中，回测将从 2019-01-02 09:20:00 开始。
  
  **开仓信号规则**
  
  编辑策略文件中的 `populate_entry_trend()` 方法以更新你的开仓策略。
  
  始终返回数据帧而不删除/修改列 “open”、“high”、“low”、“close”、“volume” 非常重要，否则这些字段将包含意外的内容。然后，策略可能会产生无效值，或完全停止工作。
  
  此方法还将定义一个新列 “enter_long” (“enter_short” 代表做空)，该列需要包含 1 表示开仓，0 表示 “无操作”。`enter_long` 是一个强制性列，即使策略仅做空也必须设置。
  
  你可以使用 “enter_tag” 列命名你的开仓信号，这可以帮助你稍后调试和评估你的策略。
  
  `user_data/strategies/sample_strategy.py` 中的示例：
  
  ```python
  def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    """
    基于 TA 指标，为给定的数据帧填充买入信号
    :param dataframe: 填充了指标的 DataFrame
    :param metadata: 其他信息，例如当前交易的交易对
    :return: 带有买入列的 DataFrame
    """
    dataframe.loc[
        (
            (qtpylib.crossed_above(dataframe['rsi'], 30)) &  # 信号：RSI 向上穿越 30
            (dataframe['tema'] <= dataframe['bb_middleband']) &  # 防护
            (dataframe['tema'] > dataframe['tema'].shift(1)) &  # 防护
            (dataframe['volume'] > 0)  # 确保成交量不为 0
        ),
        ['enter_long', 'enter_tag']] = (1, 'rsi_cross')
  
    return dataframe
  ```
  
  **开空单**
  
  **注意**
  
  买入需要有卖家才能买入。因此，成交量需要 > 0 (`dataframe['volume'] > 0`) 以确保机器人不会在无活动期间买入/卖出。
  
  **平仓信号规则**
  
  编辑策略文件中的 `populate_exit_trend()` 方法以更新你的平仓策略。
  
  可以通过在配置或策略中将 `use_exit_signal` 设置为 `false` 来抑制平仓信号。
  
  `use_exit_signal` 不会影响信号冲突规则 - 该规则仍然适用并可以阻止开仓。
  
  始终返回数据帧而不删除/修改列 “open”、“high”、“low”、“close”、“volume” 非常重要，否则这些字段将包含意外的内容。然后，策略可能会产生无效值，或完全停止工作。
  
  此方法还将定义一个新列 “exit_long” (“exit_short” 代表做空)，该列需要包含 1 表示平仓，0 表示 “无操作”。
  
  你可以使用 “exit_tag” 列命名你的平仓信号，这可以帮助你稍后调试和评估你的策略。
  
  `user_data/strategies/sample_strategy.py` 中的示例：
  
  ```python
  def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    """
    基于 TA 指标，为给定的数据帧填充卖出信号
    :param dataframe: 填充了指标的 DataFrame
    :param metadata: 其他信息，例如当前交易的交易对
    :return: 带有买入列的 DataFrame
    """
    dataframe.loc[
        (
            (qtpylib.crossed_above(dataframe['rsi'], 70)) &  # 信号：RSI 向上穿越 70
            (dataframe['tema'] > dataframe['bb_middleband']) &  # 防护
            (dataframe['tema'] < dataframe['tema'].shift(1)) &  # 防护
            (dataframe['volume'] > 0)  # 确保成交量不为 0
        ),
        ['exit_long', 'exit_tag']] = (1, 'rsi_too_high')
    return dataframe
  ```
  
  **平空单**
  
  **最小 ROI**
  
  `minimal_roi` 策略变量定义了交易在独立于平仓信号之前应达到的最小投资回报率 (ROI)。
  
  它采用以下格式，即一个 Python 字典，字典键 (冒号左侧) 是自交易开仓以来经过的分钟数，值 (冒号右侧) 是百分比。
  
  ```python
  minimal_roi = {
    "40": 0.0,
    "30": 0.01,
    "20": 0.02,
    "0": 0.04
  }
  ```
  
  因此，上述配置将意味着：
  
  *   只要达到 4% 的利润就平仓
  *   达到 2% 的利润时平仓 (在 20 分钟后生效)
  *   达到 1% 的利润时平仓 (在 30 分钟后生效)
  *   当交易不亏损时平仓 (在 40 分钟后生效)
  
  计算确实包括手续费。
  
  **禁用最小 ROI**
  
  要完全禁用 ROI，请将其设置为空字典：
  
  ```python
  minimal_roi = {}
  ```
  
  **在最小 ROI 中使用计算**
  
  要使用基于 K 线持续时间 (时间周期) 的时间，以下代码片段可能很方便。
  
  这将允许你更改策略的时间周期，但最小 ROI 时间仍将设置为 K 线，例如 3 根 K 线后。
  
  ```python
  from freqtrade.exchange import timeframe_to_minutes
  
  class AwesomeStrategy(IStrategy):
  
    timeframe = "1d"
    timeframe_mins = timeframe_to_minutes(timeframe)
    minimal_roi = {
        "0": 0.05,                      # 前 3 根 K 线为 5%
        str(timeframe_mins * 3): 0.02,  # 3 根 K 线后为 2%
        str(timeframe_mins * 6): 0.01,  # 6 根 K 线后为 1%
    }
  ```
  **无法立即成交的订单**
  
  **止损**
  
  强烈建议设置止损以保护你的资金免受对你不利的强烈波动的影响。
  
  设置 10% 止损的示例：
  
  ```python
  stoploss = -0.10
  ```
  
  有关止损功能的完整文档，请查看专门的[止损页面](https://www.freqtrade.io/en/stable/stoploss/)。
  
  **时间周期**
  
  这是机器人应该在策略中使用的 K 线周期。
  
  常见值为 “1m”、“5m”、“15m”、“1h”，但是你的交易所支持的所有值都应该有效。
  
  请注意，相同的开仓/平仓信号可能在一个时间周期上运行良好，但在其他时间周期上却不行。
  
  此设置可在策略方法中作为 `self.timeframe` 属性访问。
  
  **Can short**
  
  要在期货市场中使用做空信号，你必须设置 `can_short = True`。
  
  启用此功能的策略将无法在现货市场上加载。
  
  如果你在 `enter_short` 列中有 1 值来引发做空信号，设置 `can_short = False` (这是默认值) 将意味着这些做空信号将被忽略，即使你已经在配置中指定了期货市场。
  
  **元数据字典**
  
  元数据字典 (可用于 `populate_entry_trend`、`populate_exit_trend`、`populate_indicators`) 包含其他信息。目前这是 `pair`，可以使用 `metadata['pair']` 访问，并将以 XRP/BTC 的格式返回一个交易对 (或 XRP/BTC:BTC 代表期货市场)。
  
  元数据字典不应被修改，并且不会在策略中的多个函数之间持久保存信息。
  
  相反，请查看[存储信息](https://www.freqtrade.io/en/stable/strategy-advanced/#storing-information-across-runs-metadata-dataclasses)部分。
  
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
  
  **策略文件加载**
  
  默认情况下，freqtrade 将尝试从用户目录 (默认的 `user_data/strategies`) 中的所有 `.py` 文件中加载策略。
  
  假设你的策略名为 `AwesomeStrategy`，存储在文件 `user_data/strategies/AwesomeStrategy.py` 中，那么你可以使用以下命令以模拟 (或实盘，具体取决于你的配置) 模式启动 freqtrade：
  
  ```bash
    freqtrade trade --strategy AwesomeStrategy`
  ```
  
  请注意，我们使用的是类名，而不是文件名。
  
  你可以使用 `freqtrade list-strategies` 查看 Freqtrade 能够加载的所有策略的列表 (正确文件夹中的所有策略)。它还将包括一个 “状态” 字段，突出显示潜在问题。
  
  **自定义策略目录**
  
  **信息性交易对**
  
  **获取不可交易交易对的数据**
  
  某些策略可能需要其他信息性交易对 (参考交易对) 的数据，以便在更长的时间周期内查看数据。
  
  这些交易对的 OHLCV 数据将作为常规白名单刷新过程的一部分下载，并且可以通过 DataProvider 获得，就像其他交易对一样 (见下文)。
  
  除非这些交易对也指定在交易对白名单中，或者已被动态白名单 (例如 VolumePairList) 选中，否则不会对这些交易对进行交易。
  
  需要以元组的形式指定交易对，格式为 `("pair", "timeframe")`，其中 `pair` 作为第一个参数，`timeframe` 作为第二个参数。
  
  示例：
  
  ```python
  def informative_pairs(self):
    return [("ETH/USDT", "5m"),
            ("BTC/TUSD", "15m"),
            ]
  ```
  
  可以在[DataProvider](https://www.freqtrade.io/en/stable/strategy-advanced/#additional-data-dataprovider)部分找到完整示例。
  
  **警告**
  
  由于这些交易对将作为常规白名单刷新的一部分进行刷新，因此最好保持此列表简短。只要在使用的交易所上可用 (且活跃)，就可以指定所有时间周期和所有交易对。然而，最好尽可能使用重新采样到更长的时间周期，以避免用太多请求冲击交易所并面临被阻止的风险。
  
  **替代 K 线类型**
  
  **信息性交易对装饰器 (@informative())**
  
  为了轻松定义信息性交易对，请使用 `@informative` 装饰器。所有装饰的 `populate_indicators_*` 方法都独立运行，并且无法访问来自其他信息性交易对的数据。但是，每个交易对的所有信息性数据帧都将被合并并传递给主要的 `populate_indicators()` 方法。
  
  **注意**
  
  如果你需要在一个信息性交易对生成另一个信息性交易对时使用该交易对的数据，请不要使用 `@informative` 装饰器。相反，请按照 [DataProvider](https://www.freqtrade.io/en/stable/strategy-advanced/#additional-data-dataprovider) 部分所述手动定义信息性交易对。
  
  进行超参数优化时，不支持使用可超参数化参数的 `.value` 属性。请使用 `.range` 属性。查看[优化指标参数](https://www.freqtrade.io/en/stable/hyperopt/#optimizing-an-indicator-parameter)了解更多信息。
  
  **完整文档**
  
  快速轻松地定义信息性交易对
  
  **注意**
  
  访问其他交易对的信息性数据帧时，请使用字符串格式化。这将允许在配置中轻松更改本金货币，而无需调整策略代码。
  
  ```python
  def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
    stake = self.config['stake_currency']
    dataframe.loc[
        (
            (dataframe[f'btc_{stake}_rsi_1h'] < 35)
            &
            (dataframe['volume'] > 0)
        ),
        ['enter_long', 'enter_tag']] = (1, 'buy_signal_rsi')
  
    return dataframe
  ```
  
  或者可以使用列重命名来从列名中删除本金货币：`@informative('1h', 'BTC/{stake}', fmt='{base}_{column}_{timeframe}')`。
  
  **重复的方法名称**
  
  使用 `@informative()` 装饰器标记的方法必须始终具有唯一的名称！重用相同的名称 (例如，复制粘贴已经定义的信息性方法时) 将覆盖先前定义的方法，并且由于 Python 编程语言的限制，不会产生任何错误。在这种情况下，你会发现策略文件中较高位置的方法中创建的指标在数据帧中不可用。仔细检查方法名称并确保它们是唯一的！
  
  **merge_informative_pair()**
  
  此方法可帮助你安全且一致地将信息性交易对合并到常规主数据帧中，而不会产生提前查看偏差。
  
  选项：
  
  *   重命名列以创建唯一的列
  *   合并数据帧而不会产生提前查看偏差
  *   前向填充 (可选)
  
  有关完整示例，请参阅下面的完整数据提供程序示例。
  
  信息性数据帧的所有列都将在返回的数据帧中以重命名的方式提供：
  
  **列重命名**
  
  假设 `inf_tf` = '1d'，生成的列将为：
  
  ```
  'date', 'open', 'high', 'low', 'close', 'rsi'                     # 来自原始数据帧
  'date_1d', 'open_1d', 'high_1d', 'low_1d', 'close_1d', 'rsi_1d'   # 来自信息性数据帧
  ```
  
  **列重命名 - 1h**
  
  **自定义实现**
  
  **信息性时间周期 < 时间周期**
  
  不建议将此方法与小于主数据帧时间周期的信息性时间周期一起使用，因为它不会使用这将提供的任何其他信息。要正确使用更详细的信息，应使用更高级的方法 (这些方法不在本文档的范围内)。
  
  **附加数据 (DataProvider)**
  
  该策略提供了对 [DataProvider](https://www.freqtrade.io/en/stable/data-provider/) 的访问。这允许你获取要在策略中使用的其他数据。
  
  所有方法在失败时都会返回 `None`，即失败不会引发异常。
  
  请始终检查操作模式以选择正确的方法来获取数据 (有关示例，请参见下文)。
  
  **超参数优化限制**
  
  DataProvider 在超参数优化期间可用，但是它只能在策略的 `populate_indicators()` 中使用，而不能在超参数优化类文件中使用。它在 `populate_entry_trend()` 和 `populate_exit_trend()` 方法中也不可用。
  
  **DataProvider 的可能选项**
  
  *   `available_pairs` - 包含已缓存交易对及其时间周期的元组列表的属性 (交易对, 时间周期)。
  *   `current_whitelist()` - 返回当前白名单交易对的列表。对于访问动态白名单 (即 VolumePairList) 很有用
  *   `get_pair_dataframe(pair, timeframe)` - 这是一个通用方法，它返回历史数据 (用于回测) 或缓存的实时数据 (用于模拟交易和实盘交易模式)。
  *   `get_analyzed_dataframe(pair, timeframe)` - 返回分析后的数据帧 (调用 `populate_indicators()`、`populate_buy()`、`populate_sell()` 之后) 以及最新分析的时间。
  *   `historic_ohlcv(pair, timeframe)` - 返回存储在磁盘上的历史数据。
  *   `market(pair)` - 返回交易对的市场数据：费用、限制、精度、活动标志等。有关市场数据结构的更多详细信息，请参阅 [ccxt 文档](https://docs.ccxt.com/#/README?id=market-structure)。
  *   `ohlcv(pair, timeframe)` - 交易对的当前缓存 K 线 (OHLCV) 数据，返回 DataFrame 或空 DataFrame。
  *   `orderbook(pair, maximum)` - 返回交易对的最新订单簿数据，一个包含买单/卖单的字典，总共包含最大条目。
  *   `ticker(pair)` - 返回交易对的当前行情数据。有关行情数据结构的更多详细信息，请参阅 [ccxt 文档](https://docs.ccxt.com/#/README?id=ticker-structure)。
  *   `runmode` - 包含当前运行模式的属性。
  
  **示例用法**
  
  **available_pairs**
  
  ```python
  for pair, timeframe in self.dp.available_pairs:
    print(f"available {pair}, {timeframe}")
  ```
  
  **current_whitelist()**
  
  假设你开发了一个策略，该策略使用按交易量排名的前 10 个交易所交易对的 1d 时间周期生成的信号在 5m 时间周期上进行交易。
  
  策略逻辑可能如下所示：
  
  每 5 分钟扫描一次按交易量排名的前 10 个交易对，并使用 14 天 RSI 进行开仓和平仓。
  
  由于可用数据有限，因此很难将 5m K 线重新采样为每日 K 线以用于 14 天 RSI。大多数交易所将用户限制为仅 500-1000 根 K 线，这实际上为我们提供了大约 1.74 根每日 K 线。我们至少需要 14 天！
  
  由于我们无法重新采样数据，我们将不得不使用信息性交易对，并且由于白名单将是动态的，我们不知道要使用哪个交易对！我们遇到了问题！
  
  这时调用 `self.dp.current_whitelist()` 来仅检索白名单中的那些交易对就派上用场了。
  
  ```python
    def informative_pairs(self):
  
        # 获取对白名单中所有可用交易对的访问权限。
        pairs = self.dp.current_whitelist()
        # 将时间周期分配给每个交易对，以便可以下载并缓存它们以供策略使用。
        informative_pairs = [(pair, '1d') for pair in pairs]
        return informative_pairs
  ```
  
  **使用 current_whitelist 绘图**
  
  **get_pair_dataframe(pair, timeframe)**
  
  ```python
  # 获取第一个信息性交易对的实时/历史 K 线 (OHLCV) 数据
  inf_pair, inf_timeframe = self.informative_pairs()[0]
  informative = self.dp.get_pair_dataframe(pair=inf_pair,
                                         timeframe=inf_timeframe)
  ```
  
  **关于回测的警告**
  
  在回测中，`dp.get_pair_dataframe()` 的行为会有所不同，具体取决于它的调用位置。在 `populate_*()` 方法中，`dp.get_pair_dataframe()` 返回完整的时间范围。请确保不要 “预见未来”，以避免在模拟/实盘模式下运行时出现意外。在回调函数中，你将获得直到当前 (模拟) K 线的完整时间范围。
  
  **get_analyzed_dataframe(pair, timeframe)**
  
  此方法由 freqtrade 内部使用以确定最后一个信号。它也可以在特定回调函数中使用以获取导致操作的信号 (有关可用回调函数的更多详细信息，请参阅[高级策略文档](https://www.freqtrade.io/en/stable/strategy-callbacks/))。
  
  ```python
  # 获取当前数据帧
  dataframe, last_updated = self.dp.get_analyzed_dataframe(pair=metadata['pair'],
                                                         timeframe=self.timeframe)
  ```
  
  **没有可用数据**
  
  如果未缓存请求的交易对，则返回一个空数据帧。你可以使用 `if dataframe.empty:` 检查此情况并相应地处理这种情况。使用白名单交易对时，这不应该发生。
  
  **orderbook(pair, maximum)**
  
  ```python
  if self.dp.runmode.value in ('live', 'dry_run'):
    ob = self.dp.orderbook(metadata['pair'], 1)
    dataframe['best_bid'] = ob['bids'][0][0]
    dataframe['best_ask'] = ob['asks'][0][0]
  ```
  
  订单簿结构与 ccxt 中的订单结构一致，因此结果将格式化如下：
  
  ```json
  {
    'bids': [
        [ price, amount ], // [ float, float ]
        [ price, amount ],
        ...
    ],
    'asks': [
        [ price, amount ],
        [ price, amount ],
        //...
    ],
    //...
  }
  ```
  
  因此，如上所示使用 `ob['bids'][0][0]` 将使用最佳买入价
- ```json
  {
      'bids': [
          [ price, amount ], // [ float, float ]
          [ price, amount ],
          ...
      ],
      'asks': [
          [ price, amount ],
          [ price, amount ],
          //...
      ],
      //...
  }
  ```
  
  因此，如上所示使用 `ob['bids'][0][0]` 将使用最佳买入价。`ob['bids'][0][1]` 将查看此订单簿位置的数量。
  
  **关于回测的警告**
  
  订单簿不是历史数据的一部分，这意味着如果使用此方法，回测和超参数优化将无法正常工作，因为该方法将返回最新值。
  
  **ticker(pair)**
  
  ```python
  if self.dp.runmode.value in ('live', 'dry_run'):
      ticker = self.dp.ticker(metadata['pair'])
      dataframe['last_price'] = ticker['last']
      dataframe['volume24h'] = ticker['quoteVolume']
      dataframe['vwap'] = ticker['vwap']
  ```
  
  **警告**
  
  尽管行情数据结构是 ccxt 统一接口的一部分，但此方法返回的值对于不同的交易所可能会有所不同。例如，许多交易所不返回 `vwap` 值，并且某些交易所并不总是填写 `last` 字段 (因此它可以是 `None`) 等。因此，你需要仔细验证从交易所返回的行情数据并添加适当的错误处理/默认值。
  
  **关于回测的警告**
  
  此方法将始终返回最新/实时值。因此，在回测/超参数优化期间使用而没有 `runmode` 检查将导致错误的结果，例如，你的整个数据帧将在所有行中包含相同的单个值。
  
  **发送通知**
  
  数据提供程序的 `.send_msg()` 函数允许你从策略中发送自定义通知。除非第二个参数 (`always_send`) 设置为 `True`，否则相同的通知每个 K 线只会发送一次。
  
  ```python
      self.dp.send_msg(f"{metadata['pair']} just got hot!")
  
      # 强制发送此通知，避免缓存 (请阅读下面的警告！)
      self.dp.send_msg(f"{metadata['pair']} just got hot!", always_send=True)
  ```
  
  通知只会在交易模式 (实盘/模拟) 中发送 - 因此可以在回测中无条件调用此方法。
  
  **滥发消息**
  
  通过在此方法中设置 `always_send=True`，你可能会滥发消息。请谨慎使用此方法，并且仅在你确定在整个 K 线期间不会发生的情况下使用，以避免每 5 秒收到一条消息。
  
  **完整的 DataProvider 示例**
  
  ```python
  from freqtrade.strategy import IStrategy, merge_informative_pair
  from pandas import DataFrame
  
  class SampleStrategy(IStrategy):
      # 策略初始化内容...
  
      timeframe = '5m'
  
      # 更多策略初始化内容...
  
      def informative_pairs(self):
  
          # 获取对白名单中所有可用交易对的访问权限。
          pairs = self.dp.current_whitelist()
          # 将时间周期分配给每个交易对，以便可以下载并缓存它们以供策略使用。
          informative_pairs = [(pair, '1d') for pair in pairs]
          # 可选地添加其他 "静态" 交易对
          informative_pairs += [("ETH/USDT", "5m"),
                                ("BTC/TUSD", "15m"),
                              ]
          return informative_pairs
  
      def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          if not self.dp:
              # 如果 DataProvider 不可用，则不执行任何操作。
              return dataframe
  
          inf_tf = '1d'
          # 获取信息性交易对
          informative = self.dp.get_pair_dataframe(pair=metadata['pair'], timeframe=inf_tf)
          # 获取 14 天 rsi
          informative['rsi'] = ta.RSI(informative, timeperiod=14)
  
          # 使用辅助函数 merge_informative_pair 安全地合并交易对
          # 自动重命名列并合并较短时间周期的数据帧和较长时间周期的信息性交易对
          # 使用 ffill 使 1d 值在一天中的每一行都可用。
          # 没有这一点，原始数据帧和信息性交易对的列之间的比较将每天只工作一次。
          # 此方法的完整文档，请参阅下文
          dataframe = merge_informative_pair(dataframe, informative, self.timeframe, inf_tf, ffill=True)
  
          # 计算原始数据帧 (5m 时间周期) 的 rsi
          dataframe['rsi'] = ta.RSI(dataframe, timeperiod=14)
  
          # 做其他事情
          # ...
  
          return dataframe
  
      def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
  
          dataframe.loc[
              (
                  (qtpylib.crossed_above(dataframe['rsi'], 30)) &  # 信号：RSI 向上穿越 30
                  (dataframe['rsi_1d'] < 30) &                     # 确保每日 RSI < 30
                  (dataframe['volume'] > 0)                        # 确保此 K 线有成交量 (对回测很重要)
              ),
              ['enter_long', 'enter_tag']] = (1, 'rsi_cross')
  
          return dataframe
  ```
  
  **附加数据 (钱包)**
  
  该策略提供了对钱包对象的访问。这包含你在交易所的钱包/账户的当前余额。
  
  **回测/超参数优化**
  
  钱包的行为会有所不同，具体取决于调用它的函数。在 `populate_*()` 方法中，它将返回配置的完整钱包。在回调函数中，你将获得与模拟过程中该点的实际模拟钱包相对应的钱包状态。
  
  始终检查钱包是否可用以避免回测期间出现故障。
  
  ```python
  if self.wallets:
      free_eth = self.wallets.get_free('ETH')
      used_eth = self.wallets.get_used('ETH')
      total_eth = self.wallets.get_total('ETH')
  ```
  
  **钱包的可能选项**
  
  *   `get_free(asset)` - 当前可用于交易的余额
  *   `get_used(asset)` - 当前占用的余额 (未成交订单)
  *   `get_total(asset)` - 可用余额总数 - 上述两项之和
  
  **附加数据 (交易)**
  
  可以通过查询数据库在策略中检索交易历史记录。
  
  在文件顶部，导入所需的对象：
  
  ```python
  from freqtrade.persistence import Trade
  ```
  
  以下示例查询今天当前交易对 (`metadata['pair']`) 的交易。可以轻松添加其他过滤器。
  
  ```python
  trades = Trade.get_trades_proxy(pair=metadata['pair'],
                                  open_date=datetime.now(timezone.utc) - timedelta(days=1),
                                  is_open=False,
              ]).order_by(Trade.close_date).all()
  # 汇总此交易对的利润。
  curdayprofit = sum(trade.close_profit for trade in trades)
  ```
  
  有关可用方法的完整列表，请参阅 [Trade 对象文档](https://www.freqtrade.io/en/stable/persistence/#trade-object)。
  
  **警告**
  
  在回测或超参数优化期间，交易历史记录在 `populate_*` 方法中不可用，并将导致空结果。
  
  **阻止某个交易对发生交易**
  
  当交易对平仓时，Freqtrade 会自动锁定该交易对的当前 K 线 (直到该 K 线结束)，从而防止立即重新开仓该交易对。
  
  这是为了防止在单个 K 线内发生许多频繁交易的 “瀑布”。
  
  锁定的交易对将显示消息 `Pair <pair> is currently locked..`。
  
  **从策略中锁定交易对**
  
  有时可能需要在某些事件发生后锁定交易对 (例如，连续多次亏损交易)。
  
  Freqtrade 有一个简单的方法可以从策略中执行此操作，通过调用 `self.lock_pair(pair, until, [reason])`。`until` 必须是将来的 `datetime` 对象，在此之后将重新启用该交易对的交易，而 `reason` 是一个可选字符串，详细说明锁定该交易对的原因。
  
  也可以通过调用 `self.unlock_pair(pair)` 或 `self.unlock_reason(<reason>)` 手动解除锁定，提供解锁交易对的原因。`self.unlock_reason(<reason>)` 将解锁当前使用提供的 `reason` 锁定的所有交易对。
  
  要验证交易对当前是否被锁定，请使用 `self.is_pair_locked(pair)`。
  
  **注意**
  
  锁定的交易对将始终向上舍入到下一个 K 线。因此，假设时间周期为 5 分钟，将 `until` 设置为 10:18 的锁定将锁定交易对，直到 10:15-10:20 的 K 线完成。
  
  **警告**
  
  在回测期间，手动锁定交易对不可用。只允许通过[保护](https://www.freqtrade.io/en/stable/strategy-advanced/#protection-mode)进行锁定。
  
  **交易对锁定示例**
  
  ```python
  from freqtrade.persistence import Trade
  from datetime import timedelta, datetime, timezone
  # 将以上几行放在策略文件的顶部，靠近所有其他导入
  # --------
  
  # 在 populate indicators (或 populate_entry_trend) 中：
  if self.config['runmode'].value in ('live', 'dry_run'):
      # 获取最近 2 天的已平仓交易
      trades = Trade.get_trades_proxy(
          pair=metadata['pair'], is_open=False,
          open_date=datetime.now(timezone.utc) - timedelta(days=2))
      # 分析你想要锁定交易对的条件 .... 每个策略可能会有所不同
      sumprofit = sum(trade.close_profit for trade in trades)
      if sumprofit < 0:
          # 锁定交易对 12 小时
          self.lock_pair(metadata['pair'], until=datetime.now(timezone.utc) + timedelta(hours=12))
  ```
  
  **打印主数据帧**
  
  要检查当前的主数据帧，你可以在 `populate_entry_trend()` 或 `populate_exit_trend()` 中发出打印语句。你可能还想打印交易对，以便清楚地显示当前显示的数据。
  
  ```python
  def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
      dataframe.loc[
          (
              #>> 任何条件 <<<
          ),
          ['enter_long', 'enter_tag']] = (1, 'somestring')
  
      # 打印分析后的交易对
      print(f"result for {metadata['pair']}")
  
      # 检查最后 5 行
      print(dataframe.tail())
  
      return dataframe
  ```
  
  也可以使用 `print(dataframe)` 而不是 `print(dataframe.tail())` 打印不止几行。但是，不建议这样做，因为可能导致大量输出 (每 5 秒每个交易对约 500 行)。
  
  **开发策略时的常见错误**
  
  **在回测时预见未来**
  
  出于性能原因，回测会一次分析整个数据帧时间范围。因此，策略作者需要确保策略不会预见未来，即使用在模拟或实盘模式下不可用的数据。
  
  这是一个常见的痛点，它可能导致回测和模拟/实盘运行方法之间存在巨大差异。预见未来的策略将在回测期间表现良好，通常具有令人难以置信的利润或胜率，但在实际条件下会失败或表现不佳。
  
  以下列表包含应避免的一些常见模式，以防止出现问题：
  
  *   不要使用 `shift(-1)` 或其他负值。这会在回测中使用未来的数据，这些数据在模拟或实盘模式下不可用。
  *   不要在 `populate_` 函数中使用 `.iloc[-1]` 或数据帧中的任何其他绝对位置，因为这在模拟运行和回测之间会有所不同。但是，在回调函数中可以安全使用绝对 `iloc` 索引 - 请参阅[策略回调函数](https://www.freqtrade.io/en/stable/strategy-callbacks/)。
  *   不要使用使用所有数据帧或列值的函数，例如 `dataframe['mean_volume'] = dataframe['volume'].mean()`。由于回测使用完整的数据帧，因此在数据帧中的任何点，'mean_volume' 序列都将包括来自未来的数据。请改用 `rolling()` 计算，例如 `dataframe['volume'].rolling(<window>).mean()`。
  *   不要使用 `.resample('1h')`。这使用周期间隔的左边界，因此将数据从小时边界移动到小时的开始。请改用 `.resample('1h', label='right')`。
  
  **识别问题**
  
  你应该始终使用两个辅助命令 [lookahead-analysis](https://www.freqtrade.io/en/stable/lookahead-analysis/) 和 [recursive-analysis](https://www.freqtrade.io/en/stable/recursive-analysis/)，它们可以分别帮助你以不同的方式找出策略中的问题。请将它们视为识别最常见问题的助手。每个命令的否定结果并不能保证不包含上述错误。
  
  **冲突的信号**
  
  当冲突的信号发生冲突时 (例如，'enter_long' 和 'exit_long' 都设置为 1)，freqtrade 将不执行任何操作并忽略开仓信号。这将避免开仓后立即平仓的交易。显然，这可能会导致错失开仓机会。
  
  以下规则适用，如果设置了 3 个信号中的多个，则开仓信号将被忽略：
  
  *   `enter_long` -> `exit_long`, `enter_short`
  *   `enter_short` -> `exit_short`, `enter_long`
  
  **更多策略思路**
  
  要获得更多策略思路，请前往[策略仓库](https://github.com/freqtrade/freqtrade-strategies)。请随意将它们用作示例，但结果将取决于当前的市场情况、使用的交易对等。因此，这些策略应仅用于学习目的，而不是真实世界的交易。请首先针对你的交易所/所需交易对回测策略，然后进行模拟运行以仔细评估，并在你自担风险的情况下使用。
  
  请随意使用它们中的任何一个作为你自己策略的灵感来源。我们很高兴接受包含新策略的拉取请求到仓库中。
  
  **后续步骤**
  
  现在你已经有了一个完美的策略，你可能想回测它。你的下一步是学习如何使用[回测](https://www.freqtrade.io/en/stable/backtesting/)。