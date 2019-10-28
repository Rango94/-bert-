# 后bert时代


**UNILM **[**《Unified Language Model Pre-training for Natural Language Understanding and Generation》**](https://arxiv.org/pdf/1905.03197.pdf)


**albert **[**《ALBERT: A LITE BERT FOR SELF-SUPERVISED LEARNING OF LANGUAGE REPRESENTATIONS》**](https://arxiv.org/pdf/1909.11942.pdf)


**xlnet **[**《XLNet: Generalized Autoregressive Pretraining for Language Understanding》**](https://arxiv.org/pdf/1906.08237.pdf)


**roberta **[**《RoBERTa: A Robustly Optimized BERT Pretraining Approach**](https://arxiv.gg363.site/pdf/1907.11692.pdf)[**》**]()


**spanbert **[**《SpanBERT: Improving Pre-training by Representing and Predicting Spans》**](https://arxiv.org/pdf/1907.10529.pdf)



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

表1.后bert时代的bert们所做出的改进<br />*unilm相比较bert多使用了三个预训练任务，分别是从左至右的语言模型，从右至左的语言模型，以及seq2seq模型。<br />*albert相比较bert，在nsp任务上添加一种特殊的负例，即模型输入的前一句是后一句的后文。

| XLnet | 将自编码式模型改为自回归式模型，期望填补原bert中mask符造成的训练，预测阶段的gap，主要是为生成式模型服务。 |
| --- | --- |
| roberta | 暴力的想要证明bert现有结构的优越性，对bert本身的结构并没有作出改变，仅仅是改变了训练的超参数。 |
| spanbert | 防止预测token对小范围的依赖，比如通过“杰伦”来预测“周”。 |
| UNILM | 使用了三个生成式语言模型，期望克服bert模型在生成式任务上存在的问题。 |
| albert | 通过一些列措施减少bert的参数量，并且仍然保证了相对较高的精度。 |

表2.bert们所想要解决的问题<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571907018003-5014cb49-f001-49a4-82d5-e9d5d6469330.png#align=left&display=inline&height=400&name=image.png&originHeight=800&originWidth=1818&search=&size=539131&status=done&width=909)<br />表3.bert们在目前各种中文公共数据集上的表现<br />
<br />[ChineseGLUE](https://github.com/chineseGLUE/chineseGLUE)
<a name="kNopY"></a>
# XLnet
<a name="WmRH8"></a>
## 语言模型的建模方式
<a name="cZgUj"></a>
### 自回归语言模型

给定一个序列![](https://intranetproxy.alipay.com/skylark/lark/__latex/c6c7cd29ca12b152ba94dd8491a29da3.svg#card=math&code=X%3D%5Bx_1%2Cx_1%2C...%2Cx_t%5D&height=20&width=140)<br />有自回归语言模型目标：<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571884070514-f3f8a1c5-a6af-466d-ae6b-001f6b660abd.png#align=left&display=inline&height=83&name=image.png&originHeight=166&originWidth=1456&search=&size=81284&status=done&width=728)<br />其中![](https://intranetproxy.alipay.com/skylark/lark/__latex/7943b5fdf911af3ffcf9d8f738478e8a.svg#card=math&code=%CE%B8&height=16&width=8)是模型参数，公式目的是找出一组模型参数![](https://intranetproxy.alipay.com/skylark/lark/__latex/7943b5fdf911af3ffcf9d8f738478e8a.svg#card=math&code=%CE%B8&height=16&width=8)使得概率![](https://intranetproxy.alipay.com/skylark/lark/__latex/61bbb5c7d3fc0bfdacd6dc7dd1527d1b.svg#card=math&code=%5Clog_%7Bp_%CE%B8%7D%28X%29&height=23&width=61)最大。<br />其中![](https://intranetproxy.alipay.com/skylark/lark/__latex/fa9cfc1a838be3f41fefb9a89007f43e.svg#card=math&code=h_%CE%B8%20%28X_%7B1%3At-1%7D%29&height=20&width=74)是模型对对应序列给出的内容表示，![](https://intranetproxy.alipay.com/skylark/lark/__latex/2e0e942c82f298d937b27bab24cb1aec.svg#card=math&code=e%28x%29&height=20&width=30)表示![](https://intranetproxy.alipay.com/skylark/lark/__latex/9dd4e461268c8034f5c8564e155c67a6.svg#card=math&code=x&height=12&width=9)的embedding。
<a name="bmEbo"></a>
### 自编码语言模型
令![](https://intranetproxy.alipay.com/skylark/lark/__latex/1a3256b27112d00c6ec522602d9c7d37.svg#card=math&code=%5Chat%7BX%7D%3Dmasked%28X%29&height=24&width=121)<br />令![](https://intranetproxy.alipay.com/skylark/lark/__latex/1ce0cdaf66b9bf4b74d1f118c30080ef.svg#card=math&code=%5Coverline%7BX%7D&height=21&width=15)是被mask的word序列。

![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571885554748-c0b56ebf-de96-435d-8e2b-bf7f1df18d73.png#align=left&display=inline&height=78&name=image.png&originHeight=156&originWidth=1554&search=&size=85781&status=done&width=777)<br />其中![](https://intranetproxy.alipay.com/skylark/lark/__latex/a693c996212e83f7c6fde2de0bbbf9d4.svg#card=math&code=m_t&height=14&width=20)=1表示![](https://intranetproxy.alipay.com/skylark/lark/__latex/cf7ee950cf61a6003c0ec4af7971d8a8.svg#card=math&code=x_t&height=14&width=15)被mask。<br />其中![](https://intranetproxy.alipay.com/skylark/lark/__latex/e94c2cb2ada81692f4ea2b79965fff3f.svg#card=math&code=H_%CE%B8%20%28%5Chat%7BX%7D%29%3D%5BH_%CE%B8%20%28%5Chat%7BX%7D%29_1%2CH_%CE%B8%20%28%5Chat%7BX%7D%29_2%2C...%2CH_%CE%B8%20%28%5Chat%7BX%7D%29_t%5D&height=24&width=292)，表示在输入为![](https://intranetproxy.alipay.com/skylark/lark/__latex/fd2b1eb3a3aacb801a8c6d0b7ec448b5.svg#card=math&code=%5Chat%7BX%7D&height=20&width=14)时，模型给出的每个![](https://intranetproxy.alipay.com/skylark/lark/__latex/1ba8aaab47179b3d3e24b0ccea9f4e30.svg#card=math&code=x_i&height=14&width=15)的编码。

<a name="aa2uV"></a>
### 自回归语言模型和自编码语言模型的区别
1.**独立性假设。**注意到在自编码的优化公式中，理论的优化目标和真实的优化目标之间是“![](https://intranetproxy.alipay.com/skylark/lark/__latex/fb4f353ef9a72c24566678c957a5ae9f.svg#card=math&code=%5Capprox&height=11&width=12)”，当且仅当被mask的词![](https://intranetproxy.alipay.com/skylark/lark/__latex/1ce0cdaf66b9bf4b74d1f118c30080ef.svg#card=math&code=%5Coverline%7BX%7D&height=21&width=15)之间都是独立的时候这个公式才是严格等于，而自回归式语言模型里是不存在这种独立性假设的。<br />2.**输入的噪音。**bert中有一个人工符号[MASK]，这个人工符号的embdding在训练阶段是直接参与编码的，也就是其他所有的token实际上都有[MASK]符号的信息。但是微调阶段是没有[MASK]符号信息的。而自回归式语言模型就没有这种问题。<br />3.**上下文依赖。**bert中可以同时编码上下文的内容，这是自回归式语言模型做不到的。

<a name="DFEL3"></a>
## 主体思想
**改进语言模型建模的方法，既要双向的信息，又要使用自回归式语言模型。**
<a name="U7wTw"></a>
## 排列语言模型
示例语句：New York is a city

- 从左往右读：1-->2-->3-->4-->5，New-->York-->is-->a-->city
- 从右往左读：5-->4-->3-->2-->1，city-->a-->is-->York-->New
- 乱序1：       4-->2-->1-->5-->3，a-->York-->New-->city-->is
- 乱序2：       2-->1-->5-->3-->4，York-->New-->city-->is-->a

**乱序指的是模型读这句话的顺序，不代表语句的真实顺序。**<br />**在RNN模型中，模型读语句的顺序就代表语句的真实顺序，但在transformer体系的模型中不是，因为transformer体系的模型中，顺序的建模是通过pos_embedding的方式来实现的。而RNN是通过模型读语句的先后顺序来实现的。**<br />**<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571896190436-33c633d4-7bb6-438c-b793-ad7536e93cc2.png#align=left&display=inline&height=665&name=image.png&originHeight=1330&originWidth=1716&search=&size=605573&status=done&width=858)<br />图1.在不同的排列里，预测同一个字所需要的信息<br />

<a name="CZYHh"></a>
## 双流自注意力（Two-Stream Self-Attention）

示例序列：语言序列<br />假设给定一个排列顺序3-->2-->4-->1

| 预测目标词 | 已有的信息 |
| --- | --- |
| (2, 言) | (3, 序) |
| (4, 列) | (3, 序) (2, 言) |
| (1, 语) | (3, 序) (2, 言) (4, 列) |


**要求1，在编码过程中体现排列顺序。**<br />**要求2，给模型明确的指示预测某个位置的词。**

**如何在编码过程体现排列顺序？**<br />注意力值矩阵<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571899966224-4187cf54-114c-4752-805a-a7c6ca66895f.png#align=left&display=inline&height=157&name=image.png&originHeight=1282&originWidth=1284&search=&size=318060&status=done&width=157)<br />白色代表置0<br />1的编码结果包含了1，2，3，4的信息<br />2的编码结果包含了2，3的信息<br />3的编码结果只包含了自己的信息<br />4的编码结果包含了2，3，4的信息<br />
<br />**模型怎么知道我要预测的是哪一个位置的词？**<br />目前肯定不能用![](https://intranetproxy.alipay.com/skylark/lark/__latex/9dd4e461268c8034f5c8564e155c67a6.svg#card=math&code=x&height=12&width=9)的编码结果去预测![](https://intranetproxy.alipay.com/skylark/lark/__latex/9dd4e461268c8034f5c8564e155c67a6.svg#card=math&code=x&height=12&width=9)，因为模型会作弊，那么尝试把对自己的注意力值给置0。<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571900453306-58daa5ef-3a77-4115-a1d9-47b172ff479b.png#align=left&display=inline&height=167&name=image.png&originHeight=1284&originWidth=1280&search=&size=370430&status=done&width=166)<br />
<br />1的编码结果包含了2，3，4的信息<br />2的编码结果包含了3的信息<br />3的编码结果没有信息<br />4的编码结果包含了2，3的信息<br />
<br />第二个自注意力层：<br />1的编码结果包含了2，3的信息<br />2的编码结果没有信息<br />3的编码结果没有信息<br />4的编码结果包含了2的信息<br />
<br />**传统的多头自注意力是无法同时满足这两个要求的。**<br />**<br />**双流自注意力：**<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571897710073-2d88afdf-3ddc-4337-a5c8-1b220c799354.png#align=left&display=inline&height=544&name=image.png&originHeight=1088&originWidth=1862&search=&size=798913&status=done&width=931)

**content stream：内容流，自己可以看到自己。**<br />**query stream：查询流，自己不能看到自己。**<br />
<br />
<br />![](https://intranetproxy.alipay.com/skylark/lark/__latex/65d5c3a7f9f9b49019542a15696e999a.svg#card=math&code=h_i%5E%7B%28m%29%7D&height=26&width=31)代表在**内容流**中，第![](https://intranetproxy.alipay.com/skylark/lark/__latex/6f8f57715090da2632453988d9a1501b.svg#card=math&code=m&height=12&width=14)层![](https://intranetproxy.alipay.com/skylark/lark/__latex/1ba8aaab47179b3d3e24b0ccea9f4e30.svg#card=math&code=x_i&height=14&width=15)的编码中间结果，这个中间结果是**可以**看到自己的。<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571901783441-a687c519-25d5-47f2-87ec-9206b9f194d9.png#align=left&display=inline&height=150&name=image.png&originHeight=300&originWidth=500&search=&size=82410&status=done&width=250)<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571902180768-78560161-6b4a-45d3-8f42-d658d0e8a524.png#align=left&display=inline&height=42&name=image.png&originHeight=84&originWidth=1546&search=&size=60008&status=done&width=773)<br />其中![](https://intranetproxy.alipay.com/skylark/lark/__latex/5a5ae0760dc3dac91e546c0ea25586b0.svg#card=math&code=z_i&height=14&width=13)指被重新排序后的新序列中第![](https://intranetproxy.alipay.com/skylark/lark/__latex/865c0c0b4ab0e063e5caa3387c1a8741.svg#card=math&code=i&height=16&width=5)个词。

![](https://intranetproxy.alipay.com/skylark/lark/__latex/4de288a7352ba88de5eb047bdb679509.svg#card=math&code=g_i%5E%7B%28m%29%7D&height=26&width=29)代表在**查询流**中，第![](https://intranetproxy.alipay.com/skylark/lark/__latex/6f8f57715090da2632453988d9a1501b.svg#card=math&code=m&height=12&width=14)层![](https://intranetproxy.alipay.com/skylark/lark/__latex/1ba8aaab47179b3d3e24b0ccea9f4e30.svg#card=math&code=x_i&height=14&width=15)的编码中间结果，这个中间结果是**不可以**看到自己的。<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571901794901-3a43fe53-29a7-4ff9-b2be-5c062a13735f.png#align=left&display=inline&height=137&name=image.png&originHeight=274&originWidth=482&search=&size=69155&status=done&width=241)<br />![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2019/png/225896/1571902053140-768f5b01-6c83-4c9f-a4b7-a26f650229cb.png#align=left&display=inline&height=44&name=image.png&originHeight=88&originWidth=1622&search=&size=55869&status=done&width=811)

**在预测过程中，查询流将被丢弃只保留内容流。**<br />**<br />**<br />**<br />**<br />**<br />**

<a name="I0jts"></a>
## 

<br />








