- #freqtrade
- 交易超过700天且交易额排前100的为：
	- 1000BONK/USDT:USDT
	   1000PEPE/USDT:USDT AAVE/USDT:USDT ADA/USDT:USDT ALGO/USDT:USDT 
	  APE/USDT:USDT APT/USDT:USDT ARB/USDT:USDT ATOM/USDT:USDT AVAX/USDT:USDT 
	  BCH/USDT:USDT BNB/USDT:USDT BSW/USDT:USDT BTC/USDT:USDT CAKE/USDT:USDT 
	  CRV/USDT:USDT DOGE/USDT:USDT DOT/USDT:USDT ENJ/USDT:USDT EOS/USDT:USDT 
	  ETH/USDT:USDT FIL/USDT:USDT FLM/USDT:USDT GALA/USDT:USDT GMT/USDT:USDT 
	  GRT/USDT:USDT HBAR/USDT:USDT ICP/USDT:USDT IMX/USDT:USDT INJ/USDT:USDT 
	  JASMY/USDT:USDT LINK/USDT:USDT LTC/USDT:USDT MAGIC/USDT:USDT 
	  MKR/USDT:USDT NEAR/USDT:USDT NKN/USDT:USDT OP/USDT:USDT ORDI/USDT:USDT 
	  PEOPLE/USDT:USDT RSR/USDT:USDT SHIB1000/USDT:USDT 
	  SOL/USDT:USDT STX/USDT:USDT SUI/USDT:USDT TRX/USDT:USDT UNI/USDT:USDT 
	  VET/USDT:USDT WLD/USDT:USDT XCN/USDT:USDT XLM/USDT:USDT XRP/USDT:USDT
- 交易在810天与540天且交易额排前150的：
	- 'SUI/USDT:USDT' 'ALPHA/USDT:USDT' '1000PEPE/USDT:USDT' 'WLD/USDT:USDT'  'NKN/USDT:USDT' 'SEI/USDT:USDT' 'TON/USDT:USDT' 'ARB/USDT:USDT'  'TIA/USDT:USDT' 'ORDI/USDT:USDT' 'MEME/USDT:USDT' 'ARK/USDT:USDT'  'LEVER/USDT:USDT' 'HIFI/USDT:USDT' 'KAS/USDT:USDT' 'BIGTIME/USDT:USDT' 'PENDLE/USDT:USDT' '1000FLOKI/USDT:USDT' 'PERP/USDT:USDT'  'GAS/USDT:USDT' 'ARKM/USDT:USDT' 'AUCTION/USDT:USDT'
- static pairlist形式：
	- [
	  "1000BONK/USDT:USDT", 
	      "1000PEPE/USDT:USDT", 
	      "AAVE/USDT:USDT", 
	      "ADA/USDT:USDT", 
	      "ALGO/USDT:USDT", 
	      "APE/USDT:USDT", 
	      "APT/USDT:USDT", 
	      "ARB/USDT:USDT", 
	      "ATOM/USDT:USDT", 
	      "AVAX/USDT:USDT", 
	      "BCH/USDT:USDT", 
	      "BNB/USDT:USDT", 
	      "BSW/USDT:USDT", 
	      "BTC/USDT:USDT", 
	      "CAKE/USDT:USDT", 
	      "CRV/USDT:USDT", 
	      "DOGE/USDT:USDT", 
	      "DOT/USDT:USDT", 
	      "ENJ/USDT:USDT", 
	      "EOS/USDT:USDT", 
	      "ETH/USDT:USDT", 
	      "FIL/USDT:USDT", 
	      "FLM/USDT:USDT", 
	      "GALA/USDT:USDT", 
	      "GMT/USDT:USDT", 
	      "GRT/USDT:USDT", 
	      "HBAR/USDT:USDT", 
	      "ICP/USDT:USDT", 
	      "IMX/USDT:USDT", 
	      "INJ/USDT:USDT", 
	      "JASMY/USDT:USDT", 
	      "LINK/USDT:USDT", 
	      "LTC/USDT:USDT", 
	      "MAGIC/USDT:USDT", 
	      "MKR/USDT:USDT", 
	      "NEAR/USDT:USDT", 
	      "NKN/USDT:USDT", 
	      "OP/USDT:USDT", 
	      "ORDI/USDT:USDT", 
	      "PEOPLE/USDT:USDT", 
	      "RSR/USDT:USDT", 
	      "SHIB1000/USDT:USDT", 
	      "SOL/USDT:USDT", 
	      "STX/USDT:USDT", 
	      "SUI/USDT:USDT", 
	      "TRX/USDT:USDT", 
	      "UNI/USDT:USDT", 
	      "VET/USDT:USDT", 
	      "WLD/USDT:USDT", 
	      "XCN/USDT:USDT", 
	      "XLM/USDT:USDT", 
	      "XRP/USDT:USDT"
	  ]
