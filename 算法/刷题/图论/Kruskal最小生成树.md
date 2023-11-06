# Kruskal最小生成树

Kruskal 算法其实很容易理解和记忆，其关键是要熟悉并查集算法，如果不熟悉，建议先看下前文 [Union-Find 并查集算法](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247484751&idx=1&sn=a873c1f51d601bac17f5078c408cc3f6&scene=21#wechat_redirect)。



## 什么是最小生成树

**先说「树」和「图」的根本区别：树不会包含环，图可以包含环**。

如果一幅图没有环，完全可以拉伸成一棵树的模样。说的专业一点，树就是「无环连通图」。

那么什么是图的「生成树」呢，其实按字面意思也好理解，就是在图中找一棵包含图中的所有节点的树。专业点说，生成树是含有图中所有顶点的「无环连通子图」。

容易想到，一幅图可以有很多不同的生成树，比如下面这幅图，红色的边就组成了两棵不同的生成树：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261328900.png)

对于加权图，每条边都有权重，所以每棵生成树都有一个权重和。比如上图，右侧生成树的权重和显然比左侧生成树的权重和要小。

**那么最小生成树很好理解了，所有可能的生成树中，权重和最小的那棵生成树就叫「最小生成树」**。

> PS：一般来说，我们都是在**无向加权图**中计算最小生成树的，所以使用最小生成树算法的现实场景中，图的边权重一般代表成本、距离这样的标量。



## Union-Find并查集算法

[自己去回顾：Union-Find并查集算法](https://labuladong.github.io/algo/di-yi-zhan-da78c/shou-ba-sh-03a72/bing-cha-j-323f3/)

前文Union-Find 并查集算法运用介绍过 Union-Find 算法的一些算法场景，而它在 Kruskal 算法中的主要作用是保证最小生成树的合法性。

因为在构造最小生成树的过程中，你首先得保证生成的那玩意是棵树（不包含环）对吧，那么 Union-Find 算法就是帮你干这个事儿的。

怎么做到的呢？先来看看力扣[第 261 题「以图判树」](https://leetcode.cn/problems/graph-valid-tree/description/)，我描述下题目：

给你输入编号从`0`到`n - 1`的`n`个结点，和一个无向边列表`edges`（每条边用节点二元组表示），请你判断输入的这些边组成的结构是否是一棵树。

函数签名如下：

```java
boolean validTree(int n, int[][] edges);
```

比如输入如下：

```java
n = 5
edges = [[0,1], [0,2], [0,3], [1,4]]
```

这些边构成的是一棵树，算法应该返回 true：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261333728.png)

但如果输入：

```java
n = 5
edges = [[0,1],[1,2],[2,3],[1,3],[1,4]]
```

形成的就不是树结构了，因为包含环：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261334998.png)

**对于这道题，我们可以思考一下，什么情况下加入一条边会使得树变成图（出现环）**？

显然，像下面这样添加边会出现环：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261333737.png)

而这样添加边则不会出现环：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261334464.png)

总结一下规律就是：

**对于添加的这条边，如果该边的两个节点本来就在同一连通分量里，那么添加这条边会产生环；反之，如果该边的两个节点不在同一连通分量里，则添加这条边不会产生环**。

而判断两个节点是否连通（是否在同一个连通分量中）就是 Union-Find 算法的拿手绝活，所以这道题的解法代码如下：

```java
class Solution{
    boolean validTree(int n, int[][] edges){
        UF uf = new UF(n);
        for(int[] edge : edges){
            if(uf.connected(edge[0], edge[1]))
                return false;
            uf.union(edge[0], edge[1]);
        }
        // 题目要求形成一个合法的树，那么连通分量就不能大于1
        //if(uf.count()>1)
            //return false;
        //return true;
        return uf.count()==1;
    }
}

class UF{
    private int count;
    private int[] parent;
    public UF(int n){
        this.count = n;
        parent = new int[n];
        for(int i=0; i<n; i++)
            parent[i] = i;
    }
    
    public void union(int p, int q){
        int rootP = find(p);
        int rootQ = find(q);
        if(rootP == rootQ)
            return;
        parent[rootP] = rootQ;
        this.count--;
    }
    
    public boolean connected(int p, int q){
        int rootP = find(p);
        int rootQ = find(q);
        return rootP == rootQ;
    }
    
    public int find(int x){
        if(parent[x] != x)
            parent[x] = find(parent[x]);
        return parent[x];
    }
    
    public int count(){
        return this.count;
    }
}
```



## Kruskal算法

所谓最小生成树，就是图中若干边的集合（我们后文称这个集合为 `mst`，最小生成树的英文缩写），你要保证这些边：

1、包含图中的所有节点

2、形成的结构是树结构（即不存在环）

3、权重和最小

有之前题目的铺垫，前两条其实可以很容易地利用 Union-Find 算法做到，关键在于第 3 点，如何保证得到的这棵生成树是权重和最小的。

这里就用到了贪心思路：

**将所有边按照权重从小到大排序，从权重最小的边开始遍历，如果这条边和`mst`中的其它边不会形成环，则这条边是最小生成树的一部分，将它加入`mst`集合；否则，这条边不是最小生成树的一部分，不要把它加入`mst`集合**。

> info
>
> 注意理解这段话，首先需要讲明的是，图中是可以存在环的，但是生成的最小生成树一定不能包含环；其次，将权重按从小到大排序，从最小的权重开始遍历，如果这条边符合条件，那么说明这条边是最小生成树的一部分，只是说明是一部分，并未说明这条边是开头，结尾，还是中间的部分，只是强调是最小生成树的一部分。所以不要理解成非得从根节点开始遍历这颗树才算数。如果不是最小生成树的一部分，则跳过这条边，遍历下一条边。

