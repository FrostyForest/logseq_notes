- #ai #transformers #rlhf #预训练 #微调
- [[ChatGPT]]之类的大模型的训练主要分为两个阶段
	- 预训练阶段：
	  logseq.order-list-type:: number
	  数据来源：大规模的文本数据库
	  训练方法：[[自监督学习]]，具体而言就是预测下一个单词、填空等，loss为 [[cross entropy]] 。 这种方式不需要人工标注数据，可以高效地利用大量文本数据学习语言的内在规律。
	  核心目标：让模型学会语法，语义，知识。
	- 微调阶段：
	  logseq.order-list-type:: number
	- 数据来源：
		- 对话数据集：这些数据集包含大量的对话示例，涵盖各种主题和场景。
		- 指令数据集：些数据集包含各种指令和对应答案的示例，例如“解释什么是相对论”、“写一首关于春天的诗”等
	- 训练方法：
		- [[监督微调]](Supervised Fine-Tuning, SFT)：使用人工标注的对话和指令数据对模型进行训练
		- [[RLHF]](基于人类反馈的强化学习)：通过人类对模型生成的回答进行评分，来训练一个奖励模型。 然后，使用强化学习算法，优化模型生成更高质量的回答。
		-
	-
		- logseq.order-list-type:: number
	- logseq.order-list-type:: number
	- logseq.order-list-type:: number
	- logseq.order-list-type:: number