- 结果1，全部开仓机会均等：
	- ┏━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━┓
	  ┃               Pair ┃ Trades ┃ Avg Profit % ┃ Tot Profit USDT ┃ Tot Profit % ┃     Avg Duration ┃  Win  Draw  Loss  Win% ┃
	  ┡━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━┩
	  │      XLM/USDT:USDT │    198 │         3.98 │       44742.444 │       447.42 │  3 days, 1:57:00 │   64     0   134  32.3 │
	  │      XCN/USDT:USDT │    186 │         8.63 │       32705.473 │       327.05 │  3 days, 7:31:00 │   58     0   128  31.2 │
	  │      XRP/USDT:USDT │    189 │         0.37 │       23512.814 │       235.13 │  3 days, 3:47:00 │   54     0   135  28.6 │
	  │     DOGE/USDT:USDT │    170 │         4.83 │       21764.341 │       217.64 │ 3 days, 11:37:00 │   64     0   106  37.6 │
	  │     HBAR/USDT:USDT │    183 │         3.33 │       20325.791 │       203.26 │  3 days, 8:46:00 │   66     0   117  36.1 │
	  │     CAKE/USDT:USDT │     97 │          8.2 │       19240.194 │        192.4 │ 3 days, 17:43:00 │   49     0    48  50.5 │
	  │ 1000BONK/USDT:USDT │    148 │        11.66 │       17581.219 │       175.81 │ 3 days, 20:24:00 │   62     0    86  41.9 │
	  │      ARB/USDT:USDT │    142 │         2.98 │       17324.347 │       173.24 │  3 days, 8:46:00 │   56     0    86  39.4 │
	  │      FIL/USDT:USDT │    168 │         3.86 │       14575.432 │       145.75 │ 3 days, 16:19:00 │   73     0    95  43.5 │
	  │      ADA/USDT:USDT │    183 │         1.42 │       14462.779 │       144.63 │  3 days, 8:15:00 │   71     0   112  38.8 │
	  │      WLD/USDT:USDT │    126 │         5.07 │       14376.835 │       143.77 │  3 days, 4:40:00 │   50     0    76  39.7 │
	  │      FLM/USDT:USDT │    189 │         1.51 │       14295.286 │       142.95 │  3 days, 9:25:00 │   72     0   117  38.1 │
	  │      GRT/USDT:USDT │    184 │          3.3 │       14100.241 │        141.0 │ 3 days, 10:08:00 │   69     0   115  37.5 │
	  │      ENJ/USDT:USDT │    176 │         3.08 │       13188.606 │       131.89 │  3 days, 9:56:00 │   67     0   109  38.1 │
	  │     ALGO/USDT:USDT │    183 │         1.54 │       13010.708 │       130.11 │  3 days, 8:45:00 │   76     0   107  41.5 │
	  │      NKN/USDT:USDT │    151 │         2.13 │       12934.588 │       129.35 │ 3 days, 14:29:00 │   58     0    93  38.4 │
	  │      STX/USDT:USDT │    187 │         2.76 │       12701.189 │       127.01 │  3 days, 8:59:00 │   70     0   117  37.4 │
	  │      GMT/USDT:USDT │    171 │         3.87 │       12477.371 │       124.77 │ 3 days, 12:19:00 │   74     0    97  43.3 │
	  │      DOT/USDT:USDT │    176 │         1.81 │       12022.881 │       120.23 │  3 days, 9:56:00 │   72     0   104  40.9 │
	  │     AVAX/USDT:USDT │    187 │         2.94 │       11393.786 │       113.94 │ 3 days, 11:04:00 │   69     0   118  36.9 │
	  │      INJ/USDT:USDT │    182 │         2.82 │       11354.654 │       113.55 │ 3 days, 13:01:00 │   76     0   106  41.8 │
	  │ SHIB1000/USDT:USDT │    177 │         3.06 │       11300.803 │       113.01 │  3 days, 6:28:00 │   62     0   115  35.0 │
	  │      BSW/USDT:USDT │    181 │         1.37 │       11088.189 │       110.88 │  3 days, 9:55:00 │   63     0   118  34.8 │
	  │    MAGIC/USDT:USDT │    173 │          1.7 │       10692.950 │       106.93 │  3 days, 8:24:00 │   62     0   111  35.8 │
	  │      SUI/USDT:USDT │    139 │         1.47 │       10008.351 │       100.08 │  3 days, 5:58:00 │   55     0    84  39.6 │
	  │      RSR/USDT:USDT │    193 │         1.45 │        9875.151 │        98.75 │  3 days, 8:35:00 │   81     0   112  42.0 │
	  │     GALA/USDT:USDT │    179 │          3.4 │        9729.656 │         97.3 │ 3 days, 10:56:00 │   71     0   108  39.7 │
	  │     NEAR/USDT:USDT │    191 │         1.19 │        9725.931 │        97.26 │  3 days, 7:40:00 │   69     0   122  36.1 │
	  │      VET/USDT:USDT │    185 │         1.98 │        9560.842 │        95.61 │  3 days, 9:37:00 │   72     0   113  38.9 │
	  │     LINK/USDT:USDT │    197 │        -0.74 │        9175.453 │        91.75 │  3 days, 3:01:00 │   67     0   130  34.0 │
	  │     ORDI/USDT:USDT │    132 │         4.99 │        9151.577 │        91.52 │  3 days, 9:50:00 │   51     0    81  38.6 │
	  │      EOS/USDT:USDT │    188 │         0.79 │        8988.496 │        89.88 │  3 days, 5:04:00 │   60     0   128  31.9 │
	  │      APE/USDT:USDT │    174 │         0.69 │        8694.677 │        86.95 │ 3 days, 10:12:00 │   65     0   109  37.4 │
	  │      IMX/USDT:USDT │    189 │         0.45 │        8410.640 │        84.11 │ 3 days, 10:11:00 │   58     0   131  30.7 │
	  │      MKR/USDT:USDT │    189 │         0.72 │        7611.795 │        76.12 │ 3 days, 10:03:00 │   68     0   121  36.0 │
	  │      SOL/USDT:USDT │    182 │         3.01 │        7587.101 │        75.87 │ 3 days, 11:14:00 │   70     0   112  38.5 │
	  │ 1000PEPE/USDT:USDT │    139 │         11.9 │        7310.173 │         73.1 │  3 days, 6:51:00 │   52     0    87  37.4 │
	  │      BTC/USDT:USDT │     67 │         6.03 │        7090.056 │         70.9 │ 6 days, 12:50:00 │   31     0    36  46.3 │
	  │      UNI/USDT:USDT │    198 │        -1.52 │        6421.554 │        64.22 │ 2 days, 23:59:00 │   67     0   131  33.8 │
	  │      CRV/USDT:USDT │    187 │        -0.21 │        5808.721 │        58.09 │  3 days, 9:32:00 │   68     0   119  36.4 │
	  │    JASMY/USDT:USDT │    170 │         2.14 │        5644.102 │        56.44 │ 3 days, 15:13:00 │   65     0   105  38.2 │
	  │      APT/USDT:USDT │    189 │        -0.94 │        5023.250 │        50.23 │  3 days, 5:18:00 │   68     0   121  36.0 │
	  │       OP/USDT:USDT │    185 │         0.85 │        4898.015 │        48.98 │ 3 days, 10:48:00 │   71     0   114  38.4 │
	  │     AAVE/USDT:USDT │    197 │        -1.15 │        4603.326 │        46.03 │  3 days, 2:36:00 │   69     0   128  35.0 │
	  │     ATOM/USDT:USDT │    189 │        -1.02 │        4020.994 │        40.21 │  3 days, 3:44:00 │   69     0   120  36.5 │
	  │      LTC/USDT:USDT │    186 │        -0.94 │        3659.276 │        36.59 │  3 days, 3:05:00 │   69     0   117  37.1 │
	  │      BCH/USDT:USDT │    193 │        -0.24 │        3185.273 │        31.85 │  3 days, 3:02:00 │   68     0   125  35.2 │
	  │      ICP/USDT:USDT │    191 │         0.69 │        3101.872 │        31.02 │  3 days, 3:59:00 │   66     0   125  34.6 │
	  │      TRX/USDT:USDT │    192 │         -1.4 │        1715.431 │        17.15 │  3 days, 9:56:00 │   51     0   141  26.6 │
	  │      BNB/USDT:USDT │    177 │         0.19 │         227.759 │         2.28 │ 3 days, 13:18:00 │   57     0   120  32.2 │
	  │   PEOPLE/USDT:USDT │    185 │        -2.27 │        -543.396 │        -5.43 │  3 days, 6:02:00 │   66     0   119  35.7 │
	  │      ETH/USDT:USDT │    199 │        -1.75 │       -7508.015 │       -75.08 │  3 days, 3:22:00 │   61     0   138  30.7 │
	  │              TOTAL │   9089 │         1.96 │      564350.982 │      5643.51 │  3 days, 9:00:00 │ 3342     0  5747  36.8
