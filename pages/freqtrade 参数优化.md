- #freqtrade
- 当然，以下是将您提供的内容翻译成中文：
  
  **Hyperopt（超参数优化）**
  
  本页解释了如何通过寻找最优参数来调整您的策略，这个过程被称为超参数优化。该机器人使用 scikit-optimize 包中包含的算法来完成这项任务。这个搜索会耗尽你所有的 CPU 核心，让你的笔记本电脑听起来像战斗机，并且仍然需要很长时间。
  
  一般来说，寻找最佳参数的搜索首先从一些随机组合开始（详见下文），然后使用带有 ML 回归算法（目前是 ExtraTreesRegressor）的贝叶斯搜索，以在搜索超空间中快速找到使损失函数值最小化的参数组合。
  
  Hyperopt 需要可用的历史数据，就像回测一样（hyperopt 运行多次回测，每次使用不同的参数）。要了解如何获取您感兴趣的交易对和交易所的数据，请前往文档的“数据下载”部分。
  
  **Bug**
  
  当仅使用 1 个 CPU 核心时，Hyperopt 可能会崩溃，如 Issue #1133 中发现的那样。
  
  **注意**
  
  自 2021.4 版本起，您不再需要编写单独的 hyperopt 类，而是可以直接在策略中配置参数。旧方法在 2021.8 版本之前都受支持，但在 2021.9 版本中已被移除。
  
  **安装 hyperopt 依赖项**
  
  由于 Hyperopt 依赖项不是运行机器人本身所必需的，而且体积庞大，在某些平台（如 Raspberry PI）上不易构建，因此默认情况下不安装它们。在运行 Hyperopt 之前，您需要安装相应的依赖项，如下节所述。
  
  **注意**
  
  由于 Hyperopt 是一个资源密集型过程，因此不建议也不支持在 Raspberry Pi 上运行它。
  
  **Docker**
  
  docker 镜像包含 hyperopt 依赖项，无需进一步操作。
  
  **简易安装脚本 (setup.sh) / 手动安装**
  
  ```bash
  source .venv/bin/activate
  pip install -r requirements-hyperopt.txt
  ```
  
  **Hyperopt 命令参考**
  
  ```
  usage: freqtrade hyperopt [-h] [-v] [--no-color] [--logfile FILE] [-V]
                            [-c PATH] [-d PATH] [--userdir PATH] [-s NAME]
                            [--strategy-path PATH] [--recursive-strategy-search]
                            [--freqaimodel NAME] [--freqaimodel-path PATH]
                            [-i TIMEFRAME] [--timerange TIMERANGE]
                            [--data-format-ohlcv {json,jsongz,feather,parquet}]
                            [--max-open-trades INT]
                            [--stake-amount STAKE_AMOUNT] [--fee FLOAT]
                            [-p PAIRS [PAIRS ...]] [--hyperopt-path PATH]
                            [--eps] [--enable-protections]
                            [--dry-run-wallet DRY_RUN_WALLET]
                            [--timeframe-detail TIMEFRAME_DETAIL] [-e INT]
                            [--spaces {all,buy,sell,roi,stoploss,trailing,protection,trades,default} [{all,buy,sell,roi,stoploss,trailing,protection,trades,default} ...]]
                            [--print-all] [--print-json] [-j JOBS]
                            [--random-state INT] [--min-trades INT]
                            [--hyperopt-loss NAME] [--disable-param-export]
                            [--ignore-missing-spaces] [--analyze-per-epoch]
  
  options:
    -h, --help            显示此帮助消息并退出
    -i TIMEFRAME, --timeframe TIMEFRAME
                          指定时间周期（`1m`、`5m`、`30m`、`1h`、`1d`）。
    --timerange TIMERANGE
                          指定要使用的数据时间范围。
    --data-format-ohlcv {json,jsongz,feather,parquet}
                          下载的蜡烛图 (OHLCV) 数据的存储格式。（默认值：`feather`）。
    --max-open-trades INT
                          覆盖 `max_open_trades` 配置设置的值。
    --stake-amount STAKE_AMOUNT
                          覆盖 `stake_amount` 配置设置的值。
    --fee FLOAT           指定手续费率。将应用两次（交易进入和退出时）。
    -p PAIRS [PAIRS ...], --pairs PAIRS [PAIRS ...]
                          将命令限制为这些交易对。交易对以空格分隔。
    --hyperopt-path PATH  指定 Hyperopt 损失函数的附加查找路径。
    --eps, --enable-position-stacking
                          允许多次购买相同的交易对（仓位叠加）。
    --enable-protections, --enableprotections
                          启用回测保护。这将大大减慢回测速度，但会包含配置的保护。
    --dry-run-wallet DRY_RUN_WALLET, --starting-balance DRY_RUN_WALLET
                          起始余额，用于回测/hyperopt 和模拟运行。
    --timeframe-detail TIMEFRAME_DETAIL
                          指定回测的详细时间周期（`1m`、`5m`、`30m`、`1h`、`1d`）。
    -e INT, --epochs INT  指定轮次数（默认值：100）。
    --spaces {all,buy,sell,roi,stoploss,trailing,protection,trades,default} [{all,buy,sell,roi,stoploss,trailing,protection,trades,default} ...]
                          指定要进行超参数优化的参数。以空格分隔的列表。
    --print-all           打印所有结果，而不仅仅是最佳结果。
    --print-json          以 JSON 格式打印输出。
    -j JOBS, --job-workers JOBS
                          用于超参数优化（hyperopt 工作进程）的并发运行作业数。如果为 -1（默认值），则使用所有 CPU，如果为 -2，则使用除一个 CPU 之外的所有 CPU，依此类推。如果给定 1，则根本不使用并行计算代码。
    --random-state INT    将随机状态设置为某个正整数，以获得可重现的 hyperopt 结果。
    --min-trades INT      为 hyperopt 优化路径中的评估设置最小期望交易数量（默认值：1）。
    --hyperopt-loss NAME, --hyperoptloss NAME
                          指定 hyperopt 损失函数类（IHyperOptLoss）的类名。不同的函数可能会产生完全不同的结果，因为优化的目标是不同的。内置的 Hyperopt 损失函数有：
                          ShortTradeDurHyperOptLoss、OnlyProfitHyperOptLoss、
                          SharpeHyperOptLoss、SharpeHyperOptLossDaily、
                          SortinoHyperOptLoss、SortinoHyperOptLossDaily、
                          CalmarHyperOptLoss、MaxDrawDownHyperOptLoss、
                          MaxDrawDownRelativeHyperOptLoss、
                          ProfitDrawDownHyperOptLoss、MultiMetricHyperOptLoss
    --disable-param-export
                          禁用自动 hyperopt 参数导出。
    --ignore-missing-spaces, --ignore-unparameterized-spaces
                          抑制任何请求的 Hyperopt 空间中不包含任何参数的错误。
    --analyze-per-epoch   每个轮次运行一次 populate_indicators。
  
  通用参数：
    -v, --verbose         详细模式（-vv 更详细，-vvv 获取所有消息）。
    --no-color            禁用 hyperopt 结果的颜色化。如果您将输出重定向到文件，这可能很有用。
    --logfile FILE, --log-file FILE
                          记录到指定的文件。特殊值包括：“syslog”、“journald”。有关更多详细信息，请参阅文档。
    -V, --version         显示程序版本号并退出
    -c PATH, --config PATH
                          指定配置文件（默认值：`userdir/config.json` 或 `config.json`，以存在的为准）。可以使用多个 --config 选项。可以设置为 `-` 以从 stdin 读取配置。
    -d PATH, --datadir PATH, --data-dir PATH
                          历史回测数据的目录路径。
    --userdir PATH, --user-data-dir PATH
                          userdata 目录的路径。
  
  策略参数：
    -s NAME, --strategy NAME
                          指定机器人将使用的策略类名。
    --strategy-path PATH  指定附加策略查找路径。
    --recursive-strategy-search
                          在策略文件夹中递归搜索策略。
    --freqaimodel NAME    指定自定义 freqaimodels。
    --freqaimodel-path PATH
                          指定 freqaimodels 的附加查找路径。
  ```
  
  **Hyperopt 清单**
  
  Hyperopt 中所有任务/可能性的清单
  
  根据您要优化的空间，只需要以下部分内容：
  
  *   定义带有 `space='buy'` 的参数 - 用于入场信号优化
  *   定义带有 `space='sell'` 的参数 - 用于出场信号优化
  
  **注意**
  
  `populate_indicators` 需要创建任何空间可能使用的所有指标，否则 hyperopt 将无法工作。
  
  在极少数情况下，您可能还需要创建一个名为 `HyperOpt` 的嵌套类并实现：
  
  *   `roi_space` - 用于自定义 ROI 优化（如果您需要的优化超空间中 ROI 参数的范围与默认值不同）
  *   `generate_roi_table` - 用于自定义 ROI 优化（如果您需要的 ROI 表中值的范围与默认值不同，或者 ROI 表中的条目（步数）数量与默认的 4 步不同）
  *   `stoploss_space` - 用于自定义止损优化（如果您需要的优化超空间中止损参数的范围与默认值不同）
  *   `trailing_space` - 用于自定义追踪止损优化（如果您需要的优化超空间中追踪止损参数的范围与默认值不同）
  *   `max_open_trades_space` - 用于自定义最大开仓交易数优化（如果您需要的优化超空间中最大开仓交易数参数的范围与默认值不同）
  
  **快速优化 ROI、止损和追踪止损**
  
  您可以快速优化 `roi`、`stoploss` 和 `trailing` 空间，而无需更改策略中的任何内容。
  
  ```bash
  # 准备一个可用的策略。
  freqtrade hyperopt --hyperopt-loss SharpeHyperOptLossDaily --spaces roi stoploss trailing --strategy MyWorkingStrategy --config config.json -e 100
  ```
  
  **Hyperopt 执行逻辑**
  
  Hyperopt 将首先将您的数据加载到内存中，然后为每个交易对运行一次 `populate_indicators()` 以生成所有指标，除非指定了 `--analyze-per-epoch`。
  
  然后，Hyperopt 将分叉到不同的进程中（处理器数量，或 `-j <n>`），并一遍又一遍地运行回测，更改 `--spaces` 定义中包含的参数。
  
  对于每个新的参数集，freqtrade 将首先运行 `populate_entry_trend()`，然后运行 `populate_exit_trend()`，然后运行常规的回测过程来模拟交易。
  
  回测后，结果将传递到损失函数中，损失函数将评估此结果是优于还是劣于以前的结果。
  根据损失函数的结果，hyperopt 将确定在下一轮回测中尝试的下一组参数。
  
  **配置您的守卫条件和触发器**
  
  您需要在策略文件中更改两个地方以添加新的买入 hyperopt 进行测试：
  
  1.  在类级别定义 hyperopt 应优化的参数。
  2.  在 `populate_entry_trend()` 中 - 使用定义的参数值而不是原始常量。
  
  这里有两种不同类型的指标：1. 守卫条件 和 2. 触发器。
  
  *   守卫条件是诸如“如果 ADX < 10 永远不要买入”，或者如果当前价格高于 EMA10 永远不要买入之类的条件。
  *   触发器是实际在特定时刻触发买入的指标，例如“当 EMA5 上穿 EMA10 时买入”或“当收盘价触及下布林带时买入”。
  
  **守卫条件和触发器**
  
  从技术上讲，守卫条件和触发器之间没有区别。
  但是，本指南将区分它们，以明确信号不应“粘滞”。粘滞信号是指在多个蜡烛图上都活跃的信号。这可能导致延迟进入信号（就在信号消失之前 - 这意味着成功的机会远低于信号刚开始时）。
  
  对于每个轮次，超参数优化将选择一个触发器，并可能选择多个守卫条件。
  
  **退出信号优化**
  
  与上面的入场信号类似，也可以优化退出信号。将相应的设置放入以下方法中：
  
  1.  在类级别定义 hyperopt 应优化的参数，可以命名为 `sell_*`，或者显式定义 `space='sell'`。
  2.  在 `populate_exit_trend()` 中 - 使用定义的参数值而不是原始常量。
  
  配置和规则与买入信号相同。
  
  **解开谜团**
  
  假设您很好奇：您应该使用 MACD 交叉还是下布林带来触发您的多头入场。您还想知道是否应该使用 RSI 或 ADX 来辅助这些决策。如果您决定使用 RSI 或 ADX，那么您应该为它们使用哪些值？
  
  因此，让我们使用超参数优化来解开这个谜团。
  
  **定义要使用的指标**
  
  我们首先计算策略将要使用的指标。
  
  ```python
  class MyAwesomeStrategy(IStrategy):
  
      def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          """
          生成策略使用的所有指标
          """
          dataframe['adx'] = ta.ADX(dataframe)
          dataframe['rsi'] = ta.RSI(dataframe)
          macd = ta.MACD(dataframe)
          dataframe['macd'] = macd['macd']
          dataframe['macdsignal'] = macd['macdsignal']
          dataframe['macdhist'] = macd['macdhist']
  
          bollinger = ta.BBANDS(dataframe, timeperiod=20, nbdevup=2.0, nbdevdn=2.0)
          dataframe['bb_lowerband'] = bollinger['lowerband']
          dataframe['bb_middleband'] = bollinger['middleband']
          dataframe['bb_upperband'] = bollinger['upperband']
          return dataframe
  ```
  
  **可进行超参数优化的参数**
  
  我们继续定义可进行超参数优化的参数：
  
  ```python
  class MyAwesomeStrategy(IStrategy):
      buy_adx = DecimalParameter(20, 40, decimals=1, default=30.1, space="buy")
      buy_rsi = IntParameter(20, 40, default=30, space="buy")
      buy_adx_enabled = BooleanParameter(default=True, space="buy")
      buy_rsi_enabled = CategoricalParameter([True, False], default=False, space="buy")
      buy_trigger = CategoricalParameter(["bb_lower", "macd_cross_signal"], default="bb_lower", space="buy")
  ```
  
  上面的定义说明：我有五个参数想要随机组合，以找到最佳组合。
  `buy_rsi` 是一个整数参数，将在 20 到 40 之间进行测试。这个空间的尺寸为 20。
  `buy_adx` 是一个十进制参数，将在 20 到 40 之间进行评估，精度为 1 位小数（因此值为 20.1、20.2、...）。这个空间的尺寸为 200。
  然后我们有三个类别变量。前两个变量都是 True 或 False。我们使用它们来启用或禁用 ADX 和 RSI 守卫条件。最后一个我们称之为触发器，并使用它来决定我们要使用哪个买入触发器。
  
  **参数空间分配**
  
  参数必须分配给名为 `buy_*` 或 `sell_*` 的变量 - 或者包含 `space='buy'` | `space='sell'` 才能正确分配到空间。如果某个空间没有可用参数，则在运行 hyperopt 时您将收到未找到空间的错误。
  空间不明确的参数（例如 `adx_period = IntParameter(4, 24, default=14)` - 没有显式或隐式空间）将不会被检测到，因此将被忽略。
  
  因此，让我们使用这些值编写买入策略：
  
  ```python
      def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          conditions = []
          # 守卫条件和趋势
          if self.buy_adx_enabled.value:
              conditions.append(dataframe['adx'] > self.buy_adx.value)
          if self.buy_rsi_enabled.value:
              conditions.append(dataframe['rsi'] < self.buy_rsi.value)
  
          # 触发器
          if self.buy_trigger.value == 'bb_lower':
              conditions.append(dataframe['close'] < dataframe['bb_lowerband'])
          if self.buy_trigger.value == 'macd_cross_signal':
              conditions.append(qtpylib.crossed_above(
                  dataframe['macd'], dataframe['macdsignal']
              ))
  
          # 检查成交量是否不为 0
          conditions.append(dataframe['volume'] > 0)
  
          if conditions:
              dataframe.loc[
                  reduce(lambda x, y: x & y, conditions),
                  'enter_long'] = 1
  
          return dataframe
  ```
  
  现在，Hyperopt 将调用 `populate_entry_trend()` 许多次（轮次），每次使用不同的值组合。
  它将使用给定的历史数据，并根据使用上述函数生成的买入信号模拟买入。
  根据结果，hyperopt 将告诉您哪个参数组合产生了最佳结果（基于配置的损失函数）。
  
  **注意**
  
  上述设置期望在填充的指标中找到 ADX、RSI 和布林带。当您想要测试当前机器人未使用的指标时，请记住将其添加到策略或 hyperopt 文件中的 `populate_indicators()` 方法中。
  
  **参数类型**
  
  有四种参数类型，每种都适用于不同的目的。
  
  *   `IntParameter` - 定义一个整数参数，具有搜索空间的上限和下限。
  *   `DecimalParameter` - 定义一个浮点参数，小数位数有限（默认为 3）。在大多数情况下，应优先选择它而不是 `RealParameter`。
  *   `RealParameter` - 定义一个浮点参数，具有上限和下限，且精度没有限制。很少使用，因为它创建了一个具有近乎无限可能性的空间。
  *   `CategoricalParameter` - 定义一个具有预定数量选项的参数。
  *   `BooleanParameter` - `CategoricalParameter([True, False])` 的简写 - 非常适合“启用”参数。
  
  **参数选项**
  
  有两个参数选项可以帮助您快速测试各种想法：
  
  *   `optimize` - 当设置为 `False` 时，该参数将不包含在优化过程中。（默认值：`True`）
  *   `load` - 当设置为 `False` 时，先前 hyperopt 运行的结果（策略或 JSON 输出文件中的 `buy_params` 和 `sell_params`）将不会用作后续 hyperopts 的起始值。将使用参数中指定的默认值。（默认值：`True`）
  
  **`load=False` 对回测的影响**
  
  请注意，将 `load` 选项设置为 `False` 将意味着回测也将使用参数中指定的默认值，而不是通过超参数优化找到的值。
  
  **警告**
  
  可进行超参数优化的参数不能在 `populate_indicators` 中使用 - 因为 hyperopt 不会为每个轮次重新计算指标，因此在这种情况下将使用起始值。
  
  **优化指标参数**
  
  假设您心中有一个简单的策略 - EMA 交叉策略（两条移动平均线交叉） - 并且您想找到此策略的理想参数。默认情况下，我们假设止损为 5% - 并且获利了结（`minimal_roi`）为 10% - 这意味着 freqtrade 一旦达到 10% 的利润就会卖出交易。
  
  ```python
  from pandas import DataFrame
  from functools import reduce
  
  import talib.abstract as ta
  
  from freqtrade.strategy import (BooleanParameter, CategoricalParameter, DecimalParameter,
                                  IStrategy, IntParameter)
  import freqtrade.vendor.qtpylib.indicators as qtpylib
  
  class MyAwesomeStrategy(IStrategy):
      stoploss = -0.05
      timeframe = '15m'
      minimal_roi = {
          "0":  0.10
      }
      # 定义参数空间
      buy_ema_short = IntParameter(3, 50, default=5)
      buy_ema_long = IntParameter(15, 200, default=50)
  
  
      def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          """生成策略使用的所有指标"""
  
          # 计算所有 ema_short 值
          for val in self.buy_ema_short.range:
              dataframe[f'ema_short_{val}'] = ta.EMA(dataframe, timeperiod=val)
  
          # 计算所有 ema_long 值
          for val in self.buy_ema_long.range:
              dataframe[f'ema_long_{val}'] = ta.EMA(dataframe, timeperiod=val)
  
          return dataframe
  
      def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          conditions = []
          conditions.append(qtpylib.crossed_above(
                  dataframe[f'ema_short_{self.buy_ema_short.value}'], dataframe[f'ema_long_{self.buy_ema_long.value}']
              ))
  
          # 检查成交量是否不为 0
          conditions.append(dataframe['volume'] > 0)
  
          if conditions:
              dataframe.loc[
                  reduce(lambda x, y: x & y, conditions),
                  'enter_long'] = 1
          return dataframe
  
      def populate_exit_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          conditions = []
          conditions.append(qtpylib.crossed_above(
                  dataframe[f'ema_long_{self.buy_ema_long.value}'], dataframe[f'ema_short_{self.buy_ema_short.value}']
              ))
  
          # 检查成交量是否不为 0
          conditions.append(dataframe['volume'] > 0)
  
          if conditions:
              dataframe.loc[
                  reduce(lambda x, y: x & y, conditions),
                  'exit_long'] = 1
          return dataframe
  ```
  
  **分解说明：**
  
  使用 `self.buy_ema_short.range` 将返回一个范围对象，其中包含参数的低值和高值之间的所有条目。在本例中（`IntParameter(3, 50, default=5)`），循环将遍历 3 到 50 之间的所有数字（`[3, 4, 5, ... 49, 50]`）。通过在循环中使用它，hyperopt 将生成 48 个新列（`['buy_ema_3', 'buy_ema_4', ... , 'buy_ema_50']`）。
  
  然后，Hyperopt 本身将使用选定的值来创建买入和卖出信号。
  
  虽然此策略很可能过于简单而无法提供持续的利润，但它应该作为如何优化指标参数的示例。
  
  **注意**
  
  `self.buy_ema_short.range` 在 hyperopt 和其他模式下的行为会有所不同。对于 hyperopt，上面的示例可能会生成 48 个新列，但是对于所有其他模式（回测、模拟/实盘），它只会为选定的值生成列。因此，您应该避免使用带有显式值（除 `self.buy_ema_short.value` 之外的值）的结果列。
  
  **注意**
  
  `range` 属性也可以与 `DecimalParameter` 和 `CategoricalParameter` 一起使用。`RealParameter` 由于搜索空间无限，因此不提供此属性。
  
  **性能提示**
  
  **优化保护**
  
  Freqtrade 还可以优化保护机制。如何优化保护机制取决于您，以下内容仅应被视为示例。
  
  该策略只需将 "protections" 条目定义为属性，返回保护配置列表即可。
  
  ```python
  from pandas import DataFrame
  from functools import reduce
  
  import talib.abstract as ta
  
  from freqtrade.strategy import (BooleanParameter, CategoricalParameter, DecimalParameter,
                                  IStrategy, IntParameter)
  import freqtrade.vendor.qtpylib.indicators as qtpylib
  
  class MyAwesomeStrategy(IStrategy):
      stoploss = -0.05
      timeframe = '15m'
      # 定义参数空间
      cooldown_lookback = IntParameter(2, 48, default=5, space="protection", optimize=True)
      stop_duration = IntParameter(12, 200, default=5, space="protection", optimize=True)
      use_stop_protection = BooleanParameter(default=True, space="protection", optimize=True)
  
  
      @property
      def protections(self):
          prot = []
  
          prot.append({
              "method": "CooldownPeriod",
              "stop_duration_candles": self.cooldown_lookback.value
          })
          if self.use_stop_protection.value:
              prot.append({
                  "method": "StoplossGuard",
                  "lookback_period_candles": 24 * 3,
                  "trade_limit": 4,
                  "stop_duration_candles": self.stop_duration.value,
                  "only_per_pair": False
              })
  
          return prot
  
      def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          # ...
  ```
  
  然后您可以按如下方式运行 hyperopt：`freqtrade hyperopt --hyperopt-loss SharpeHyperOptLossDaily --strategy MyAwesomeStrategy --spaces protection`
  
  **注意**
  
  保护空间不是默认空间的一部分，并且仅在使用参数 Hyperopt 接口时可用，而不适用于旧版 hyperopt 接口（需要单独的 hyperopt 文件）。如果选择了保护空间，Freqtrade 还会自动更改 "--enable-protections" 标志。
  
  **警告**
  
  如果保护机制定义为属性，则配置文件中的条目将被忽略。因此，建议不要在配置文件中定义保护机制。
  
  **从以前的属性设置迁移**
  
  从以前的设置迁移非常简单，可以通过将 `protections` 条目转换为属性来完成。简单来说，以下配置将转换为以下内容。
  
  **之前**
  
  ```python
  class MyAwesomeStrategy(IStrategy):
      protections = [
          {
              "method": "CooldownPeriod",
              "stop_duration_candles": 4
          }
      ]
  ```
  
  **之后**
  
  ```python
  class MyAwesomeStrategy(IStrategy):
  
      @property
      def protections(self):
          return [
              {
                  "method": "CooldownPeriod",
                  "stop_duration_candles": 4
              }
          ]
  ```
  
  然后，您显然也会将潜在的有趣条目更改为参数，以允许超参数优化。
  
  **优化 max_entry_position_adjustment**
  
  虽然 `max_entry_position_adjustment` 不是一个单独的空间，但仍然可以通过使用上面显示的属性方法在 hyperopt 中使用它。
  
  ```python
  from pandas import DataFrame
  from functools import reduce
  
  import talib.abstract as ta
  
  from freqtrade.strategy import (BooleanParameter, CategoricalParameter, DecimalParameter,
                                  IStrategy, IntParameter)
  import freqtrade.vendor.qtpylib.indicators as qtpylib
  
  class MyAwesomeStrategy(IStrategy):
      stoploss = -0.05
      timeframe = '15m'
  
      # 定义参数空间
      max_epa = CategoricalParameter([-1, 0, 1, 3, 5, 10], default=1, space="buy", optimize=True)
  
      @property
      def max_entry_position_adjustment(self):
          return self.max_epa.value
  
  
      def populate_indicators(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          # ...
  ```
  
  **使用 IntParameter**
  
  **损失函数**
  
  每个超参数调整都需要一个目标。这通常定义为损失函数（有时也称为目标函数），对于更理想的结果，损失函数应该减小，对于糟糕的结果，损失函数应该增加。
  
  必须通过 `--hyperopt-loss <Class-name>` 参数（或可选地通过配置中的 `"hyperopt_loss"` 键）指定损失函数。此类应位于 `user_data/hyperopts/` 目录中的单独文件中。
  
  目前，内置了以下损失函数：
  
  *   `ShortTradeDurHyperOptLoss` -（默认的旧版 Freqtrade 超参数优化损失函数）- 主要用于缩短交易持续时间并避免损失。
  *   `OnlyProfitHyperOptLoss` - 仅考虑利润量。
  *   `SharpeHyperOptLoss` - 优化基于交易回报率相对于标准差计算的夏普比率。
  *   `SharpeHyperOptLossDaily` - 优化基于每日交易回报率相对于标准差计算的夏普比率。
  *   `SortinoHyperOptLoss` - 优化基于交易回报率相对于下行标准差计算的索提诺比率。
  *   `SortinoHyperOptLossDaily` - 优化基于每日交易回报率相对于下行标准差计算的索提诺比率。
  *   `MaxDrawDownHyperOptLoss` - 优化最大绝对回撤。
  *   `MaxDrawDownRelativeHyperOptLoss` - 优化最大绝对回撤，同时调整最大相对回撤。
  *   `CalmarHyperOptLoss` - 优化基于交易回报率相对于最大回撤计算的卡玛比率。
  *   `ProfitDrawDownHyperOptLoss` - 通过最大化利润和最小化回撤目标进行优化。可以调整 hyperoptloss 文件中的 `DRAWDOWN_MULT` 变量，以更严格或更灵活地处理回撤目的。
  *   `MultiMetricHyperOptLoss` - 通过多个关键指标进行优化，以实现平衡的性能。主要关注最大化利润和最小化回撤，同时还考虑其他指标，例如利润因子、期望比率和胜率。此外，它还对交易次数较少的轮次施加惩罚，鼓励具有足够交易频率的策略。
  
  自定义损失函数的创建在文档的“高级 Hyperopt”部分中介绍。
  
  **执行 Hyperopt**
  
  更新 hyperopt 配置后，即可运行它。由于 hyperopt 会尝试大量组合以找到最佳参数，因此需要花费时间才能获得良好的结果。
  
  我们强烈建议使用 `screen` 或 `tmux` 以防止任何连接丢失。
  
  ```bash
  freqtrade hyperopt --config config.json --hyperopt-loss <hyperoptlossname> --strategy <strategyname> -e 500 --spaces all
  ```
  
  `-e` 选项将设置 hyperopt 将执行的评估次数。由于 hyperopt 使用贝叶斯搜索，因此一次运行太多轮次可能不会产生更好的结果。经验表明，在 500-1000 轮次之后，最佳结果通常不会有太大改善。
  使用几千个轮次和不同的随机状态多次运行（执行）很可能会产生不同的结果。
  
  `--spaces all` 选项确定应优化所有可能的参数。可能性如下所列。
  
  **注意**
  
  Hyperopt 将使用 hyperopt 开始时间的时间戳存储 hyperopt 结果。读取命令（`hyperopt-list`，`hyperopt-show`）可以使用 `--hyperopt-filename <filename>` 来读取和显示较旧的 hyperopt 结果。您可以使用 `ls -l user_data/hyperopt_results/` 列出文件名。
  
  **使用不同的历史数据源执行 Hyperopt**
  
  如果您想使用磁盘上备用的历史数据集来对参数进行超参数优化，请使用 `--datadir PATH` 选项。默认情况下，hyperopt 使用来自 `user_data/data` 目录的数据。
  
  **使用较小的测试集运行 Hyperopt**
  
  使用 `--timerange` 参数更改要使用的测试集量。例如，要使用一个月的数据，请将 `--timerange 20210101-20210201`（从 2021 年 1 月到 2021 年 2 月）传递给 hyperopt 调用。
  
  完整命令：
  
  ```bash
  freqtrade hyperopt --strategy <strategyname> --timerange 20210101-20210201
  ```
  
  **使用更小的搜索空间运行 Hyperopt**
  
  使用 `--spaces` 选项限制 hyperopt 使用的搜索空间。让 Hyperopt 优化所有内容是一个非常庞大的搜索空间。通常，从仅搜索初始买入算法开始可能更有意义。或者，您可能只想为您出色的新买入策略优化止损或 ROI 表。
  
  合法值包括：
  
  *   `all`：优化所有内容
  *   `buy`：仅搜索新的买入策略
  *   `sell`：仅搜索新的卖出策略
  *   `roi`：仅优化策略的最小利润表
  *   `stoploss`：搜索最佳止损值
  *   `trailing`：搜索最佳追踪止损值
  *   `trades`：搜索最佳最大开仓交易数
  *   `protection`：搜索最佳保护参数（阅读保护部分，了解如何正确定义这些参数）
  *   `default`：除追踪止损和保护之外的所有内容
  *   任何上述值的空格分隔列表，例如 `--spaces roi stoploss`
  
  默认的 Hyperopt 搜索空间，在未指定 `--space` 命令行选项时使用，不包括追踪止损超空间。我们建议您在找到、验证其他超空间的最佳参数并将其粘贴到您的自定义策略后，单独运行追踪止损超空间的优化。
  
  **理解 Hyperopt 结果**
  
  Hyperopt 完成后，您可以使用结果更新您的策略。给定以下来自 hyperopt 的结果：
  
  ```
  最佳结果：
  
      44/100:    135 笔交易。平均利润 0.57%。总利润 0.03871918 BTC (0.7722%)。平均持续时间 180.4 分钟。目标：1.94367
  
      # 买入超空间参数：
      buy_params = {
          'buy_adx': 44,
          'buy_rsi': 29,
          'buy_adx_enabled': False,
          'buy_rsi_enabled': True,
          'buy_trigger': 'bb_lower'
      }
  ```
  
  您应该这样理解此结果：
  
  *   效果最佳的买入触发器是 `bb_lower`。
  *   您不应使用 ADX，因为 `'buy_adx_enabled': False`。
  *   您应考虑使用 RSI 指标（`'buy_rsi_enabled': True`），最佳值为 29.0（`'buy_rsi': 29.0`）。
  
  **自动将参数应用于策略**
  
  当使用可进行超参数优化的参数时，您的 hyperopt 运行结果将写入策略旁边的 json 文件中（因此对于 `MyAwesomeStrategy.py`，该文件将是 `MyAwesomeStrategy.json`）。
  当使用 `hyperopt-show` 子命令时，此文件也会更新，除非为这两个命令中的任何一个提供了 `--disable-param-export`。
  
  您的策略类也可以显式包含这些结果。只需复制 hyperopt 结果块并将其粘贴在类级别，替换旧参数（如果有）。下次执行策略时，将自动加载新参数。
  
  将您的整个 hyperopt 结果转移到您的策略将如下所示：
  
  ```python
  class MyAwesomeStrategy(IStrategy):
      # 买入超空间参数：
      buy_params = {
          'buy_adx': 44,
          'buy_rsi': 29,
          'buy_adx_enabled': False,
          'buy_rsi_enabled': True,
          'buy_trigger': 'bb_lower'
      }
  ```
  
  **注意**
  
  配置文件中的值将覆盖参数文件级别的参数 - 两者都将覆盖策略 `*_params` 中的参数和参数默认值。因此，优先级顺序为：配置 > 参数文件 > 策略 `*_params` > 参数默认值。
  
  **理解 Hyperopt ROI 结果**
  
  如果您正在优化 ROI（即，如果优化搜索空间包含 'all'、'default' 或 'roi'），您的结果将如下所示，并包含 ROI 表：
  
  ```
  最佳结果：
  
      44/100:    135 笔交易。平均利润 0.57%。总利润 0.03871918 BTC (0.7722%)。平均持续时间 180.4 分钟。目标：1.94367
  
      # ROI 表：
      minimal_roi = {
          0: 0.10674,
          21: 0.09158,
          78: 0.03634,
          118: 0
      }
  ```
  
  为了在回测和实盘交易/模拟运行中使用 Hyperopt 找到的最佳 ROI 表，请将其复制粘贴为您自定义策略的 `minimal_roi` 属性的值：
  
  ```python
      # 策略设计的最小 ROI。
      # 如果配置文件包含 "minimal_roi"，则将覆盖此属性
      minimal_roi = {
          0: 0.10674,
          21: 0.09158,
          78: 0.03634,
          118: 0
      }
  ```
  
  如注释中所述，您也可以将其用作配置文件中 `minimal_roi` 设置的值。
  
  **默认 ROI 搜索空间**
  
  如果您正在优化 ROI，Freqtrade 会为您创建 'roi' 优化超空间 -- 它是 ROI 表组件的超空间。默认情况下，Freqtrade 生成的每个 ROI 表都由 4 行（步）组成。Hyperopt 为 ROI 表实现了自适应范围，ROI 步中的值的范围取决于使用的时间周期。默认情况下，这些值在以下范围内变化（对于一些最常用的时间周期，值四舍五入到小数点后 3 位）：
  
  ```
  # 步 	1m 		5m 		1h 		1d
  1 	0 	0.011...0.119 	0 	0.03...0.31 	0 	0.068...0.711 	0 	0.121...1.258
  2 	2...8 	0.007...0.042 	10...40 	0.02...0.11 	120...480 	0.045...0.252 	2880...11520 	0.081...0.446
  3 	4...20 	0.003...0.015 	20...100 	0.01...0.04 	240...1200 	0.022...0.091 	5760...28800 	0.040...0.162
  4 	6...44 	0.0 	30...220 	0.0 	360...2640 	0.0 	8640...63360 	0.0
  ```
  
  这些范围在大多数情况下应该足够了。步骤中的分钟数（ROI 字典键）根据使用的时间周期线性缩放。步骤中的 ROI 值（ROI 字典值）根据使用的时间周期对数缩放。
  
  如果您的自定义 hyperopt 中有 `generate_roi_table()` 和 `roi_space()` 方法，请删除它们，以便利用这些自适应 ROI 表和 Freqtrade 默认生成的 ROI 超参数优化空间。
  
  如果您需要 ROI 表的组件在其他范围内变化，请覆盖 `roi_space()` 方法。如果您需要 ROI 表的不同结构或其他行数（步数），请覆盖 `generate_roi_table()` 和 `roi_space()` 方法，并实现您自己的自定义方法来在超参数优化期间生成 ROI 表。
  
  在“覆盖预定义空间”部分中可以找到这些方法的示例。
  
  **缩小搜索空间**
  
  为了进一步限制搜索空间，小数限制为 3 位小数（精度为 0.001）。这通常就足够了，任何比这更精确的值通常都会导致过度拟合的结果。但是，您可以覆盖预定义的空间以根据您的需要更改此设置。
  
  **理解 Hyperopt 止损结果**
  
  如果您正在优化止损值（即，如果优化搜索空间包含 'all'、'default' 或 'stoploss'），您的结果将如下所示，并包含止损：
  
  ```
  最佳结果：
  
      44/100:    135 笔交易。平均利润 0.57%。总利润 0.03871918 BTC (0.7722%)。平均持续时间 180.4 分钟。目标：1.94367
  
      # 买入超空间参数：
      buy_params = {
          'buy_adx': 44,
          'buy_rsi': 29,
          'buy_adx_enabled': False,
          'buy_rsi_enabled': True,
          'buy_trigger': 'bb_lower'
      }
  
      stoploss: -0.27996
  ```
  
  为了在回测和实盘交易/模拟运行中使用 Hyperopt 找到的最佳止损值，请将其复制粘贴为您自定义策略的 `stoploss` 属性的值：
  
  ```python
      # 策略设计的最佳止损
      # 如果配置文件包含 "stoploss"，则将覆盖此属性
      stoploss = -0.27996
  ```
  
  如注释中所述，您也可以将其用作配置文件中 `stoploss` 设置的值。
  
  **默认止损搜索空间**
  
  如果您正在优化止损值，Freqtrade 会为您创建 'stoploss' 优化超空间。默认情况下，该超空间中的止损值在 -0.35...-0.02 范围内变化，这在大多数情况下都足够了。
  
  如果您的自定义 hyperopt 文件中有 `stoploss_space()` 方法，请删除它，以便利用 Freqtrade 默认生成的止损超参数优化空间。
  
  如果您需要在超参数优化期间止损值在其他范围内变化，请覆盖 `stoploss_space()` 方法并在其中定义所需的范围。在“覆盖预定义空间”部分中可以找到此方法的示例。
  
  **缩小搜索空间**
  
  为了进一步限制搜索空间，小数限制为 3 位小数（精度为 0.001）。这通常就足够了，任何比这更精确的值通常都会导致过度拟合的结果。但是，您可以覆盖预定义的空间以根据您的需要更改此设置。
  
  **理解 Hyperopt 追踪止损结果**
  
  如果您正在优化追踪止损值（即，如果优化搜索空间包含 'all' 或 'trailing'），您的结果将如下所示，并包含追踪止损参数：
  
  ```
  最佳结果：
  
      45/100:    606 笔交易。平均利润 1.04%。总利润 0.31555614 BTC ( 630.48%)。平均持续时间 150.3 分钟。目标：-1.10161
  
      # 追踪止损：
      trailing_stop = True
      trailing_stop_positive = 0.02001
      trailing_stop_positive_offset = 0.06038
      trailing_only_offset_is_reached = True
  ```
  
  为了在回测和实盘交易/模拟运行中使用 Hyperopt 找到的最佳追踪止损参数，请将它们复制粘贴为您自定义策略的相应属性的值：
  
  ```python
      # 追踪止损
      # 如果配置文件包含相应的值，则将覆盖这些属性。
      trailing_stop = True
      trailing_stop_positive = 0.02001
      trailing_stop_positive_offset = 0.06038
      trailing_only_offset_is_reached = True
  ```
  
  如注释中所述，您也可以将其用作配置文件中相应设置的值。
  
  **默认追踪止损搜索空间**
  
  如果您正在优化追踪止损值，Freqtrade 会为您创建 'trailing' 优化超空间。默认情况下，在该超空间中，`trailing_stop` 参数始终设置为 `True`，`trailing_only_offset_is_reached` 的值在 `True` 和 `False` 之间变化，`trailing_stop_positive` 和 `trailing_stop_positive_offset` 参数的值分别在 0.02...0.35 和 0.01...0.1 范围内变化，这在大多数情况下都足够了。
  
  如果您需要在超参数优化期间追踪止损参数的值在其他范围内变化，请覆盖 `trailing_space()` 方法并在其中定义所需的范围。在“覆盖预定义空间”部分中可以找到此方法的示例。
  
  **缩小搜索空间**
  
  为了进一步限制搜索空间，小数限制为 3 位小数（精度为 0.001）。这通常就足够了，任何比这更精确的值通常都会导致过度拟合的结果。但是，您可以覆盖预定义的空间以根据您的需要更改此设置。
  
  **可重现的结果**
  
  寻找最佳参数的搜索首先从超参数空间中的一些（目前为 30 个）随机组合开始，即随机 Hyperopt 轮次。这些随机轮次在 Hyperopt 输出的第一列中标记有星号字符 (*)。
  
  生成这些随机值的初始状态（随机状态）由 `--random-state` 命令行选项的值控制。您可以将其设置为您选择的任意值，以获得可重现的结果。
  
  如果您未在命令行选项中显式设置此值，则 Hyperopt 会为您使用一些随机值播种随机状态。每个 Hyperopt 运行的随机状态值都会显示在日志中，因此您可以复制并将其粘贴到 `--random-state` 命令行选项中，以重复使用的初始随机轮次集。
  
  如果您没有更改命令行选项、配置、时间范围、策略和 Hyperopt 类、历史数据和损失函数中的任何内容 -- 您应该使用相同的随机状态值获得相同的超参数优化结果。
  
  **输出格式**
  
  默认情况下，hyperopt 会打印彩色结果 -- 具有正利润的轮次以绿色显示。此高亮显示可帮助您找到可能对稍后分析有用的轮次。总利润为零或负利润（损失）的轮次以正常颜色打印。如果您不需要结果的颜色化（例如，当您将 hyperopt 输出重定向到文件时），您可以通过在命令行中指定 `--no-color` 选项来关闭颜色化。
  
  如果您想在 hyperopt 输出中查看所有结果，而不仅仅是最佳结果，则可以使用 `--print-all` 命令行选项。当使用 `--print-all` 时，当前最佳结果默认也会进行颜色化 -- 它们以粗体（明亮）样式打印。也可以使用 `--no-color` 命令行选项关闭此功能。
  
  **Windows 和彩色输出**
  
  Windows 本身不支持彩色输出，因此会自动禁用彩色输出。要使在 Windows 下运行的 hyperopt 具有彩色输出，请考虑使用 WSL。
  
  **仓位叠加和禁用最大市场仓位**
  
  在某些情况下，您可能需要使用 `--eps/--enable-position-staking` 参数运行 Hyperopt（和回测），或者您可能需要将 `max_open_trades` 设置为非常高的数字以禁用对最大开仓交易数的限制。
  
  默认情况下，hyperopt 模拟 Freqtrade 实盘运行/模拟运行的行为，其中每个交易对仅允许一笔开仓交易。所有交易对的总开仓交易数也受到 `max_open_trades` 设置的限制。在 Hyperopt/回测期间，这可能会导致潜在的交易被已开仓交易隐藏（或屏蔽）。
  
  `--eps/--enable-position-stacking` 参数允许模拟多次购买相同的交易对。将 `--max-open-trades` 与非常高的数字一起使用将禁用对最大开仓交易数的限制。
  
  **注意**
  
  模拟/实盘运行将 *不* 使用仓位叠加 - 因此，在没有仓位叠加的情况下验证策略也是有意义的，因为它更接近现实。
  
  您还可以通过在配置文件中显式设置 `"position_stacking"=true` 来启用仓位叠加。
  
  **内存不足错误**
  
  由于 hyperopt 会消耗大量内存（每个并行回测进程都需要将完整数据加载到内存中一次），因此您很可能会遇到“内存不足”错误。为了解决这些问题，您有多种选择：
  
  *   减少交易对的数量。
  *   减少使用的时间范围 (`--timerange <timerange>`)。
  *   避免使用 `--timeframe-detail`（这会将大量额外数据加载到内存中）。
  *   减少并行进程数 (`-j <n>`)。
  *   增加机器的内存。
  *   如果您正在使用大量具有 `.range` 功能的参数，请使用 `--analyze-per-epoch`。
  
  **“目标在此点之前已评估过。”**
  
  如果您看到 “目标在此点之前已评估过。” - 那么这表明您的空间已耗尽，或者接近耗尽。基本上，您的空间中的所有点都已被命中（或者已达到局部最小值） - 并且 hyperopt 不再在多维空间中找到它尚未尝试过的点。在这种情况下，Freqtrade 尝试通过使用新的随机点来应对“局部最小值”问题。
  
  **示例：**
  
  ```python
  buy_ema_short = IntParameter(5, 20, default=10, space="buy", optimize=True)
  # 这是买入空间中唯一的参数
  ```
  
  `buy_ema_short` 空间有 15 个可能的值（5、6、... 19、20）。如果您现在为买入空间运行 hyperopt，则 hyperopt 将只有 15 个值可以尝试，然后才会耗尽选项。因此，您的轮次应与可能的值对齐 - 或者，如果您注意到大量 “目标在此点之前已评估过。” 警告，您应该准备好中断运行。
  
  **显示 Hyperopt 结果的详细信息**
  
  在您为所需的轮次数运行 Hyperopt 后，您可以稍后列出所有结果以进行分析，仅选择最佳或盈利结果，并显示先前评估的任何轮次的详细信息。这可以使用 `hyperopt-list` 和 `hyperopt-show` 子命令来完成。这些子命令的用法在“Utils”章节中描述。
  
  **从您的策略输出调试消息**
  
  如果您想从策略输出调试消息，可以使用 `logging` 模块。默认情况下，Freqtrade 将输出级别为 INFO 或更高级别的所有消息。
  
  ```python
  import logging
  
  logger = logging.getLogger(__name__)
  
  class MyAwesomeStrategy(IStrategy):
      ...
  
      def populate_entry_trend(self, dataframe: DataFrame, metadata: dict) -> DataFrame:
          logger.info("这是一条调试消息")
          ...
  ```
  
  **使用 `print`**
  
  除非禁用并行性 (`-j 1`)，否则通过 `print()` 打印的消息不会显示在 hyperopt 输出中。建议改用 `logging` 模块。
  
  **验证回测结果**
  
  将优化后的策略实施到您的策略后，您应该回测此策略以确保一切正常。
  
  为了获得与 Hyperopt 期间相同的结果（交易数量、交易持续时间、利润等），请使用与 hyperopt 相同的配置和参数（时间范围、时间周期等）进行回测。
  
  **为什么我的回测结果与我的 hyperopt 结果不匹配？**
  
  如果结果不匹配，请检查以下因素：
  
  *   您可能已在 `populate_indicators()` 中向 hyperopt 添加了参数，这些参数将仅为所有轮次计算一次。例如，如果您尝试优化多个 SMA 时间周期值，则可进行超参数优化的时间周期参数应放置在每次轮次都计算的 `populate_entry_trend()` 中。请参阅“优化指标参数”。
  *   如果您禁用了将 hyperopt 参数自动导出到 JSON 参数文件，请仔细检查以确保您已将所有超参数优化值正确传输到您的策略中。
  *   检查日志以验证正在设置哪些参数以及正在使用哪些值。
  *   特别注意止损、最大开仓交易数和追踪止损参数，因为这些参数通常在配置文件中设置，这将覆盖对策略的更改。检查回测日志以确保没有参数被配置意外设置（如止损、最大开仓交易数或追踪止损）。
  *   验证您没有意外的参数 JSON 文件覆盖策略中的参数或默认 hyperopt 设置。
  *   验证回测中启用的任何保护机制在进行超参数优化时也已启用，反之亦然。当使用 `--space protection` 时，保护机制会自动为超参数优化启用。