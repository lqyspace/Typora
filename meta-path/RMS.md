<img src="http://latex.codecogs.com/gif.latex?\sqrt{\sum\limits_{i=1}^{n}(x_{i}-y_{i})^2}" />
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