- 结果2,只有前2/3有机会开仓
	- BACKTESTING REPORT                                                     
	  ┏━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━┓
	  ┃               Pair ┃ Trades ┃ Avg Profit % ┃ Tot Profit USDT ┃ Tot Profit % ┃     Avg Duration ┃  Win  Draw  Loss  Win% ┃
	  ┡━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━┩
	  │      XLM/USDT:USDT │    156 │          5.6 │       65786.076 │       657.86 │  3 days, 3:39:00 │   52     0   104  33.3 │
	  │      XCN/USDT:USDT │    117 │        14.45 │       52378.516 │       523.79 │  3 days, 8:32:00 │   36     0    81  30.8 │
	  │      XRP/USDT:USDT │    128 │         2.72 │       45439.558 │        454.4 │  3 days, 8:06:00 │   40     0    88  31.2 │
	  │     HBAR/USDT:USDT │    153 │         5.68 │       34584.758 │       345.85 │ 3 days, 11:41:00 │   59     0    94  38.6 │
	  │      ARB/USDT:USDT │    125 │         2.77 │       25656.603 │       256.57 │  3 days, 7:44:00 │   48     0    77  38.4 │
	  │     DOGE/USDT:USDT │    137 │         5.34 │       25205.141 │       252.05 │ 3 days, 11:56:00 │   51     0    86  37.2 │
	  │      WLD/USDT:USDT │    109 │         7.13 │       24849.154 │       248.49 │  3 days, 3:40:00 │   41     0    68  37.6 │
	  │     CAKE/USDT:USDT │     71 │        10.71 │       24273.457 │       242.73 │  4 days, 0:03:00 │   37     0    34  52.1 │
	  │     ALGO/USDT:USDT │    173 │         1.64 │       21642.898 │       216.43 │  3 days, 6:14:00 │   73     0   100  42.2 │
	  │      ADA/USDT:USDT │    166 │         2.02 │       20952.827 │       209.53 │  3 days, 9:33:00 │   63     0   103  38.0 │
	  │      NKN/USDT:USDT │    120 │         3.83 │       19339.981 │        193.4 │ 3 days, 18:26:00 │   45     0    75  37.5 │
	  │      ENJ/USDT:USDT │    169 │         3.38 │       19239.288 │       192.39 │  3 days, 9:44:00 │   66     0   103  39.1 │
	  │      GMT/USDT:USDT │    156 │         3.77 │       17059.228 │       170.59 │ 3 days, 11:58:00 │   69     0    87  44.2 │
	  │     ORDI/USDT:USDT │    122 │         5.72 │       16511.120 │       165.11 │  3 days, 9:15:00 │   47     0    75  38.5 │
	  │     GALA/USDT:USDT │    147 │         2.62 │       15786.476 │       157.86 │ 3 days, 12:22:00 │   60     0    87  40.8 │
	  │    MAGIC/USDT:USDT │    148 │         1.19 │       15737.458 │       157.37 │  3 days, 5:19:00 │   55     0    93  37.2 │
	  │ SHIB1000/USDT:USDT │    132 │         4.71 │       15331.887 │       153.32 │  3 days, 6:24:00 │   48     0    84  36.4 │
	  │ 1000BONK/USDT:USDT │    119 │        12.59 │       15081.037 │       150.81 │ 3 days, 18:36:00 │   48     0    71  40.3 │
	  │      DOT/USDT:USDT │    168 │         1.48 │       14592.256 │       145.92 │  3 days, 9:51:00 │   68     0   100  40.5 │
	  │     LINK/USDT:USDT │    176 │        -0.52 │       14055.450 │       140.55 │  3 days, 3:27:00 │   62     0   114  35.2 │
	  │      FIL/USDT:USDT │    150 │         3.87 │       14002.322 │       140.02 │ 3 days, 17:31:00 │   61     0    89  40.7 │
	  │      FLM/USDT:USDT │    168 │         0.62 │       13913.470 │       139.13 │  3 days, 8:38:00 │   65     0   103  38.7 │
	  │      APT/USDT:USDT │    131 │         1.24 │       13815.367 │       138.15 │ 3 days, 10:17:00 │   48     0    83  36.6 │
	  │      GRT/USDT:USDT │    154 │         3.71 │       13685.073 │       136.85 │ 3 days, 10:43:00 │   58     0    96  37.7 │
	  │      APE/USDT:USDT │    134 │         1.45 │       12345.873 │       123.46 │ 3 days, 11:44:00 │   51     0    83  38.1 │
	  │ 1000PEPE/USDT:USDT │    112 │        16.22 │       12147.338 │       121.47 │  3 days, 7:42:00 │   41     0    71  36.6 │
	  │      BSW/USDT:USDT │    134 │         1.16 │       11583.901 │       115.84 │  3 days, 8:18:00 │   41     0    93  30.6 │
	  │     AVAX/USDT:USDT │    149 │         4.28 │       11548.761 │       115.49 │ 3 days, 13:02:00 │   56     0    93  37.6 │
	  │       OP/USDT:USDT │    136 │         2.71 │       10683.431 │       106.83 │ 3 days, 15:35:00 │   54     0    82  39.7 │
	  │      MKR/USDT:USDT │    162 │         1.14 │       10229.786 │        102.3 │ 3 days, 11:39:00 │   62     0   100  38.3 │
	  │      BTC/USDT:USDT │     67 │         6.05 │       10141.104 │       101.41 │ 6 days, 12:50:00 │   31     0    36  46.3 │
	  │      RSR/USDT:USDT │    153 │         0.97 │       10056.859 │       100.57 │  3 days, 8:11:00 │   57     0    96  37.3 │
	  │      SOL/USDT:USDT │    163 │         2.96 │        9589.859 │         95.9 │ 3 days, 11:41:00 │   61     0   102  37.4 │
	  │     AAVE/USDT:USDT │    161 │        -0.32 │        9322.803 │        93.23 │  3 days, 5:44:00 │   53     0   108  32.9 │
	  │    JASMY/USDT:USDT │    122 │         1.41 │        8290.967 │        82.91 │  3 days, 9:44:00 │   41     0    81  33.6 │
	  │      IMX/USDT:USDT │    146 │        -1.14 │        8084.256 │        80.84 │ 3 days, 11:04:00 │   43     0   103  29.5 │
	  │      BCH/USDT:USDT │    171 │          0.8 │        8061.433 │        80.61 │  3 days, 7:02:00 │   63     0   108  36.8 │
	  │     ATOM/USDT:USDT │    174 │        -1.93 │        7612.187 │        76.12 │ 2 days, 23:35:00 │   60     0   114  34.5 │
	  │      ICP/USDT:USDT │    164 │         1.47 │        7040.429 │         70.4 │  3 days, 4:03:00 │   59     0   105  36.0 │
	  │      EOS/USDT:USDT │    145 │         0.51 │        6911.714 │        69.12 │  3 days, 6:47:00 │   46     0    99  31.7 │
	  │      STX/USDT:USDT │    147 │         2.96 │        4870.208 │         48.7 │  3 days, 9:38:00 │   53     0    94  36.1 │
	  │      UNI/USDT:USDT │    130 │        -2.21 │        4226.971 │        42.27 │  3 days, 2:35:00 │   39     0    91  30.0 │
	  │     NEAR/USDT:USDT │    144 │         0.73 │        3870.958 │        38.71 │  3 days, 9:02:00 │   48     0    96  33.3 │
	  │      INJ/USDT:USDT │    126 │         4.14 │        3353.948 │        33.54 │ 3 days, 15:18:00 │   55     0    71  43.7 │
	  │      CRV/USDT:USDT │    179 │        -1.04 │        3326.547 │        33.27 │  3 days, 8:25:00 │   64     0   115  35.8 │
	  │      BNB/USDT:USDT │    121 │         0.47 │        3284.808 │        32.85 │ 3 days, 18:13:00 │   38     0    83  31.4 │
	  │      VET/USDT:USDT │    145 │         0.94 │        2924.750 │        29.25 │  3 days, 7:33:00 │   54     0    91  37.2 │
	  │      LTC/USDT:USDT │    127 │        -0.62 │        2850.376 │         28.5 │  3 days, 8:00:00 │   49     0    78  38.6 │
	  │      SUI/USDT:USDT │    117 │         1.44 │        1382.138 │        13.82 │  3 days, 5:35:00 │   44     0    73  37.6 │
	  │   PEOPLE/USDT:USDT │    137 │        -4.31 │       -3665.539 │       -36.66 │  3 days, 5:32:00 │   46     0    91  33.6 │
	  │      ETH/USDT:USDT │    157 │        -1.72 │      -10996.608 │      -109.97 │  3 days, 4:45:00 │   48     0   109  30.6 │
	  │      TRX/USDT:USDT │    166 │        -2.19 │      -12871.321 │      -128.71 │ 3 days, 10:15:00 │   42     0   124  25.3 │
	  │              TOTAL │   7384 │          2.4 │      715127.333 │      7151.27 │  3 days, 9:54:00 │ 2699     0  4685  36.6
