[TOC]

# Reinforced Meta-path Selection for Recommendation on Heterogeneous Information Networks

> 引用

```
Ning W, Cheng R, Shen J, et al. Reinforced Meta-path Selection for Recommendation on Heterogeneous Information Networks[J]. arXiv preprint arXiv:2112.12845, 2021.
```

> 摘要：

- 异构信息网络可以捕获各种实体的复杂关系并可以用来提高各种数据挖掘任务的性能。
- 许多基于异构信息网络的推荐算法通过手动构建元路径来获取网络中的语义信息，这些算法依赖于广泛的领域知识，可以选择最佳的元路径集。
- 对于异构信息网络非常复杂的应用，手动构建元路径是非常繁琐和易于出错的。
- 提出基于强化学习的元路径选择器Agent（RMS），将其应用于现存的基于元路径的推荐其器中。
- 为了确认高质量的元路径，RMS训练一个基于强化学习的策略网络 agent, 这个策略网络 agent 从下游的推荐任务中获取奖励。
- 然后构建出一个 RMS-HRec 模型，它可以利用高效的元路径。

> 关键词

==recommender system，reinforcement learning，meta-path，graph neural network==

> 发展状况：

- 传统的推荐算法经常使用 user-item 交互历史来产生结果。然后异构信息网络能够通过分析交互日志来产生更多准确的结果。因此获得广泛的关注。
- 使用 **meta-path** 的概念来提取有用的信息，参考：[Neural PathSim for Inductive Similarity Search in Heterogeneous Information Networks](https://dl.acm.org/doi/abs/10.1145/3459637.3482454)[1]
- 大量的基于元路径的推荐算法被提出：
  - [Heterogeneous Information Network Embedding for Recommendation ](https://ieeexplore.ieee.org/abstract/document/8355676)[2]：利用不同元路径中的语义信息来增强HIN中的用户和项的表征。
  - [Metapath- and Entity-aware Graph Neural Network for Recommendation](https://arxiv.org/abs/2010.11793)[3]：应用了自注意力机制来整合不同元路径中可用的信息
  - [Leveraging Meta-path based Context for Top- N Recommendation with A Neural Co-Attention Model](https://dl.acm.org/doi/abs/10.1145/3219819.3219965)[4]：提出传统的基于HIN的推荐模型没有解决两个主要问题：
    - 1、很少能够学习路径或者元路径的表征
    - 2、并没有考虑在交互中，元路径和它所涉及的 user-item 的相互影响。
    - 3、从而提出了一种基于共同注意力机制的新型深度神经网络，用于进行基于元路径的上下文的Top-N推荐。
- 但是如何选择最有效率的元路径，在现在的严重并没有被很好地解决。

 

> 参考文献

[1] Xiao W, Zhao H, Zheng V W, et al. Neural PathSim for Inductive Similarity Search in Heterogeneous Information Networks[C]//Proceedings of the 30th ACM International Conference on Information & Knowledge Management. 2021: 2201-2210.

[2] C. Shi, B. Hu, W. X. Zhao and P. S. Yu, "Heterogeneous Information Network Embedding for Recommendation," in IEEE Transactions on Knowledge and Data Engineering, vol. 31, no. 2, pp. 357-370, 1 Feb. 2019, doi: 10.1109/TKDE.2018.2833443.

[3] Anwaar M U, Han Z, Arumugaswamy S, et al. Metapath-and Entity-aware Graph Neural Network for Recommendation[J]. arXiv preprint arXiv:2010.11793, 2020.

[4] Hu B, Shi C, Zhao W X, et al. Leveraging meta-path based context for top-n recommendation with a neural co-attention model[C]//Proceedings of the 24th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining. 2018: 1531-1540.
