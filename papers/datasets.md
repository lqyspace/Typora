## [MovieLens](https://grouplens.org/datasets/movielens/)

**MovieLens** 是一个[推荐系统](https://zh.m.wikipedia.org/zh-hans/推薦系統)和[虚拟社区](https://zh.m.wikipedia.org/wiki/虛擬社區)网站，于1997年建立。其主要功能为应用[协同过滤](https://zh.m.wikipedia.org/wiki/協同過濾)技术和用户对电影的喜好，向用户推荐电影。

- GroupLens
  - GroupLens实验室创建于1992年，于1997年创建MovieLens。
- 电影推荐
  - MovieLens根据用户对一部份电影的评分，预测该用户对其他电影的评分。
- 标签系统
  - 标签是MovieLens的一个重要应用，用户可以对电影添加标签，并根据其他用户的标签了解电影信息，搜索电影。
- 问答系统
  - MovieLens系统包括以电影为主题的问答系统。用户可以提问，回答，并对回答打分。

> 数据集

- ratings.csv
  - 格式：userId，movieId，rating，timestamp
- tags.csv
  - 格式：userId，movieId，tag，timestamp
- movie.csv
  - 格式：movieId，title，genres
- links.csv
  - 格式：movieId，imdbId，tmdbId



> MovieLens（确定系知识图谱）如何与不确定知识图谱对接

使用的确定性知识图谱：

- MovieLens
  - 确定性：导演，演员，

使用的不确定知识图谱：

- CN15k
- NL27k

思路：

- 不确定知识图谱的关系事实作为MovieLens（确定性知识图谱）的子图

对接方式：

- 自己手动列举不确定的关系事实，然后从不确定知识图谱中获取置信度得分
  - 用户的主观类型，用户观感、
  - 拍摄风格，建筑（不好采集，不太可行）
- 从不确定性知识图谱中获取不确定的关系事实以及不确定关系事实置信度得分



> 下一周

1、找另外一个数据集：亚马逊；商品数据集，旅行网站（带有不确定性的，带有用户评论的）

2、了解以及会使用**知识图谱**。

3、讲讲带评论的数据集，和nell、conceptNet一起讲。

4、数据集必须有**不确定的因素**。

5、必须有**NLP**的东西。

6、类型化，结构化，难当作不确定。

7、用户的评论很重要（意图，态度）。



> 2022.7.21

## 不确定数据集

> [yelp_dataset](https://www.yelp.com/dataset/documentation/main)

- business.json
  - 包含业务数据，包括位置数据，属性和类别

- review.json
  - 包含完整的评论文本数据，包括撰写评论的 user_id 和撰写评论的 business_id.

- user.json
  - 用户数据包括用户的好友映射以及与用户相关的所有元数据。

- tip.json
  - 用户写的关于企业的提示。提示比评论短，并且倾向于传达快速的建议.



> [Amazon Review Data（2018）](https://nijianmo.github.io/amazon/index.html)

该数据集包括评论（review）（评分、文本、帮助投票）、产品元数据（Metadata）（描述、类别信息、价格、品牌和图像特征）和链接（links）（也查看/还购买了图表）。

**1、review**

- **`reviewerID` - ID of the reviewer, e.g. [A2SUAM1J3GNN3B](http://www.amazon.com/gp/cdp/member-reviews/A2SUAM1J3GNN3B)**
- **`asin` - ID of the product, e.g. [0000013714](http://www.amazon.com/dp/0000013714)**
- `reviewerName` - name of the reviewer
- `vote` - helpful votes of the review
- `style` - a disctionary of the product metadata, e.g., "Format" is "Hardcover"
- **`reviewText` - text of the review**
- `overall` - rating of the product
- `summary` - summary of the review
- `unixReviewTime` - time of the review (unix time)
- `reviewTime` - time of the review (raw)
- `image` - images that users post after they have received the product

**2、Metadata**

- **`asin` - ID of the product, e.g. [0000031852](http://www.amazon.com/dp/0000031852)**
- `title` - name of the product
- `feature` - bullet-point format features of the product
- `description` - description of the product
- `price` - price in US dollars (at time of crawl)
- `imageURL` - url of the product image
- `imageURL` - url of the high resolution product image
- `related` - related products (also bought, also viewed, bought together, buy after viewing)
- `salesRank` - sales rank information
- `brand` - brand name
- `categories` - list of categories the product belongs to
- `tech1` - the first technical detail table of the product
- `tech2` - the second technical detail table of the product
- `similar` - similar product table

---



> 2022.7.26

- **conceptnet5**与**亚马逊数据**集评论做对接：实体和关系

- 两个数据集的重叠

- 是否分词？

- 写到开题报告：是否重叠

- 先做英文，做通

- 后期中文

 

- 图上找路径还是候选集找更优的路径（可以写到开题报告）
- 可以开始写开题报告



> 2022.8.2

- 知识图谱的存储与查询
  - 存储
    - 图存储
    - 关系型存储
  - 查询
    - Gremlin
    - Neo4j

- 数据分析
  - **后期：**打算先学一下**Neo4j**，绘制知识图谱，使其可视化
  - 把conceptnet5和亚马逊数据集可视化
  - 如何对接



> 重点

- 子串匹配
- 算法里添加：计数器
- 重叠的部分索引另外保存

- 前期的数据分析不考虑拼接
- **前期：KMP子串匹配**（算法层面）
  - 共同的子串做拼接成另外一个数据集（一个大图）
    - 扩展子串匹配的算法
  - 没有重叠的部分过滤掉
- 数据集与知识图谱没有重叠：是否近义词，同义词
- conceptnet5的词与数值对应
  - 举例输出看看形式 
- neo4j放在第五章，做应用



> conceptNet查找规则

1、如果查找实体不匹配，末尾减少单词一个字母，继续匹配，求匹配的所有实体的均值（能用上）

2、如果匹配实体不是英语（其他的语言），在匹配不到具体ConceptNet实体内容的情况下，把该实体当作英文对待（用不上）

3、KMP算法搜索（目前用不上，后期可能用的上）



> 评论内容是否做主题提取

eg1：This is a charming version of the classic Dicken\'s tale.  Henry Winkler makes a good showing as the "Scrooge" character.  Even though you know what will happen this version has enough of a change to make it better that average.  If you love A Christmas Carol in any version, then you will love this.

```
译：这是经典狄更斯故事的迷人版本。亨利·温克勒 (Henry Winkler) 作为“史克鲁奇”的角色表现出色。即使您知道会发生什么，此版本也有足够的变化以使其更好地达到平均水平。如果你喜欢任何版本的圣诞颂歌，那么你会喜欢这个
```

主题：charming version、classic Dicken\'s tale、Henry Winkler、the "Scrooge" character、enough change、make it better 、love、 A Christmas Carol，love、 this

eg2：'It was good but not as emotionally moving as the the Christmas Carol by Dickens  I like Christmas movies that make me sigh'

```
它很好，但不像狄更斯的圣诞颂歌那样动人，我喜欢让我叹息的圣诞电影。
```



- 分词，目标：知识图谱里面有；提前把词的关系在conceptnet检索出来
- 在所有的三元组里面选top k=20
- Amazon提供关键词，conceptnet提供三元组
- 把评论转成图，通过知识图谱
- 下一次，评论的top-k；类似this这样的词，代词，介词，去掉——>黑名单，过滤掉对推荐无用的词
- 分词-->去停用词-->过滤（前处理）-->确定关键词
  - 类似于 not 这样的词：not as emotionally moving as 还是 not、moving
  - 使用
  - 是否自己做库
- 先枚举，再考虑后期的优化
- 先写功能，后期优化



> Amazon数据集

1、有点大（23.5g），电脑跑崩了---->前期做实验的时候不用太大的数据集

2、数据集选择，可以分类

 



- 1、建个亚马逊词典
- 2、把词典里的相关词提取以扩充
- 3、按相关性进行聚类为一个桶
- 先做一个同义词，同义词典



- 桶：相关词的桶——>分类
- 聚类：海量评论



1、分词（规模多大，形成测试集）-----**做自己停用词，结构规则的模板**

- 搜模板、看看别人是否有相同的任务
- ![image-20220906102424083](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202209061024199.png)
- ![image-20220906103351085](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202209061033130.png)
- 查双向api

2、数据集合，抽象，定义集合

3、**程序符号的命名，数据结构，类型定义**

4、把数据处理的过程形成在开题报告里面

5、一个总图，三四五三张抽取三个子图



> Amazon词典格式

```json
mydict: {
    "keyword1": { # 评论里的一个关键词，已去重
        "PID": { # 关键词对应的 产品ID 以及 频数count
            asin1: count,
            asin2: count,
            ...
            asinN: count
        },
        "related_word": { # 与关键词相关的词
            "related_kw1": { # 相关词 kw1
                "asin": { # 相关词所对应的 产品ID 以及 频数count
                    asin1: count,
                    asin2: count,
                    ...
                    asinN: count
                }
                "language": "en", # 相关词的语言
                "rel" : "RelatedTo", # 关键词与相关词的关系
                "weight": 5.2911212 # 关键词与相关词的权重
            },
            "related_kw2": {}, # 相关词 kw2
            "related_kw3": {}, # 相关词 kw3
            ...
            "related_kwN": {} # 相关词 kw4
        },
        "related_type": { # 关键词所拥有的所有关系relation
            "RelatedTo": true,
			"AtLocation": true,
			"CapableOf": true,
			"Synonym": true
        }
    },
    "keyword2": {}, # 关键词2
    "keyword3": {}, # 关键词3
    ...
    "keywordN": {} # 关键词N
}
```

- 拟解决的关键问题：
  - 将关键词和关键词的相关词都作为关键词
  - 过滤掉关键词中PID为空的词
  - 双向查找关键词与相关词的相关性



> 优化词典

- 优化：多线程、基于第一轮的查询做第二轮
- 基于扩充的数据集和传统的推荐算法，看效果
  - 其实用的是user-item（矩阵），并不是图
- 传统的推荐的算法选择：（协同过滤）
- 扩充过的 和 没有扩充的 在同一模型下 作比较

 

> 论文题目设计思路

3.1 设计思想

3.2 细微模块和算法

...

3.x 细节若干

3.3 实验步骤



- 拟解决的关键问题：

  - 起初单向，后来双向
  - 逆向扫描性能低，改进提高性能
    - 多线程、AC自动机

- 后期改进：

1、 把词典库建成关键词树

	- asin
	- weight

2、直接用评论，分词，一个字符串

3、用字符串去遍历树



**4、优化关键词树**

Beautiful

Beauty

时态，词性：don't care

依据单词的特性去压缩：根据单词的性质，找前缀，不能有意思不一样的

1、单词的前缀必须唯一：先建前缀数组（相同前缀的单词是否有同一个意思），再建关键词树（用前缀建树）

2、输入：beauty，b,e,a,u，搜到u，

用前缀模糊查询asin

> 前缀举例

app

apple

application

> 拟解决的关键问题

拟解决的关键问题：

- 建前缀数组
  - 考虑



注：看看有没有别人优化的更好的AC自动机



==2022-11-15==

### 第三章小论文

> 目标

- 1、证明语义扩充过的数据集可以提升推荐性能
  - 数据集：==Amazon、不确定知识图谱ConceptNet3==
    - ​	==融合不确定知识图谱异构网络1==
  - 模型：HREC（内核：HAN，HREC为作者改优的基于元路径的推荐模型）、**HERec、MCRec**
  - 过程：1、==构词过程（AC自动机）2==；2、以 **有/无语义扩充的数据集 **为HREC的数据集，查看推荐性能
  - 指标：recall、ACC，Micro-f1、Macro-f1
- 2、~~用已经扩充语义的数据集进行多个不同推荐模型的性能试验~~



问题：分词（a1）——>去停用词(a2)——>人工去停用词（a3）

### 第四章实验

> 目标

- 1、训练智能体，让智能体在  **被语义扩充过的异构信息网络**  中选择高质量元路径
  - 数据集：经过 **语义扩充的异构信息网络**
  - 







## [CN15K](https://docs.ampligraph.org/en/develop/generated/ampligraph.datasets.load_cn15k.html)

​		CN15k 是常识 [KG ConceptNet](https://conceptnet.io/) 的子图。该子图包含 15,000 个实体和 241,158 个英语不确定关系事实。 ConceptNet 中的原始分数从 0.1 到 22 不等，其中 99.6% 小于或等于 3.0。对于归一化，我们首先将置信度分数绑定到 [0.1, 3.0]，然后应用最小-最大归一化将它们映射到 [0.1, 1.0]。

​	[ConceptNet](https://conceptnet.io/) [SCH16] 是一个免费提供的语义网络，旨在帮助计算机理解人们使用的单词的含义。

​	[ConceptNet](https://conceptnet.io/) 起源于众包项目 Open Mind Common Sense，该项目于 1999 年在麻省理工学院媒体实验室推出。此后，它已经发展到包括来自其他众包资源、专家创建的资源和有目的的游戏的知识。

​	[ConceptNet](https://conceptnet.io/) 用于创建词嵌入——将词义表示为向量，类似于 word2vec、GloVe 或 fastText，但更好。

​	有关 ConceptNet 的详细文档出现在其 [GitHub wiki](https://github.com/commonsense/conceptnet5/wiki) 上。

​	ConceptNet 是一个多语言知识库，代表人们使用的单词和短语以及它们之间的常识关系。 ConceptNet 中的知识是从各种资源中收集的，包括众包资源（如维基词典和 Open Mind Common Sense）、有目的的游戏（如 Verbosity 和 nadya.jp）以及专家创建的资源（如WordNet 和 JMDict）。



## [NL27k](https://docs.ampligraph.org/en/develop/generated/ampligraph.datasets.load_nl27k.html)

​		NL27k 是从 NELL (Mitchell et al. 2018) 中提取的，这是一个从网页阅读中获得的不确定的 KG。 NL27k 包含 27,221 个实体、404 个关系和 175,412 个不确定的关系事实。在最小-最大归一化过程中，我们从 0.1 到 0.9 寻找下边界。我们发现将置信度分数标准化为区间 [0.1, 1] 会产生最佳结果。

​	NL27K 最初是在 [CCS+19] 中提出的。它是从网页收集数据的永无止境语言学习 (NELL) 数据集 [MCH+18] 的子集。三元组的数值代表链路的不确定性。



## [PPI5k](https://docs.ampligraph.org/en/develop/generated/ampligraph.datasets.load_ppi5k.html)

​		蛋白质-蛋白质相互作用知识库 STRING 用发生概率标记蛋白质之间的相互作用。 PPI5k 是 STRING 的子集，包含 271,666 个不确定关系事实，用于 5,000 种蛋白质和 7 种相互作用。

​		PPI5K 最初在 [CCS+19] 中提出，是蛋白质-蛋白质相互作用 (PPI) 知识图谱 [SM+16] 的一个子集。数值代表基于现有科学文献证据的链接的置信度



## 参考文献

|            | [CCS+19] | Xuelu Chen, Muhao Chen, Weijia Shi, Yizhou Sun, and Carlo Zaniolo. Embedding uncertain knowledge graphs. In *AAAI*, volume 33, 3363–3370. 2019. |
| ---------- | -------- | ------------------------------------------------------------ |
|            |          | 确定性知识图（KG）的嵌入模型已经被广泛研究，目的是捕获实体之间的潜在语义关系并将它们包含的结构化知识整合到机器学习中。然而，有许多 KG 对不确定的知识进行建模，它们通常用置信度分数对关系事实的固有不确定性进行建模，而嵌入这种不确定的知识代表了一个未解决的挑战。通过提供更自然的知识表征，不确定知识的捕获将有利于许多知识驱动的应用程序，例如问答和语义搜索。在本文中，我们提出了一种新颖的不确定 KG 嵌入模型 UKGE，旨在保留嵌入空间中关系事实的结构和不确定信息。与以前使用二元分类技术表征关系事实的模型不同，UKGE 根据不确定关系事实的置信度分数来学习嵌入。为了进一步提高 UKGE 的精度，我们还引入了概率软逻辑来推断训练期间看不见的关系事实的置信度分数。我们基于不同的置信度评分建模策略提出并评估了 UKGE 的两种变体。通过三个任务，即置信度预测、关系事实排名和关系事实分类，对三个真实世界的不确定 KG 进行了实验。 UKGE 通过取得有希望的结果显示出在捕获不确定知识方面的有效性，并且在这些任务上始终优于基线。 |
| NELL       | [MCH+18] | Tom Mitchell, William Cohen, Hruschka, and others. Never-ending learning. *Communications of the ACM*, 61(5):103–115, 2018. |
|            |          | 尽管人们多年来从不同的经历中学习了许多不同类型的知识，并且随着时间的推移成为更好的学习者，但大多数当前的机器学习系统都更加狭窄，仅学习基于单个数据集的统计分析的单个函数或数据模型。我们建议，正是因为这种差异，人们比计算机学得更好，我们建议机器学习研究的一个关键方向是开发软件架构，使智能代理也能够学习多种类型的知识，持续多年，并变得更好学习者随着时间的推移。在本文中，我们更准确地为机器学习定义了这种永无止境的学习范式，并提出了一个案例研究：**永无止境的语言学习器（NELL）**，它实现了永无止境的学习者的许多期望属性。自 2010 年 1 月以来，**NELL 一直在学习每天 24 小时阅读网络，到目前为止，已经获得了一个包含 1.2 亿个不同的置信加权信念（例如，servedWith(tea,biscuits)）的知识库**，同时学习了数千个相互关联的函数，这些函数不断地随着时间的推移提高其阅读能力。 **NELL 还学会了对其知识库进行推理，以从已有的知识中推断出它尚未读取的新信念，并且 NELL 正在发明新的关系谓词来扩展它用来表示信念的本体**。我们描述了 NELL 的设计、说明其行为的实验结果，并作为永无止境的学习案例研究讨论了它的成功和缺点。 NELL 可以在 http://rtw.ml.cmu.edu 在线跟踪，并在 Twitter 上@CMUNELL 关注 |
| ConceptNet | [SCH16]  | Robyn Speer, Joshua Chin, and Catherine Havasi. Conceptnet 5.5: an open multilingual graph of general knowledge. *CoRR*, 2016. URL: http://arxiv.org/abs/1612.03975, [arXiv:1612.03975](https://arxiv.org/abs/1612.03975). |
|            |          | 通过为其提供特定的知识和外部信息来源，可以改进关于语言的机器学习。我们在这里展示了一个新版本的链接开放数据资源 ConceptNet，它特别适合与现代 NLP 技术（如词嵌入）一起使用。 ConceptNet 是一个知识图谱，它将自然语言的单词和短语与标记的边缘连接起来。它的知识是从许多来源收集的，包括专家创建的资源、众包和有目的的游戏。它旨在代表理解语言所涉及的一般知识，通过允许应用程序更好地理解人们使用的单词背后的含义来改进自然语言应用程序。当 ConceptNet 与从分布式语义（如 word2vec）中获取的词嵌入相结合时，它为应用程序提供了一种理解，即它们无法单独从分布式语义中获取，也无法从 WordNet 或 DBPedia 等较窄的资源中获取。我们通过对单词相关性的内在评估的最新结果证明了这一点，这些结果转化为对单词向量应用的改进，包括解决 SAT 风格的类比 |
| STRING     | [SM+16]  | Damian Szklarczyk, John H Morris, and others. The string database in 2017: quality-controlled protein–protein association networks, made broadly accessible. *Nucleic acids research*, pages gkw937, 2016. |
|            |          | 对细胞功能的全系统理解需要了解表达的蛋白质之间的所有功能相互作用。 STRING 数据库旨在通过整合大量生物体的已知和预测的蛋白质-蛋白质关联数据来收集和整合这些信息。 STRING 中的关联包括直接（物理）相互作用和间接（功能）相互作用，只要两者都是特定的且具有生物学意义。除了收集和重新评估关于蛋白质-蛋白质相互作用的可用实验数据，以及从精选数据库中导入已知途径和蛋白质复合物外，相互作用预测还来自以下来源：（i）系统共表达分析，（ii）检测共享选择性跨基因组的信号，（iii）科学文献的自动文本挖掘和（iv）基于基因正交学的生物体之间相互作用知识的计算转移。在最新版本的 STRING 10.5 中，最大的变化与数据传播有关：Web 前端已经完全重新设计，以减少对过时浏览器技术的依赖，并且现在还可以从流行的 Cytoscape 软件框架内查询数据库。进一步的改进包括对功能丰富的用户输入的自动背景分析，以及简化的下载选项。 STRING 资源可在线获取，网址为 http://string-db.org/。 |