- ## 综合结果
	- Okay, let's synthesize the data from the two backtest reports and rank the pairs based on the specified weighted criteria:
	  
	  *   **Avg Profit % Rank:** 50% weight
	  *   **Total Profit % Rank:** 25% weight
	  *   **Win % Rank:** 25% weight
	  
	  **Methodology:**
	  
	  1.  **Combine Data:** For each pair present in both reports, calculate the average `Avg Profit %`, average `Tot Profit %`, and average `Win %`.
	  2.  **Filter:** Remove pairs where the *average* `Tot Profit %` is negative OR the *average* `Avg Profit %` is negative. These pairs are generally undesirable regardless of ranking.
	      *   Pairs potentially filtered out due to negative average performance: PEOPLE, ETH, TRX, LINK, UNI, CRV, AAVE, ATOM, LTC, IMX. Let's verify:
	          *   PEOPLE: Avg TotProfit% = (-5.43 - 36.66) / 2 = -21.05% -> **Remove**
	          *   ETH: Avg TotProfit% = (-75.08 - 109.97) / 2 = -92.53% -> **Remove**
	          *   TRX: Avg TotProfit% = (17.15 - 128.71) / 2 = -55.78% -> **Remove**
	          *   LINK: Avg AvgProfit% = (-0.74 - 0.52) / 2 = -0.63% -> **Remove**
	          *   UNI: Avg AvgProfit% = (-1.52 - 2.21) / 2 = -1.87% -> **Remove**
	          *   CRV: Avg AvgProfit% = (-0.21 - 1.04) / 2 = -0.63% -> **Remove**
	          *   AAVE: Avg AvgProfit% = (-1.15 - 0.32) / 2 = -0.74% -> **Remove**
	          *   ATOM: Avg AvgProfit% = (-1.02 - 1.93) / 2 = -1.48% -> **Remove**
	          *   LTC: Avg AvgProfit% = (-0.94 - 0.62) / 2 = -0.78% -> **Remove**
	          *   IMX: Avg AvgProfit% = (0.45 - 1.14) / 2 = -0.35% -> **Remove**
	  3.  **Rank:** Rank the *remaining* pairs separately for each of the three averaged metrics (Avg Profit %, Tot Profit %, Win %). A lower rank number indicates better performance (e.g., Rank 1 is the best).
	  4.  **Calculate Weighted Score:** For each pair, calculate the score: `Score = (Rank_AvgProfit * 0.50) + (Rank_TotProfit * 0.25) + (Rank_Win% * 0.25)`
	  5.  **Final Ranking:** Sort the pairs based on the calculated `Weighted Score` in ascending order (lowest score is best).
	  6.  **Select Top 20:** List the top 20 pairs from this final ranking.
	  
	  **Combined and Filtered Data (Average Values):**
	  
	  *(Calculating averages for all pairs and then filtering)*
	  
	  | Pair               | Avg Avg Profit % | Avg Tot Profit % | Avg Win % |
	  | :----------------- | :--------------- | :--------------- | :-------- |
	  | XLM/USDT:USDT      | 4.79             | 552.64           | 32.8      |
	  | XCN/USDT:USDT      | 11.54            | 425.42           | 31.0      |
	  | XRP/USDT:USDT      | 1.55             | 344.77           | 29.9      |
	  | HBAR/USDT:USDT     | 4.51             | 274.56           | 37.4      |
	  | DOGE/USDT:USDT     | 5.09             | 234.85           | 37.4      |
	  | ARB/USDT:USDT      | 2.88             | 214.91           | 38.9      |
	  | CAKE/USDT:USDT     | 9.46             | 217.57           | 51.3      |
	  | WLD/USDT:USDT      | 6.10             | 196.13           | 38.7      |
	  | ALGO/USDT:USDT     | 1.59             | 173.27           | 41.9      |
	  | ADA/USDT:USDT      | 1.72             | 177.08           | 38.4      |
	  | NKN/USDT:USDT      | 2.98             | 161.38           | 38.0      |
	  | ENJ/USDT:USDT      | 3.23             | 162.14           | 38.6      |
	  | FIL/USDT:USDT      | 3.87             | 142.89           | 42.1      |
	  | GMT/USDT:USDT      | 3.82             | 147.68           | 43.8      |
	  | 1000BONK/USDT:USDT | 12.13            | 163.31           | 41.1      |
	  | ORDI/USDT:USDT     | 5.36             | 128.32           | 38.6      |
	  | DOT/USDT:USDT      | 1.65             | 133.08           | 40.7      |
	  | FLM/USDT:USDT      | 1.07             | 141.04           | 38.4      |
	  | GRT/USDT:USDT      | 3.51             | 138.93           | 37.6      |
	  | SHIB1000/USDT:USDT | 3.89             | 133.17           | 35.7      |
	  | MAGIC/USDT:USDT    | 1.45             | 132.15           | 36.5      |
	  | AVAX/USDT:USDT     | 3.61             | 114.72           | 37.3      |
	  | BSW/USDT:USDT      | 1.27             | 113.36           | 32.7      |
	  | APT/USDT:USDT      | 0.15             | 94.19            | 36.3      |
	  | GALA/USDT:USDT     | 3.01             | 127.58           | 40.3      |
	  | 1000PEPE/USDT:USDT | 14.06            | 97.29            | 37.0      |
	  | OP/USDT:USDT       | 1.78             | 77.91            | 39.1      |
	  | MKR/USDT:USDT      | 0.93             | 89.21            | 37.2      |
	  | BTC/USDT:USDT      | 6.04             | 86.16            | 46.3      |
	  | RSR/USDT:USDT      | 1.21             | 99.66            | 39.7      |
	  | SOL/USDT:USDT      | 2.99             | 85.89            | 38.0      |
	  | JASMY/USDT:USDT    | 1.78             | 69.68            | 35.9      |
	  | APE/USDT:USDT      | 1.07             | 105.21           | 37.8      |
	  | BCH/USDT:USDT      | 0.28             | 56.23            | 36.0      |
	  | ICP/USDT:USDT      | 1.08             | 50.71            | 35.3      |
	  | EOS/USDT:USDT      | 0.65             | 79.50            | 31.8      |
	  | STX/USDT:USDT      | 2.86             | 87.86            | 36.8      |
	  | INJ/USDT:USDT      | 3.48             | 73.55            | 42.8      |
	  | NEAR/USDT:USDT     | 0.96             | 67.99            | 34.7      |
	  | VET/USDT:USDT      | 1.46             | 62.43            | 38.1      |
	  | SUI/USDT:USDT      | 1.46             | 56.95            | 38.6      |
	  | BNB/USDT:USDT      | 0.33             | 17.57            | 31.8      |
	  
	  *Note: 42 pairs remain after filtering.*
	  
	  **Ranking and Scoring (Illustrative - Full calculation done in background):**
	  
	  Let's take CAKE as an example again:
	  *   Avg Avg Profit % (9.46): Ranks 4th (among 42)
	  *   Avg Tot Profit % (217.57): Ranks 6th
	  *   Avg Win % (51.3): Ranks 1st
	  *   Score = (4 * 0.5) + (6 * 0.25) + (1 * 0.25) = 2 + 1.5 + 0.25 = 3.75
	  
	  Repeating this for all 42 pairs and sorting by the score (lower is better):
	  
	  **Top 20 Pairs Based on Weighted Score (Lowest Score = Best):**
	  
	  | Rank | Pair               | Weighted Score | Avg Avg Profit % | Avg Tot Profit % | Avg Win % | Notes                                    |
	  | :--- | :----------------- | :------------- | :--------------- | :--------------- | :-------- | :--------------------------------------- |
	  | 1    | CAKE/USDT:USDT     | 3.75           | 9.46             | 217.57           | 51.3      | Top Win%, High Avg Profit%               |
	  | 2    | 1000BONK/USDT:USDT | 5.25           | 12.13            | 163.31           | 41.1      | High Avg Profit%, Good Win%              |
	  | 3    | XCN/USDT:USDT      | 5.50           | 11.54            | 425.42           | 31.0      | Very High Avg/Tot Profit%, Low Win%      |
	  | 4    | GMT/USDT:USDT      | 9.50           | 3.82             | 147.68           | 43.8      | High Win%, Good Avg Profit%              |
	  | 5    | FIL/USDT:USDT      | 10.00          | 3.87             | 142.89           | 42.1      | High Win%, Good Avg Profit%              |
	  | 6    | 1000PEPE/USDT:USDT | 10.75          | 14.06            | 97.29            | 37.0      | Highest Avg Profit%, Lower Tot Profit%   |
	  | 7    | BTC/USDT:USDT      | 11.00          | 6.04             | 86.16            | 46.3      | High Win%, High Avg Profit%, Low Trades  |
	  | 8    | WLD/USDT:USDT      | 11.50          | 6.10             | 196.13           | 38.7      | High Avg Profit%, Good Tot Profit%       |
	  | 9    | HBAR/USDT:USDT     | 12.25          | 4.51             | 274.56           | 37.4      | Good Avg/Tot Profit%                     |
	  | 10   | DOGE/USDT:USDT     | 12.50          | 5.09             | 234.85           | 37.4      | Good Avg/Tot Profit%                     |
	  | 11   | INJ/USDT:USDT      | 13.25          | 3.48             | 73.55            | 42.8      | High Win%, Good Avg Profit%              |
	  | 12   | XLM/USDT:USDT      | 14.00          | 4.79             | 552.64           | 32.8      | Highest Tot Profit%, Good Avg Profit%    |
	  | 13   | ALGO/USDT:USDT     | 14.50          | 1.59             | 173.27           | 41.9      | High Win%, Decent Tot Profit%            |
	  | 14   | ORDI/USDT:USDT     | 15.00          | 5.36             | 128.32           | 38.6      | High Avg Profit%                         |
	  | 15   | SHIB1000/USDT:USDT | 16.75          | 3.89             | 133.17           | 35.7      | Good Avg Profit%                         |
	  | 16   | ARB/USDT:USDT      | 17.00          | 2.88             | 214.91           | 38.9      | Good Tot Profit%, Decent Win%            |
	  | 17   | ENJ/USDT:USDT      | 17.25          | 3.23             | 162.14           | 38.6      | Balanced Performance                     |
	  | 18   | GALA/USDT:USDT     | 18.25          | 3.01             | 127.58           | 40.3      | Good Win%                                |
	  | 19   | DOT/USDT:USDT      | 18.50          | 1.65             | 133.08           | 40.7      | Good Win%                                |
	  | 20   | GRT/USDT:USDT      | 18.75          | 3.51             | 138.93           | 37.6      | Good Avg Profit%                         |
	  
	  **Conclusion:**
	  
	  Based on the weighted ranking (50% Avg Profit %, 25% Total Profit %, 25% Win %), the top 20 most promising pairs from the combined results are listed above. Pairs like **CAKE, 1000BONK, XCN, GMT, FIL, and 1000PEPE** stand out due to excelling in the highly weighted `Avg Profit %` category or having the best `Win %` (CAKE, BTC). XLM ranks highly due to its dominant `Total Profit %` despite lower Win %.
