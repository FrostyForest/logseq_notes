- #crypto #freqtrade
- ## Freqtrade 策略 101：策略开发快速入门
  
  为了这篇快速入门的目的，我们假设你熟悉交易的基础知识，并且已经阅读了 Freqtrade 基础页面。
  
  **必备知识**
  
  Freqtrade 中的策略是一个 Python 类，它定义了买卖加密货币资产的逻辑。
  
  资产被定义为交易对，它代表了币种和本金。币种是你正在交易的资产，而另一种货币作为本金。
  
  数据由交易所提供，以 K 线的形式提供，K 线由六个值组成：日期、开盘价、最高价、最低价、收盘价和成交量。
  
  技术分析函数使用各种计算和统计公式分析 K 线数据，并产生称为指标的次级值。
  
  在资产对 K 线上分析指标以生成信号。
  
  信号在加密货币交易所转化为订单，即交易。
  
  我们使用术语 “开仓” 和 “平仓” 而不是 “买入” 和 “卖出”，因为 Freqtrade 同时支持做多和做空交易。
  
  *   **做多:** 你根据本金买入币种，例如使用 USDT 作为本金购买 BTC，并通过以高于你支付的价格出售币种来获利。在做多交易中，利润是通过币值相对于本金上涨来实现的。
  *   **做空:** 你以币的形式从交易所借入资金，稍后偿还该币的本金价值。在做空交易中，利润是通过币值相对于本金下跌来实现的 (你以较低的价格偿还贷款)。
  
  虽然 Freqtrade 支持某些交易所的现货和期货市场，但为了简单起见，我们将只关注现货 (做多) 交易。
  
  **基本策略的结构**
  
  **主数据帧 (dataframe)**
  
  Freqtrade 策略使用具有行和列的表格数据结构（称为数据帧）来生成开仓和平仓交易的信号。
  
  你配置的交易对列表中的每个交易对都有自己的数据帧。数据帧按日期列进行索引，例如 2024-06-31 12:00。
  
  接下来的 5 列代表开盘价、最高价、最低价、收盘价和成交量 (OHLCV) 数据。
  
  **填充指标值**
  
  `populate_indicators` 函数向数据帧添加表示技术分析指标值的列。
  
  常见指标的示例包括相对强弱指数 (RSI)、布林带、资金流量指数 (MFI)、移动平均线和平均真实波幅 (ATR)。
  
  通过调用技术分析函数（例如 ta-lib 的 RSI 函数 `ta.RSI()`）并将它们分配给列名（例如 `rsi`），将列添加到数据帧中。
  
  ```python
  dataframe['rsi'] = ta.RSI(dataframe)
  ```
  
  [技术分析库](https://www.freqtrade.io/en/stable/strategy-customization/#technical-analysis-libraries)
  
  **填充开仓信号**
  
  `populate_entry_trend` 函数定义开仓信号的条件。
  
  数据帧列 `enter_long` 被添加到数据帧中，当此列中的值为 1 时，Freqtrade 会看到一个开仓信号。
  **做空**
  
  **填充平仓信号**
  
  `populate_exit_trend` 函数定义平仓信号的条件。
  
  数据帧列 `exit_long` 被添加到数据帧中，当此列中的值为 1 时，Freqtrade 会看到一个平仓信号。
  **做空**
  
  **一个简单的策略**
  
  以下是一个 Freqtrade 策略的最小示例：
  
  ```python
  from freqtrade.strategy import IStrategy
  from pandas import DataFrame
  import talib.abstract as ta
  
  class MyStrategy(IStrategy):
  
    # 将初始止损设置为 -10%
    stoploss = -0.10
  
    # 当利润大于 1% 时，随时平仓获利
    minimal_roi = {"0": 0.01}
  
    def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        # 生成技术分析指标的值
        dataframe['rsi'] = ta.RSI(dataframe, timeperiod=14)
  
        return dataframe
  
    def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        # 根据指标值生成开仓信号
        dataframe.loc[
            (dataframe['rsi'] < 30),
            'enter_long'] = 1
  
        return dataframe
  
    def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
        # 根据指标值生成平仓信号
        dataframe.loc[
            (dataframe['rsi'] > 70),
            'exit_long'] = 1
  
        return dataframe
  ```
  
  **进行交易**
  
  当发现信号（开仓或平仓列中的 1）时，Freqtrade 将尝试下单，即交易或仓位。
  
  每个新的交易仓位占用一个槽位。槽位表示可以同时打开的最大新交易数量。
  
  槽位的数量由 `max_open_trades` 配置选项定义。
  
  然而，在各种情况下，生成信号并不总是会创建交易订单。这些情况包括：
  
  *   没有足够的剩余本金来购买资产，或钱包中没有足够的资金来出售资产 (包括任何费用)
  *   没有足够的剩余空闲槽位来开设新交易 (你已开仓的仓位数量等于 `max_open_trades` 选项)
  *   已经存在该交易对的未结交易 (Freqtrade 不能叠加仓位 - 但它可以调整现有仓位)
  *   如果在同一根 K 线上同时出现开仓和平仓信号，它们将被视为冲突，并且不会下单
  *   由于你使用相关开仓或平仓回调函数指定的逻辑，策略主动拒绝交易订单
  
  阅读[策略定制文档](https://www.freqtrade.io/en/stable/strategy-customization/)了解更多详情。
  
  **回测和前向测试**
  
  策略开发可能是一个漫长而令人沮丧的过程，因为将我们人类的 “直觉” 转变为一个有效的计算机控制 (“算法”) 策略并不总是那么简单。
  
  因此，应该对策略进行测试，以验证它是否会按预期工作。
  
  Freqtrade 有两种测试模式：
  
  *   **回测:** 使用你从交易所下载的历史数据，回测是评估策略性能的一种快速方法。然而，很容易扭曲结果，使策略看起来比实际盈利能力强得多。查看[回测文档](https://www.freqtrade.io/en/stable/backtesting/)了解更多信息。
  *   **模拟交易:** 通常被称为前向测试，模拟交易使用交易所的实时数据。然而，任何会导致交易的信号都会被 Freqtrade 正常跟踪，但不会在交易所本身开启任何交易。前向测试实时运行，因此虽然需要更长的时间才能获得结果，但它是比回测更可靠的潜在性能指标。
  
  通过在配置中将 `dry_run` 设置为 `true` 来启用模拟交易。
  
  **回测可能非常不准确**
  
  有很多原因可能导致回测结果与实际情况不符。请查看[回测假设和常见策略错误文档](https://www.freqtrade.io/en/stable/backtesting-assumptions/)。一些列出和排名 Freqtrade 策略的网站显示了令人印象深刻的回测结果。不要假设这些结果是可以实现的或现实的。
  
  **有用的命令**
  
  **评估回测和模拟交易结果**
  
  在回测策略后，始终对其进行模拟交易，以查看回测和模拟交易结果是否足够相似。
  
  如果有任何显著差异，请验证你的开仓和平仓信号是否一致，并在两种模式下的相同 K 线上出现。然而，模拟交易和回测之间总会有差异：
  
  *   回测假设所有订单都成交。在使用限价单或交易所没有交易量的情况下，模拟交易中可能并非如此。
  *   在 K 线收盘时出现开仓信号后，回测假设交易在下一根 K 线的开盘价成交 (除非你的策略中有自定义定价回调函数)。在模拟交易中，信号和交易开仓之间通常存在延迟。这是因为当你的主要时间周期（例如每 5 分钟）出现新的 K 线时，Freqtrade 需要时间来分析所有交易对的数据帧。因此，Freqtrade 将尝试在 K 线开盘后几秒钟 (理想情况下延迟尽可能小) 开仓。
  *   由于模拟交易中的开仓价格可能与回测不匹配，这意味着利润计算也将有所不同。因此，如果 ROI、止损、追踪止损和回调平仓不完全相同，这是正常的。
  *   新 K 线进入和你的信号被触发和交易被打开之间的计算 “延迟” 越多，价格不可预测性就越大。确保你的计算机足够强大，能够在合理的时间内处理你的交易对列表中所有交易对的数据。如果存在严重的数据处理延迟，Freqtrade 将在日志中警告你。
  
  **控制或监控正在运行的机器人**
  
  一旦你的机器人在模拟或实盘模式下运行，Freqtrade 有五种机制来控制或监控正在运行的机器人：
  
  *   **FreqUI:** 最容易上手的，FreqUI 是一个 Web 界面，用于查看和控制你的机器人的当前活动。
  *   **Telegram:** 在移动设备上，可以使用 Telegram 集成来获取有关你的机器人活动的警报并控制某些方面。
  *   **FTUI:** FTUI 是 Freqtrade 的终端 (命令行) 界面，仅允许监控正在运行的机器人。
  *   **REST API:** REST API 允许程序员开发自己的工具来与 Freqtrade 机器人交互。
  *   **Webhooks:** Freqtrade 可以通过 Webhooks 向其他服务 (例如 Discord) 发送信息。
  
  **日志**
  
  Freqtrade 生成广泛的调试日志，以帮助你了解发生了什么。请熟悉你在机器人日志中可能看到的信息和错误消息。
  
  **最后的思考**
  
  算法交易很困难，由于在多种情况下使策略盈利所需的时间和精力，大多数公共策略都不是好的执行者。
  
  因此，采用公共策略并使用回测作为评估性能的方式通常是有问题的。然而，Freqtrade 提供了有用的方法来帮助你做出决策并进行尽职调查。
  
  有很多不同的方法可以实现盈利，并且没有一个单一的技巧、窍门或配置选项可以修复表现不佳的策略。
  
  Freqtrade 是一个拥有庞大且乐于助人的社区的开源平台 - 请务必访问我们的 [discord 频道](https://discord.gg/freqtrade) 与其他人讨论你的策略！
  
  一如既往，只投资你愿意损失的资金。
  
  **结论**
  
  在 Freqtrade 中开发策略涉及根据技术指标定义开仓和平仓信号。通过遵循上面概述的结构和方法，你可以创建和测试你自己的交易策略。
  
  常见问题和答案可在我们的 [FAQ](https://www.freqtrade.io/en/stable/faq/) 中找到。
  
  要继续，请参阅更深入的 [Freqtrade 策略定制文档](https://www.freqtrade.io/en/stable/strategy-customization/)。