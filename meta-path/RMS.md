<img src="http://latex.codecogs.com/gif.latex?\sqrt{\sum\limits_{i=1}^{n}(x_{i}-y_{i})^2}" />
![](http://latex.codecogs.com/gif.latex?\sqrt{\sum\limits_{i=1}^{n}(x_{i}-y_{i})^2})

![](http://latex.codecogs.com/gif.latex?\sqrt{\sum\limits_{i=1}^{n}(x_{i}-y_{i})^2})
# RMS

## State

- 状态state存储当前元路径的结构信息
- 对于每一条元路径，使用 `Relation IDs` 来表示它
- 编码一条元路径：
  - 二进制
  - 包含在元路径里面的关系的位置赋 1
  - 不包含在元路径里面的关系的位置赋 0
  - 举例：
    - 如果一个异构图有6种关系，则分配关系ID 1-6。
    - 如果一条元路径 $\phi$：[2, 6, 4]，那么它的 $E\phi$ 为 (0, 1, 0, 1, 0, 1)
- 第 $i$ 步的状态 $s_i = Normalize(\sum_{\phi \in {\Phi_i}} E_{\phi})$  ; $\Phi_{i}$ 为第 $i$ 步的元路径的集合
- 使用 $\lbrace User-Item-User \rbrace$ 和 $\lbrace Item-User-Item \rbrace$  作为 初始集。



## Action

- the action space $A_{s_{i}}$ for a state $s_{i}$ is all the relations $(r_1,r_2,...)$ that appear on a HIN, with a special action $STOP(r_0)$ .
- At each step, the policy network will predict an action to extend the current meta-path set to yield a higher reward during the long period.
- the policy network selects the $STOP(r_{0})$ or the current exceeds the maximal step limit $I$, the meta-paths set will not be extended.
- 生成对的元路径
- 然后扩展所有的元路径使用这个自动对称后的关系，并且把这个关系插入到当前的元路径的集合中
- 之前的元路径的会被复制并保留在元路径的集合中，不会被删除。
- 举例：
  - 当前用户元路径：$\lbrace U-M-U \rbrace$ 
  - 预测的action（relations）is $M-A$ ,自动对称：$M-A-M$ 
  - 下一步的元路径的集合：
    - $\lbrace U-M-U, U-M-A-M-U \rbrace$
    - 由于 $M-A-M$ 并不是以用户类型开头，所以不能被添加到user meta-path set。



## Policy

- the policy 是一个状态转移函数，用于搜索基于当前状态的最优action
- 目标：最大化折扣累计奖励 $R = \sum_{i=i_0}^I \gamma^{i-i_0} r_i$ ，$I$ 是最大的步骤限制。$\gamma$ 是0到1之间的真值，衰减因子。
- 最大化reward： $\pi(s) = \mathop{\arg\max}\limits_{\alpha}Q(s,a)$ 
- 更新Q：$Q(s_{i},a_{i}) = r+\gamma Q(s_{i+1},\pi(s_{i+1}))$ 



## Reward

- 如果选择 $STOP$ ,reward=0；如果选择无效的动作，reward=-1，否则：$R(s_i,a_i) = N(s_i,a_i)-N(s_{i-1},a_{i-1})$ 
- $N(s_i,a_i)$ 是第 $i$ 步推荐任务的性能
- 采用 $NDCG@10$ 作为性能矩阵



## Agent

- 基于当前的元路径的集合得到当前的状态
  - **输入**：智能体把 **当前的元路径集合** 嵌入到一个 **状态向量** 中，这个 **状态向量** 作为智能体的 **输入**
  - **输出**：智能体会产生下一个关系（**relations**）
  - 得到的关系**（relations）**用于更新 **当前的元路径集合**，从而产生一个 **新的集合**



## Recommender

- **recommender**：任何基于元路径的model
- 设计了一些训练技巧来更好地训练 **HRec**
  - 1、邻居采样技术：随机采样结点邻居的子集
  - 2、设置阈值 $t$ 确保结点的有效性
- **RMS-HRec** 可以自动设计元路径

# RMS复现

## 数据集的统计

- Yelp
  - 实体数量统计
  - 实体间关系的统计
    - 如何进行实体间相互关系的统计
- Douban Movie
  - 实体
  - 实体关系

## 如何评价性能

> 基本过程

智能体（**agent**）在当前的环境中，基于当前的状态（**state**），这个状态（state）是：元路径的集合（该集合被编码为一个状态向量）。





产生下一个动作，这个动作是关系（**relations**），使用这个新的关系来更新当前的元路径：

- 扩展所有的元路径
- 保留以前的元路径，并把新的被扩展的元路径插入到集合中





推荐器（**recommender**）使用新的元路径的集合，进行推荐：	

​	**具体过程**：在训练agent智能体的时候，只进行一个epoch的训练和评估。在智能体被很好地训练之后，使用由智能体训练得到的元路径集来训练推荐器，也是进行一个epoch的训练和评估，

​	性能评价指标：$NDCG@10$ 作为性能矩阵，每个epoch的 $Reward(s_i,a_i) = N(s_i,a_i)-N(s_{i-1},a_{i-1})$ ；如果智能体选择的 $STOP(r_0)$ , $Reward = 0$ ;如果智能体选择 **无效的动作**（没有改变元路径集合），$Reward = -1$  。



​	后期的推荐：使用由训练好的agent产生的元路径，放入推荐器中，推荐 top N。

## 下一步计划

> Code

- 1、根据RlCard的agent环境以及相关代码设计RMS的agent环境
- 2、根据HERec的模型（已在github上找到相关源码以及数据集），根据这个模型，查看如何基于meta-path做推荐
- 3、数据集的使用：Yelp的官方数据有使用教程，入门

> 待解决

- 1、设计RMS自己的env
- 2、根据数据集进行实体关系（relations）的统计
- 3、复现HERec模型



# RlCard与推荐的映射

## 以斗地主为例

- State（状态空间）：在游戏的每个决策点上，每个玩家以自己的角度能够观察到的当前状态的所有信息。以下以 **字典** 的形式呈现状态结构。

  ![image-20220513134837083](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20220513134837083.png)



- Action（动作空间）：54张牌，根据不同的牌的组合可有27472种组合。在新版的RlCard里，出牌的动作空间有27472种，分别编码0-27471。在旧版的RlCard里，出牌的动作空间被抽象成309种，抽象过程：例如：“33344”被抽象为“333 **”。
- Action Encoding：54-d one-hot vector

![image-20220513135040258](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20220513135040258.png)



- 收益规则：如果地主先出完手中所有牌，获胜并得1分。如果任一平民出完所有牌，获胜得1分。



- 卡牌action与电影推荐一一对应
- 强化学习
- 第三章与第四章



- 1. 每位研一同学选定2～3篇参考文献（近三年顶级），作为开题和硕士论文的重要参考。2. 从文献中抽取出研究的问题，下次例会详细汇报。要给出问题的形式化定义（即数学模型）、具体案例或应用场景（即举例说明该问题的研究意义）。3. 一个规范：在ppt或文档中提及论文时，要按照参考文献的格式给出完整信息。











































1、找2-3篇论文，找出论文中研究的问题，解决的是什么场景，具体的应用（做什么**what**、为什么做**why**）

2、我的问题：基于深度强化学习的推荐任务下   异构信息网络元路径    的研究与挖掘

- 基于元路径的推荐模型很多，但是关于元路径的设计很少
- 人工设计元路径存在易出错，繁琐，存在个人偏见等问题
- 基于强化学习，由智能体设计出尽可能最优的元路径，从而提高推荐的效率

3、给出形式化定义、给出具体应用，加入案例（实际生活中的案例）