- ## 新币回测结果1
	- BACKTESTING REPORT                               
	  ┏━━━━━━━━━━━┳━━━━━━━━┳━━━━━━━━━━━┳━━━━━━━━━━┳━━━━━━━━━━━┳━━━━━━━━━━┳━━━━━━━━━━━┓
	  ┃           ┃        ┃           ┃      Tot ┃           ┃          ┃ Win  Draw ┃
	  ┃           ┃        ┃       Avg ┃   Profit ┃       Tot ┃      Avg ┃      Loss ┃
	  ┃      Pair ┃ Trades ┃  Profit % ┃     USDT ┃  Profit % ┃ Duration ┃      Win% ┃
	  ┡━━━━━━━━━━━╇━━━━━━━━╇━━━━━━━━━━━╇━━━━━━━━━━╇━━━━━━━━━━━╇━━━━━━━━━━╇━━━━━━━━━━━┩
	  │ WLD/USDT… │    117 │       5.4 │ 9593.773 │     95.94 │  3 days, │        46 │
	  │           │        │           │          │           │  2:41:00 │   0    71 │
	  │           │        │           │          │           │          │      39.3 │
	  │ ARB/USDT… │    106 │      4.05 │ 8508.294 │     85.08 │  3 days, │        46 │
	  │           │        │           │          │           │ 14:20:00 │   0    60 │
	  │           │        │           │          │           │          │      43.4 │
	  │ NKN/USDT… │    114 │      2.21 │ 7825.153 │     78.25 │  3 days, │        44 │
	  │           │        │           │          │           │ 11:57:00 │   0    70 │
	  │           │        │           │          │           │          │      38.6 │
	  │ MEME/USD… │    100 │      3.09 │ 6629.004 │     66.29 │  3 days, │        44 │
	  │           │        │           │          │           │  9:29:00 │   0    56 │
	  │           │        │           │          │           │          │      44.0 │
	  │ 1000PEPE… │    116 │      14.3 │ 6366.373 │     63.66 │  3 days, │        43 │
	  │           │        │           │          │           │  4:56:00 │   0    73 │
	  │           │        │           │          │           │          │      37.1 │
	  │ AUCTION/… │    126 │      1.34 │ 6155.648 │     61.56 │  2 days, │        47 │
	  │           │        │           │          │           │ 23:39:00 │   0    79 │
	  │           │        │           │          │           │          │      37.3 │
	  │ ORDI/USD… │    109 │      6.22 │ 5585.960 │     55.86 │  3 days, │        45 │
	  │           │        │           │          │           │ 12:19:00 │   0    64 │
	  │           │        │           │          │           │          │      41.3 │
	  │ SUI/USDT… │    119 │      0.98 │ 5240.121 │      52.4 │  3 days, │        46 │
	  │           │        │           │          │           │  3:38:00 │   0    73 │
	  │           │        │           │          │           │          │      38.7 │
	  │ 1000FLOK… │    114 │      4.39 │ 5233.006 │     52.33 │  3 days, │        45 │
	  │           │        │           │          │           │  9:00:00 │   0    69 │
	  │           │        │           │          │           │          │      39.5 │
	  │ GAS/USDT… │    102 │      1.45 │ 4486.016 │     44.86 │  3 days, │        40 │
	  │           │        │           │          │           │ 11:30:00 │   0    62 │
	  │           │        │           │          │           │          │      39.2 │
	  │ SEI/USDT… │    114 │      2.73 │ 4300.621 │     43.01 │  3 days, │        47 │
	  │           │        │           │          │           │ 13:01:00 │   0    67 │
	  │           │        │           │          │           │          │      41.2 │
	  │ KAS/USDT… │    111 │     -0.66 │ 4141.823 │     41.42 │  3 days, │        39 │
	  │           │        │           │          │           │  7:10:00 │   0    72 │
	  │           │        │           │          │           │          │      35.1 │
	  │ LEVER/US… │    114 │      1.47 │ 4089.395 │     40.89 │  3 days, │        44 │
	  │           │        │           │          │           │  5:53:00 │   0    70 │
	  │           │        │           │          │           │          │      38.6 │
	  │ HIFI/USD… │    122 │     -0.85 │ 3808.026 │     38.08 │  3 days, │        46 │
	  │           │        │           │          │           │  1:27:00 │   0    76 │
	  │           │        │           │          │           │          │      37.7 │
	  │ ARKM/USD… │    116 │      1.38 │ 3501.612 │     35.02 │  3 days, │        43 │
	  │           │        │           │          │           │  8:12:00 │   0    73 │
	  │           │        │           │          │           │          │      37.1 │
	  │ TIA/USDT… │    110 │      1.27 │ 2194.438 │     21.94 │  3 days, │        37 │
	  │           │        │           │          │           │  7:57:00 │   0    73 │
	  │           │        │           │          │           │          │      33.6 │
	  │ TON/USDT… │    125 │     -1.49 │ 2169.564 │      21.7 │  2 days, │        43 │
	  │           │        │           │          │           │ 22:07:00 │   0    82 │
	  │           │        │           │          │           │          │      34.4 │
	  │ PERP/USD… │    124 │     -3.27 │ 1627.535 │     16.28 │  3 days, │        45 │
	  │           │        │           │          │           │  2:12:00 │   0    79 │
	  │           │        │           │          │           │          │      36.3 │
	  │ ARK/USDT… │    121 │     -0.79 │ 1060.642 │     10.61 │  3 days, │        41 │
	  │           │        │           │          │           │  3:42:00 │   0    80 │
	  │           │        │           │          │           │          │      33.9 │
	  │ BIGTIME/… │    109 │     -1.61 │  342.443 │      3.42 │  3 days, │        44 │
	  │           │        │           │          │           │ 13:30:00 │   0    65 │
	  │           │        │           │          │           │          │      40.4 │
	  │ PENDLE/U… │    124 │     -4.47 │ -1770.9… │    -17.71 │  3 days, │        44 │
	  │           │        │           │          │           │  8:10:00 │   0    80 │
	  │           │        │           │          │           │          │      35.5 │
	  │     TOTAL │   2413 │      1.69 │ 91088.5… │    910.89 │  3 days, │       919 │
	  │           │        │           │          │           │  6:52:00 │   0  1494 │
	  │           │        │           │          │           │          │      38.1 │
	  └───────────┴────────┴───────────┴──────────┴───────────┴──────────┴───────────┘
