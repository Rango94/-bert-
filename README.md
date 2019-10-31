# 后bert时代 #

**UNILM**[**《Unified Language Model Pre-training for Natural Language Understanding and Generation》**](https://arxiv.org/pdf/1905.03197.pdf)


**albert**[**《ALBERT: A LITE BERT FOR SELF-SUPERVISED LEARNING OF LANGUAGE REPRESENTATIONS》**](https://arxiv.org/pdf/1909.11942.pdf)


**xlnet**[**《XLNet: Generalized Autoregressive Pretraining for Language Understanding》**](https://arxiv.org/pdf/1906.08237.pdf)


**roberta**[**《RoBERTa: A Robustly Optimized BERT Pretraining Approach**](https://arxiv.gg363.site/pdf/1907.11692.pdf)[**》**]()


**spanbert**[**《SpanBERT: Improving Pre-training by Representing and Predicting Spans》**](https://arxiv.org/pdf/1907.10529.pdf)

<a name="SkF9P"></a>
# 改进点一览
|  |  | BERT | XLnet | roberta | spanbert | UNILM | albert |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 对mask的改进 | 全词掩盖（中文特有） |  |  |  |  |  |  |
|  | n-gram掩盖 |  |  |  | ✔ |  | ✔ |
|  | 动态掩盖 |  |  | ✔ |  |  |  |
| 训练超参数改进 | 更大的batch size |  |  | ✔ |  |  |  |
|  | 更多的训练时间 |  |  | ✔ |  |  |  |
|  | 更大的数据量 |  | ✔ | ✔ |  |  |  |
| 模型结构的改进 | 全排列语言模型 |  | ✔ |  |  |  |  |
|  | 相对位置嵌入 |  | ✔ |  |  |  |  |
|  | 层级参数共享 |  |  |  |  |  | ✔ |
|  | 因式分解词嵌入 |  |  |  |  |  | ✔ |
| 预训练任务的改进 | 去掉下一句判断任务 |  |  | ✔ | ✔ |  |  |
|  | 加入其他预训练任务 |  |  |  |  | ✔（*） | ✔（*） |
|  |  |  |  |  |  |  |  |

表1.后bert时代的bert们所做出的改进<br />
*unilm相比较bert多使用了三个预训练任务，分别是从左至右的语言模型，从右至左的语言模型，以及seq2seq模型。<br />*albert相比较bert，在nsp任务上添加一种特殊的负例，即模型输入的前一句是后一句的后文。

| XLnet | 将自编码式模型改为自回归式模型，期望填补原bert中mask符造成的训练，预测阶段的gap，主要是为生成式模型服务。 |
| --- | --- |
| roberta | 暴力的想要证明bert现有结构的优越性，对bert本身的结构并没有作出改变，仅仅是改变了训练的超参数。 |
| spanbert | 防止预测token对小范围的依赖，比如通过“杰伦”来预测“周”。 |
| UNILM | 使用了三个生成式语言模型，期望克服bert模型在生成式任务上存在的问题。 |
| albert | 通过一些列措施减少bert的参数量，并且仍然保证了相对较高的精度。 |

表2.bert们所想要解决的问题<br />

| 模型 | Score | 参数 | TNEWS | LCQMC | XNLI | INEWS | DRCD | CMRC2018 | BQ | MSRANER | THUCNEWS |
| :----:| :----: | :----: | :----: |:----: |:----: |:----: |:----: |:----: |:----: |:----: |:----: |
| <a href="https://github.com/google-research/bert">BERT-base</a>	| 86.82 | 108M | 89.78 	| 86.9 	|77.8 | 82.7 | 91.46 | 85.48 | 85.08 | 95.38 | 95.35 |
| <a href="https://github.com/ymcui/Chinese-BERT-wwm">BERT-wwm-ext</a> | 87.42  | 108M |89.81   | ***87.3***  | 78.7	| 83.46 | 92.63 | 86.68 | ***85.21*** | 95.26 | 95.57 |
| <a href="https://github.com/PaddlePaddle/ERNIE">ERNIE-base</a>	| 87.43 | 108M |89.83  	|87.2 | 78.6| ***85.14*** | 92.01 | 87.30 | 84.47 | 95.17 | 94.90 |
| <a href="https://github.com/brightmart/roberta_zh">RoBERTa-large</a> | 87.95 | 334M 	|89.91  | 87.2  | 79.9 | 84.0 | 94.25 | 88.60 | 85.20 | ***96.07*** | 94.56 | 
| <a href="https://github.com/ymcui/Chinese-PreTrained-XLNet">XLNet-mid</a>	| 84.55 | 209M | 86.26 | 85.98  |78.7 |84.0| 91.44 | 85.63 | 77.85 | - | 94.54 |
| <a href="https://github.com/brightmart/albert_zh">ALBERT-xlarge</a> | 86.81 | 59M |	88.3 |	86.76 | 74.0? |82.4 | 94.70 | 88.66 | 84.21 | 89.51 | 95.45 |
| <a href="https://github.com/brightmart/albert_zh">ALBERT-tiny</a> | 79.61 | 1.8M |	87.10 |	85.4 | 68.0 |81.4 | 80.67 | 73.95 | 80.76 | 84.77 | 93.54 |
| <a href="https://github.com/ymcui/Chinese-BERT-wwm">RoBERTa-wwm-ext</a>  | 87.25 | 108M  |	89.79 |	86.33 | 79.28 | 82.28 | 93.53 | 87.28 | 84.02 | 95.06 | 95.52 |
| <a href="https://github.com/ymcui/Chinese-BERT-wwm">RoBERTa-wwm-large</a> | ***88.13*** | 330M |	***90.11*** |	86.82 | ***80.04*** | 82.78 | ***95.06*** | ***89.42*** | 84.90 | 95.32 | ***95.93*** |

表3.bert们在目前各种中文公共数据集上的表现<br />

# XLnet

