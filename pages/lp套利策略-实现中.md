- #crpyto #套利策略
- 一方面做多通过lp的手续费获取收益，另一方面，开空单通过正费率获取收益
- 待优化：
	- 怎么计算当时市场的最佳套利品种：50%lp年化，50%资金费率，但是切换需要交手续费，嘛总的来说长期套利收益最高的应该是成交量大的品种
	- 最完美的当然是市场底部买入持有，高位开始套利，极限位置开始做空，再套利不断循环。
- fartcoin套利
	- lp：250%
	- 空单：fartcoin,0.015%费率，对应年化16%，sol，0.01%费率，对应年化11%，两倍空单年化22%
	- 总的年化：250*0.5+16*0.25+22*0.25=134%
	- 套利过程：计算lp中sol和对应coin的比例，自主决定套保比例p，总投资额取出p%到交易所，(1-p)%到链上钱包，暂定55%到链上，45%到交易所，~~到交易所按lp池比例开空单，其中sol的空单为2倍杠杆~~。给定做lp的范围，到交易所按相同头寸开做空网格，当价格到lp上界或下界时候，进行再平衡。
- 套利的风险
	- 无常损失：主要是做lp的两个币同时下跌。应对方法：空单网格
	- 价格溢出lp范围：假设lp范围是当前汇率的上下15%，汇率为1,比例为1：1,涨到上限重新调整lp范围，然后再跌回1
		- 以btc/usdt为例来计算，此时汇率为1/100，当汇率为100的时候，持有1btc和100u,总价值200u，当上涨至120u的时候，已经卖出了全部的btc,平均价格为110u,此时总资产为210u,再进行再平衡，105u买到了0.875个btc,然后汇率再跌回100,此时再进行再平衡，总资产为0.875*100+105=192.5u,相比一开始亏损为1-192.5/200=3.75%
		-
	- 如果价格没有溢出，但是两种资产都同时下跌，这时候网格能对冲头寸吗
		- 没办法，下跌到网格下限，必须要调整网格范围
	- 对于有自动再平衡的lp来说，就无需频繁调整两种资产的网格仓位，主要风险就是两种资产同时下跌
	- 等到资产价格接近网格下限再调整那就太晚了，再接近下限与中值的一半的时候就该重新调整
	-