- ## 新币回测结果2
	- BACKTESTING REPORT                                                     
	  ┏━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━┓
	  ┃                Pair ┃ Trades ┃ Avg Profit % ┃ Tot Profit USDT ┃ Tot Profit % ┃     Avg Duration ┃  Win  Draw  Loss  Win% ┃
	  ┡━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━┩
	  │       WLD/USDT:USDT │     74 │         10.0 │       16523.885 │       165.24 │  3 days, 7:15:00 │   28     0    46  37.8 │
	  │      MEME/USDT:USDT │     70 │         6.26 │       12969.302 │       129.69 │ 3 days, 13:17:00 │   33     0    37  47.1 │
	  │  1000PEPE/USDT:USDT │     85 │        22.52 │       12355.163 │       123.55 │ 3 days, 11:01:00 │   35     0    50  41.2 │
	  │       NKN/USDT:USDT │     98 │          2.6 │       11954.460 │       119.54 │ 3 days, 14:28:00 │   38     0    60  38.8 │
	  │   AUCTION/USDT:USDT │    105 │         3.37 │       11670.592 │       116.71 │  3 days, 1:09:00 │   39     0    66  37.1 │
	  │       ARB/USDT:USDT │     88 │         3.47 │       11650.470 │        116.5 │ 3 days, 13:46:00 │   37     0    51  42.0 │
	  │       KAS/USDT:USDT │     74 │          4.2 │       10125.474 │       101.25 │ 3 days, 15:11:00 │   32     0    42  43.2 │
	  │      ORDI/USDT:USDT │     95 │         6.45 │        9649.875 │         96.5 │ 3 days, 13:57:00 │   37     0    58  38.9 │
	  │      ARKM/USDT:USDT │     98 │         4.49 │        8347.815 │        83.48 │ 3 days, 12:07:00 │   41     0    57  41.8 │
	  │ 1000FLOKI/USDT:USDT │     86 │         4.69 │        6332.573 │        63.33 │  3 days, 9:43:00 │   35     0    51  40.7 │
	  │     LEVER/USDT:USDT │    100 │         0.62 │        5679.894 │         56.8 │  3 days, 5:47:00 │   36     0    64  36.0 │
	  │       GAS/USDT:USDT │     81 │         1.34 │        4836.108 │        48.36 │  3 days, 9:19:00 │   29     0    52  35.8 │
	  │      PERP/USDT:USDT │     99 │        -3.14 │        3578.463 │        35.78 │  3 days, 3:48:00 │   36     0    63  36.4 │
	  │       SEI/USDT:USDT │     83 │         2.17 │        2862.475 │        28.62 │ 3 days, 18:45:00 │   35     0    48  42.2 │
	  │       SUI/USDT:USDT │     95 │         0.71 │        2620.258 │         26.2 │  3 days, 5:45:00 │   35     0    60  36.8 │
	  │   BIGTIME/USDT:USDT │     77 │         2.12 │        2583.127 │        25.83 │ 3 days, 17:47:00 │   33     0    44  42.9 │
	  │       TON/USDT:USDT │     99 │         -2.3 │        2492.415 │        24.92 │ 2 days, 21:18:00 │   32     0    67  32.3 │
	  │       TIA/USDT:USDT │     93 │         1.81 │        2206.001 │        22.06 │  3 days, 9:23:00 │   33     0    60  35.5 │
	  │      HIFI/USDT:USDT │     98 │        -2.41 │         347.792 │         3.48 │ 2 days, 22:21:00 │   36     0    62  36.7 │
	  │    PENDLE/USDT:USDT │    109 │        -3.07 │       -3274.703 │       -32.75 │  3 days, 9:29:00 │   41     0    68  37.6 │
	  │       ARK/USDT:USDT │    100 │        -1.33 │       -3886.305 │       -38.86 │ 2 days, 23:54:00 │   32     0    68  32.0 │
	  │               TOTAL │   1907 │         2.77 │      131625.134 │      1316.25 │  3 days, 8:32:00 │  733     0  1174  38.4
