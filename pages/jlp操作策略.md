- #crypto #jlp #套利策略
- [[Jupiter]] 是 [[Solana]] 区块链上领先的去中心化交易所（DEX）聚合器，旨在通过整合多个流动性来源为用户提供最优交易价格，并扩展了一系列 DeFi 工具。
- 1. 核心功能：DEX 聚合与流动性优化
	- 多源流动性聚合：Jupiter 连接 Solana 生态中的多个 DEX（如 Raydium、Orca、Serum 等），通过智能路由算法比较不同平台的报价，动态拆分交易以降低滑点，确保用户获得最优价格。
	- 低交易成本：得益于 Solana 的高吞吐量和低 Gas 费（每笔交易成本不足 0.01 美元），Jupiter 能够高效执行跨多个池的交易，而无需承担类似以太坊的高成本问题。
- 2. 产品扩展：全栈 DeFi 工具套件
	- Jupiter 已从单纯的聚合器发展为涵盖多种金融工具的生态系统：
	- 限价订单：允许用户设定特定价格执行交易，避免市场波动风险915。
	- DCA（定期定额投资）：支持按分钟、小时、天等周期自动分批买入资产，适合长期投资者15。
	- 永续合约交易：提供高达 100 倍的杠杆交易，用户可通过 JLP 流动性池（包含 SOL、ETH、BTC 等资产）参与做多或做空。Launchpad：为 Solana 生态项目提供代币发行平台（如 LFG Launchpad），推动新项目发展911。
- JLP（Jupiter Liquidity Provider）与Jupiter（JUP）是Solana生态中紧密关联但功能不同的核心组成部分，前者是永续合约流动性池的代币化资产，后者是Jupiter生态的治理代币。
- JLP：永续合约的流动性基石
  JLP是Jupiter Perps（永续合约交易所）的流动性池代币，其价值由多种资产（SOL、ETH、WBTC、USDC、USDT）及交易者盈亏共同决定。用户通过存入资产获得JLP，成为交易者的对手盘，赚取75%的交易手续费和借贷费用
- JUP是Jupiter DAO的治理代币，用于投票决定协议发展方向（如流动性策略、费用分配等）。JUP的初始空投（总供应量10%）旨在激励社区参与，推动Solana生态的采用
- JLP产生的交易费用中，75%直接复利至池中提升JLP价值，剩余25%作为协议费用支持Jupiter开发（可能间接与JUP持有者利益挂钩）
	- JLP的APY驱动：当前JLP的APY约30%-106%，主要依赖交易量和交易者亏损，而JUP的价值则与生态整体增长（如TVL、用户基数）相关
	- JLP的TVL上限（7亿美元）由Jupiter团队动态调整，需通过JUP治理投票决定扩容或收缩策略。例如，当JLP达到容量限制时，新增铸造暂停，市场溢价可能提升JLP价格
	- JLP的价值由44% SOL、10% ETH、11% WBTC、26% USDC、9% USDT组成，稳定币占比35%，降低波动性.
- JLP波动性分析：
	- 2025-02-17，SOL权重47%，利用率35%，ETH权重10%，14.5%，BTC权重11%，33.5%，USDC权重28%，USDT权重4%，
	- 暴露的权重：
		- SOL：0.47*0.65=0.305
		- ETH：0.1*0.855=0.0855
		- BTC：0.11*0.665=0.073
		- 总共暴露：46.35%，其中SOL权重为66%，ETH权重18.5%，BTC权重15.5%
		- 总的来说2倍杠杆是正常SOL的波动率
-
-
-