这样，最后`mst`集合中的边就形成了最小生成树，下面我们看两道例题来运用一下 Kruskal 算法。

第一题是力扣第 [1135 题「最低成本联通所有城市」](https://leetcode.cn/problems/connecting-cities-with-minimum-cost/)，这是一道标准的最小生成树问题：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261353074.png)

```java
class Solution{
    public int minimumCost(int n, int[][] connections){
        UF uf = new UF(n);
        // 对所有的边按照权重大小从小到大排序
        Arrays.sort(connections, (a, b) -> (a[2] - b[2]));
        // 记录最小生成树的和
        int mst = 0;
        for(int[] edge : connections){
            int u = edge[0];
            int v = edge[1];
            int weight = edge[2];
            // 若这条边会产生环，则不能加入mst
            // 这道题的意思就是：图可以有环，但是树一定不能存在环
            if(uf.connected(u-1, v-1))
                continue;// 有环的话就跳过
            mst += weight;
            uf.union(u-1, v-1);
        }
        return uf.count()==1? mst : -1;
    }
}

class UF{
    private int count;
    private int[] parent;
    public UF(int n){
        this.count = n;
        parent = new int[n];
        for(int i=0; i<n; i++)
            parent[i] = i;
    }
    
    public void union(int p, int q){
        int rootP = find(p);
        int rootQ = find(q);
        if(rootP == rootQ)
            return;
        parent[rootP] = rootQ;
        this.count--;
    }
    
    public boolean connected(int p, int q){
        int rootP = find(p);
        int rootQ = find(q);
        return rootP == rootQ;
    }
    
    public int find(int x){
        if(parent[x] != x)
            parent[x] = find(parent[x]);
        return parent[x];
    }
    
    public int count(){
        return this.count;
    }
}
```

这道题就解决了，整体思路和上一道题非常类似，你可以认为树的判定算法加上按权重排序的逻辑就变成了 Kruskal 算法。

==总结：Kruskal最小生成树问题的解题思路==

1、使用并查集！！！使用并查集！！！使用并查集！！！

2、对带有权重的边进行排序，使用贪心算法！！！

3、用一个变量 `mst` 记录最小生成树的路径大小！！！



再来看看力扣第 [1584 题「连接所有点的最小费用」](https://leetcode.cn/problems/min-cost-to-connect-all-points/)：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261437896.png)

比如题目给的例子：

```
points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
```

算法应该返回 20，按如下方式连通各点：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307261438010.png)

很显然这也是一个标准的最小生成树问题：每个点就是无向加权图中的节点，边的权重就是曼哈顿距离，连接所有点的最小费用就是最小生成树的权重和。

所以解法思路就是先生成所有的边以及权重，然后对这些边执行 Kruskal 算法即可：

```java
class Solution{
    int minCostConnectPoints(int[][] points) {
        int n = points.length;
        List<int[]> edges = new ArrayList<>();
    	// 先形成边
        for(int i = 0; i<n; i++)
            for(int j=i+1; j<n; j++){
                int xi = points[i][0], yi = points[i][1];
                int xj = points[j][0], yj = points[j][1];
                // 注意，这个地方只是求边而已，并没有求邻接表
                edges.add(new int[]{
                    i, j, Math.abs(xi-xj)+Math.abs(yi-yj)
                });
            }
        // 将边按照权重大小从小到大排序
        Collections.sort(edges, (a, b)->(a[2] - b[2]));
        // 执行kruskal算法
        int mst = 0;
        UF uf = new UF(n);
        for(int[] edge : edges){
            int u = edge[0];
            int v = edge[1];
            int weight = edge[2];
            if(uf.connected(u, v))
                continue;
            mst += weight;
            uf.union(u, v);
        }
        return mst;
    }
}

class UF{
    private int count;
    private int[] parent;
    public UF(int n){
        this.count = n;
        parent = new int[n];
        for(int i=0; i<n; i++)
            parent[i] = i;
    }
    public void union(int p, int q){
        int rootP = find(p);
        int rootQ = find(q);
        if(rootP == rootQ)
            return;
        parent[rootP] = rootQ;
        this.count--;
    }
    public boolean connected(int p, int q){
        int rootP = find(p);
        int rootQ = find(q);
        return rootP == rootQ;
    }
    public int find(int x){
        if(parent[x] != x)
            parent[x] = find(parent[x]);
        return parent[x];
    }
    public int count(){
        return this.count;
    }
}
```

> info
>
> 注意：在这道题里，并不需要判断连通分量是否大于1，因为题目给的是点，一定可以找到一条路径和最小的边。

这道题做了一个小的变通：每个坐标点是一个二元组，那么按理说应该用五元组表示一条带权重的边，但这样的话不便执行 Union-Find 算法；所以我们用 `points` 数组中的索引代表每个坐标点，这样就可以直接复用之前的 Kruskal 算法逻辑了。

**通过以上三道算法题，相信你已经掌握了 Kruskal 算法，主要的难点是利用 Union-Find 并查集算法向最小生成树中添加边，配合排序的贪心思路，从而得到一棵权重之和最小的生成树。**

最后说下 Kruskal 算法的复杂度分析：

假设一幅图的节点个数为`V`，边的条数为`E`，首先需要`O(E)`的空间装所有边，而且 Union-Find 算法也需要`O(V)`的空间，所以 Kruskal 算法总的空间复杂度就是`O(V + E)`。

时间复杂度主要耗费在排序，需要`O(ElogE)`的时间，Union-Find 算法所有操作的复杂度都是`O(1)`，套一个 for 循环也不过是`O(E)`，所以总的时间复杂度为`O(ElogE)`。