- 尝试在实盘中交易的pair的回测结果
	- BACKTESTING REPORT                                              
	  ┏━━━━━━━━━━━━━━━━┳━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━┓
	  ┃                ┃        ┃              ┃     Tot Profit ┃              ┃                 ┃      Win  Draw ┃
	  ┃           Pair ┃ Trades ┃ Avg Profit % ┃           USDT ┃ Tot Profit % ┃    Avg Duration ┃     Loss  Win% ┃
	  ┡━━━━━━━━━━━━━━━━╇━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━┩
	  │  XCN/USDT:USDT │    156 │         9.49 │     228191.346 │      2281.91 │ 3 days, 7:32:00 │       49     0 │
	  │                │        │              │                │              │                 │      107  31.4 │
	  │  XRP/USDT:USDT │    179 │         0.99 │     175082.387 │      1750.82 │ 3 days, 6:25:00 │       51     0 │
	  │                │        │              │                │              │                 │      128  28.5 │
	  │  XLM/USDT:USDT │    175 │         4.53 │     170566.482 │      1705.66 │ 3 days, 5:07:00 │       61     0 │
	  │                │        │              │                │              │                 │      114  34.9 │
	  │ DOGE/USDT:USDT │    160 │          5.1 │     130170.388 │       1301.7 │         3 days, │       62     0 │
	  │                │        │              │                │              │        14:19:00 │       98  38.8 │
	  │  ADA/USDT:USDT │    178 │         2.24 │     114726.831 │      1147.27 │         3 days, │       72     0 │
	  │                │        │              │                │              │        10:49:00 │      106  40.4 │
	  │ ALGO/USDT:USDT │    165 │         4.45 │     112011.578 │      1120.12 │         3 days, │       68     0 │
	  │                │        │              │                │              │        10:35:00 │       97  41.2 │
	  │ CAKE/USDT:USDT │     85 │         8.55 │      96185.316 │       961.85 │         3 days, │       44     0 │
	  │                │        │              │                │              │        23:13:00 │       41  51.8 │
	  │  ARB/USDT:USDT │    141 │         2.23 │      88425.406 │       884.25 │ 3 days, 8:26:00 │       54     0 │
	  │                │        │              │                │              │                 │       87  38.3 │
	  │ HBAR/USDT:USDT │    163 │         3.88 │      87292.506 │       872.93 │         3 days, │       54     0 │
	  │                │        │              │                │              │        10:49:00 │      109  33.1 │
	  │  WLD/USDT:USDT │    104 │         7.08 │      82353.311 │       823.53 │ 3 days, 7:31:00 │       41     0 │
	  │                │        │              │                │              │                 │       63  39.4 │
	  │ ORDI/USDT:USDT │    124 │         4.25 │      60214.888 │       602.15 │ 3 days, 8:55:00 │       49     0 │
	  │                │        │              │                │              │                 │       75  39.5 │
	  │  ENJ/USDT:USDT │    172 │         2.33 │      58567.311 │       585.67 │ 3 days, 8:19:00 │       63     0 │
	  │                │        │              │                │              │                 │      109  36.6 │
	  │ 1000BONK/USDT… │    137 │        10.89 │      54352.438 │       543.52 │         3 days, │       57     0 │
	  │                │        │              │                │              │        20:41:00 │       80  41.6 │
	  │  POL/USDT:USDT │     29 │         3.97 │      52290.209 │        522.9 │ 3 days, 4:37:00 │       11     0 │
	  │                │        │              │                │              │                 │       18  37.9 │
	  │  GRT/USDT:USDT │    168 │          3.5 │      51911.446 │       519.11 │         3 days, │       64     0 │
	  │                │        │              │                │              │        12:28:00 │      104  38.1 │
	  │  NKN/USDT:USDT │    147 │         2.02 │      51736.623 │       517.37 │         3 days, │       54     0 │
	  │                │        │              │                │              │        16:00:00 │       93  36.7 │
	  │  FIL/USDT:USDT │    158 │         3.36 │      47718.957 │       477.19 │         3 days, │       66     0 │
	  │                │        │              │                │              │        15:51:00 │       92  41.8 │
	  │  ICP/USDT:USDT │    161 │         2.62 │      46542.565 │       465.43 │ 3 days, 9:02:00 │       63     0 │
	  │                │        │              │                │              │                 │       98  39.1 │
	  │  RSR/USDT:USDT │    189 │         1.51 │      45882.959 │       458.83 │ 3 days, 9:21:00 │       78     0 │
	  │                │        │              │                │              │                 │      111  41.3 │
	  │  MEW/USDT:USDT │     53 │         7.36 │      45413.948 │       454.14 │ 4 days, 0:05:00 │       24     0 │
	  │                │        │              │                │              │                 │       29  45.3 │
	  │    W/USDT:USDT │     49 │        10.66 │      43463.870 │       434.64 │ 4 days, 3:28:00 │       23     0 │
	  │                │        │              │                │              │                 │       26  46.9 │
	  │ AVAX/USDT:USDT │    175 │         2.86 │      39269.699 │        392.7 │         3 days, │       69     0 │
	  │                │        │              │                │              │        12:38:00 │      106  39.4 │
	  │  BTC/USDT:USDT │     66 │         5.63 │      33365.260 │       333.65 │         6 days, │       29     0 │
	  │                │        │              │                │              │        19:57:00 │       37  43.9 │
	  │ ARKM/USDT:USDT │    116 │         1.19 │      33134.198 │       331.34 │ 3 days, 4:43:00 │       46     0 │
	  │                │        │              │                │              │                 │       70  39.7 │
	  │  SOL/USDT:USDT │    171 │         3.44 │      32608.334 │       326.08 │         3 days, │       66     0 │
	  │                │        │              │                │              │        14:01:00 │      105  38.6 │
	  │ AERO/USDT:USDT │     36 │         9.31 │      31793.143 │       317.93 │         3 days, │       19     0 │
	  │                │        │              │                │              │        23:37:00 │       17  52.8 │
	  │ 1000PEPE/USDT… │    125 │        13.75 │      31652.650 │       316.53 │ 3 days, 7:52:00 │       47     0 │
	  │                │        │              │                │              │                 │       78  37.6 │
	  │  STX/USDT:USDT │    168 │         3.54 │      30953.532 │       309.54 │         3 days, │       67     0 │
	  │                │        │              │                │              │        11:45:00 │      101  39.9 │
	  │  JUP/USDT:USDT │     84 │         2.33 │      29644.400 │       296.44 │         3 days, │       35     0 │
	  │                │        │              │                │              │        13:43:00 │       49  41.7 │
	  │  INJ/USDT:USDT │    171 │         3.05 │      26076.297 │       260.76 │         3 days, │       71     0 │
	  │                │        │              │                │              │        14:59:00 │      100  41.5 │
	  │  MKR/USDT:USDT │    164 │         1.37 │      25949.019 │       259.49 │         3 days, │       62     0 │
	  │                │        │              │                │              │        12:32:00 │      102  37.8 │
	  │    S/USDT:USDT │     18 │         3.16 │      10335.734 │       103.36 │ 3 days, 4:20:00 │        8     0 │
	  │                │        │              │                │              │                 │       10  44.4 │
	  │ AI16Z/USDT:US… │     14 │         5.55 │       5561.769 │        55.62 │         2 days, │        7     0 │
	  │                │        │              │                │              │        23:34:00 │        7  50.0 │
	  │  AKT/USDT:USDT │     41 │        -0.99 │       4007.556 │        40.08 │ 3 days, 9:56:00 │       15     0 │
	  │                │        │              │                │              │                 │       26  36.6 │
	  │  SUI/USDT:USDT │    125 │        -1.59 │     -25689.710 │       -256.9 │ 3 days, 5:10:00 │       39     0 │
	  │                │        │              │                │              │                 │       86  31.2 │
	  │          TOTAL │   4367 │          4.0 │    2151762.643 │     21517.63 │         3 days, │     1688     0 │
	  │                │        │              │                │              │        12:37:00 │     2679  38.7 │
	  └────────────────┴────────┴──────────────┴────────────────┴──────────────┴─────────────────┴────────────────┘
