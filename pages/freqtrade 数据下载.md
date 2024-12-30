- #freqtrade #crypto
- ## 数据下载
  
  **获取用于回测和超参数优化的数据**
  
  要下载回测和超参数优化所需的 (K 线 / OHLCV) 数据，请使用 `freqtrade download-data` 命令。
  
  如果没有指定额外的参数，freqtrade 将下载最近 30 天的 "1m" 和 "5m" 时间周期的数据。交易所和交易对将来自 `config.json`（如果使用 `-c/--config` 指定）。如果没有提供配置文件，则必须指定 `--exchange` 参数。
  
  你可以使用相对时间范围 (`--days 20`) 或绝对开始时间点 (`--timerange 20200101-`)。对于增量下载，应该使用相对时间范围方式。
  
  **提示: 更新现有数据**
  
  如果你已经在你的数据目录中有可用的回测数据，并且想要将这些数据更新到今天，freqtrade 将自动计算现有交易对的缺失时间范围，并且下载将从最新的可用时间点开始，直到 "现在"。此时既不需要 `--days` 也不需要 `--timerange` 参数。Freqtrade 将保留可用数据，并且只下载缺失的数据。
  
  如果你在插入没有数据的新交易对后更新现有数据，请使用 `--new-pairs-days xx` 参数。新交易对将被下载指定天数的数据，而旧交易对将仅更新缺失的数据。
  
  **用法**
  
  ```
  usage: freqtrade download-data [-h] [-v] [--logfile FILE] [-V] [-c PATH]
                               [-d PATH] [--userdir PATH]
                               [-p PAIRS [PAIRS ...]] [--pairs-file FILE]
                               [--days INT] [--new-pairs-days INT]
                               [--include-inactive-pairs]
                               [--timerange TIMERANGE] [--dl-trades]
                               [--convert] [--exchange EXCHANGE]
                               [-t TIMEFRAMES [TIMEFRAMES ...]] [--erase]
                               [--data-format-ohlcv {json,jsongz,hdf5,feather,parquet}]
                               [--data-format-trades {json,jsongz,hdf5,feather,parquet}]
                               [--trading-mode {spot,margin,futures}]
                               [--prepend]
  
  options:
  -h, --help            显示此帮助信息并退出
  -p PAIRS [PAIRS ...], --pairs PAIRS [PAIRS ...]
                        将命令限制在这些交易对上。交易对之间用空格分隔。
  --pairs-file FILE     包含交易对列表的文件。优先于 `--pairs` 或配置文件中配置的交易对。
  --days INT            下载给定天数的数据。
  --new-pairs-days INT  下载新交易对的给定天数的数据。默认值：`None`。
  --include-inactive-pairs
                        也下载非活跃交易对的数据。
  --timerange TIMERANGE
                        指定要使用的数据的时间范围。
  --dl-trades           下载交易数据而不是 OHLCV 数据。机器人将根据 `--timeframes/-t` 指定的时间周期将交易数据重新采样。
  --convert             将下载的交易数据转换为 OHLCV 数据。仅在与 `--dl-trades` 结合使用时适用。对于没有历史 OHLCV 数据的交易所（例如 Kraken）将自动执行。如果没有提供，请使用 `trades-to-ohlcv` 将交易数据转换为 OHLCV 数据。
  --exchange EXCHANGE   交易所名称。仅在未提供配置时有效。
  -t TIMEFRAMES [TIMEFRAMES ...], --timeframes TIMEFRAMES [TIMEFRAMES ...]
                        指定要下载哪些时间周期的数据。空格分隔列表。默认值：`1m 5m`。
  --erase               清除所选交易所/交易对/时间周期的所有现有数据。
  --data-format-ohlcv {json,jsongz,hdf5,feather,parquet}
                        下载的 K 线 (OHLCV) 数据的存储格式。(默认值：`feather`)。
  --data-format-trades {json,jsongz,hdf5,feather,parquet}
                        下载的交易数据的存储格式。(默认值：`feather`)。
  --trading-mode {spot,margin,futures}, --tradingmode {spot,margin,futures}
                        选择交易模式
  --prepend             允许数据前置。(禁用数据追加)
  
  Common arguments:
  -v, --verbose         详细模式 (-vv 表示更多，-vvv 获取所有消息)。
  --logfile FILE, --log-file FILE
                        记录到指定的文件。特殊值为：'syslog', 'journald'。查看文档了解更多详情。
  -V, --version         显示程序的版本号并退出
  -c PATH, --config PATH
                        指定配置文件 (默认值：`userdir/config.json` 或 `config.json`，以存在的为准)。可以使用多个 `--config` 选项。可以设置为 `-` 以从标准输入读取配置。
  -d PATH, --datadir PATH, --data-dir PATH
                        包含历史回测数据的目录路径。
  --userdir PATH, --user-data-dir PATH
                        用户数据目录的路径。
  
  下载一种报价货币的所有数据
  
  通常，你会希望下载特定报价货币的所有交易对的数据。在这种情况下，你可以使用以下简写：`freqtrade download-data --exchange binance --pairs ".*/USDT" <...>`。提供的 "pairs" 字符串将被展开以包含交易平台上的所有活跃交易对。要同时下载非活跃（已下架）交易对的数据，请在命令中添加 `--include-inactive-pairs`。
  
  启动周期
  
  `download-data` 是一个独立于策略的命令。其理念是下载一大块数据一次，然后迭代地增加存储的数据量。
  
  因此，`download-data` 不关心策略中定义的 "启动周期"。由用户决定是否下载额外的天数，如果回测应该在特定的时间点开始（同时要考虑启动周期）。
  ```