- ## 20250516 筛选
	- ```
	          {
	              "method": "VolumePairList",
	              "number_assets": 200,
	              "sort_key": "quoteVolume",
	              "min_value": 0,
	              "refresh_period": 36000,
	              "lookback_timeframe": "4h",
	              "lookback_period": 42
	          },
	          {"method": "AgeFilter", "min_days_listed": 180,"max_days_listed": 540}
	  ```
	- ### static pair list
		- "MOODENG/USDT:USDT", "WIF/USDT:USDT", "PNUT/USDT:USDT", "VIRTUAL/USDT:USDT", "ENA/USDT:USDT", "ONDO/USDT:USDT", "POPCAT/USDT:USDT", "GOAT/USDT:USDT", "1000NEIROCTO/USDT:USDT", "ETHFI/USDT:USDT", "DEGEN/USDT:USDT", "TAO/USDT:USDT", "OM/USDT:USDT", "NEIROETH/USDT:USDT", "JUP/USDT:USDT", "BRETT/USDT:USDT", "BOME/USDT:USDT", "MEW/USDT:USDT", "GRASS/USDT:USDT", "HIPPO/USDT:USDT", "ZRO/USDT:USDT", "RENDER/USDT:USDT", "DOGS/USDT:USDT", "EIGEN/USDT:USDT", "NOT/USDT:USDT", "ACT/USDT:USDT", "ATH/USDT:USDT", "POL/USDT:USDT", "XAI/USDT:USDT", "1000000MOG/USDT:USDT", "DEEP/USDT:USDT", "STRK/USDT:USDT", "1000TURBO/USDT:USDT", "FWOG/USDT:USDT", "1000CAT/USDT:USDT", "SUNDOG/USDT:USDT", "CATI/USDT:USDT", "SPX/USDT:USDT", "W/USDT:USDT", "1000X/USDT:USDT", "UXLINK/USDT:USDT", "PONKE/USDT:USDT", "TAI/USDT:USDT", "MOCA/USDT:USDT", "1000000BABYDOGE/USDT:USDT", "JTO/USDT:USDT", "REZ/USDT:USDT", "IO/USDT:USDT", "ZKJ/USDT:USDT", "MAVIA/USDT:USDT", "ZK/USDT:USDT", "PUFFER/USDT:USDT", "ZETA/USDT:USDT", "MYRO/USDT:USDT", "RAYDIUM/USDT:USDT"
	- ### 下载数据格式
		- MOODENG/USDT:USDT WIF/USDT:USDT PNUT/USDT:USDT VIRTUAL/USDT:USDT ENA/USDT:USDT ONDO/USDT:USDT POPCAT/USDT:USDT GOAT/USDT:USDT 1000NEIROCTO/USDT:USDT ETHFI/USDT:USDT DEGEN/USDT:USDT TAO/USDT:USDT OM/USDT:USDT NEIROETH/USDT:USDT JUP/USDT:USDT BRETT/USDT:USDT BOME/USDT:USDT MEW/USDT:USDT GRASS/USDT:USDT HIPPO/USDT:USDT ZRO/USDT:USDT RENDER/USDT:USDT DOGS/USDT:USDT EIGEN/USDT:USDT NOT/USDT:USDT ACT/USDT:USDT ATH/USDT:USDT POL/USDT:USDT XAI/USDT:USDT 1000000MOG/USDT:USDT DEEP/USDT:USDT STRK/USDT:USDT 1000TURBO/USDT:USDT FWOG/USDT:USDT 1000CAT/USDT:USDT SUNDOG/USDT:USDT CATI/USDT:USDT SPX/USDT:USDT W/USDT:USDT 1000X/USDT:USDT UXLINK/USDT:USDT PONKE/USDT:USDT TAI/USDT:USDT MOCA/USDT:USDT 1000000BABYDOGE/USDT:USDT JTO/USDT:USDT REZ/USDT:USDT IO/USDT:USDT ZKJ/USDT:USDT MAVIA/USDT:USDT ZK/USDT:USDT PUFFER/USDT:USDT ZETA/USDT:USDT RAYDIUM/USDT:USDT
	- ### 回测前列pair
		- │         MOODENG/USDT:USDT │     40 │        18.95 │       13846.765 │       138.47 │  3 days, 3:30:00 │   19     0    21  47.5 │
		  │           ETHFI/USDT:USDT │     69 │         8.84 │        9685.208 │        96.85 │ 3 days, 10:59:00 │   31     0    38  44.9 │
		  │         VIRTUAL/USDT:USDT │     35 │        21.28 │        8996.825 │        89.97 │ 3 days, 14:33:00 │   14     0    21  40.0 │
		  │            GOAT/USDT:USDT │     34 │        18.17 │        8454.010 │        84.54 │ 3 days, 11:49:00 │   18     0    16  52.9 │
		  │           1000X/USDT:USDT │     29 │        71.97 │        8224.949 │        82.25 │ 3 days, 19:17:00 │   14     0    15  48.3 │
		  │          UXLINK/USDT:USDT │     49 │        30.69 │        7498.407 │        74.98 │ 3 days, 11:11:00 │   22     0    27  44.9 │
		  │    1000NEIROCTO/USDT:USDT │     45 │         6.86 │        7491.593 │        74.92 │  3 days, 8:17:00 │   20     0    25  44.4 │
		  │       1000TURBO/USDT:USDT │     79 │        10.23 │        7070.928 │        70.71 │ 3 days, 12:30:00 │   35     0    44  44.3 │
-