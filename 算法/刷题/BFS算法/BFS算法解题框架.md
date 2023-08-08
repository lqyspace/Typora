# BFS解题框架

## 前言

BFS的核心思想：就是把一些问题抽象成图，从一个点开始，向四周开始扩散。一般来说，我们写BFS算法都是用对列这种数据结构，每次将一个点周围的所有节点加入队列。

**BFS相对于DFS最主要的区别是：BFS找到的一定是最短路径，但代价就是空间复杂度可能比DFS要大很多。**



## 一、算法框架

要说框架的话，我们先举例一个BFS出现的常见场景吧。**问题的本质就是让你在一幅图中找到从起点 `start`到终点 `target`的最近距离，这个例子听起来很枯燥，但是 `BFS`算法问题其实都是在干这件事儿。  **

这个广义的描述可以有各种变体，比如走迷宫，有的格子是围墙不能走，从起点到终点的最短距离是多少？如果这个迷宫带[传送门]可以瞬间传送呢？

再比如说，两个单词，要求你通过某些替换，把其中一个变成另一个，每次只能替换一个字符，最少要替换几次？

再比如说连连看游戏，两个方块消除的条件不仅仅是图案相同，还得保证两个方块之间的最短连线不能多于两个拐点。你玩连连看，点击两个坐标，游戏是如何判断它两的最短连线有几个拐点？

再比如......

净整这些花里胡哨的，本质上看这些问题都没啥区别，就是一幅图，让你从一个起点走到终点，问最短路径。这就是BFS的本质，框架搞清楚了直接默写就行。

下面是一个框架：

```java
// 计算从起点start到终点target的最近距离
int bfs(Node start, Node target){
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路
    
    q.offer(start); // 将起点加入队列
    visited.add(start);
    while(q not empty){
        int qs = q.size();
        // 将当前队列中的所有节点向四周扩散
        for(int i=0; i<qs; i++){
            Node cur = q.poll();
            // 划重点：判断是否到达终点
            if(cur is target)
                return step;
            // 将cur的相邻节点加入队列
            for(Node x: cur.obj()){
                if(x not in visited){
                    q.offer(x);
                    visited.add(x);
                }
            }
        }
    }
    // 如果走到这里，说明在图中没有找到目标节点
}
```

队列 `q`就不说了，BFS的核心框架结构；`cur.abj()`泛指 `cur`相邻的节，比如说二维数组中，`cur`上下左右四面的位置就是相邻节点；`visited`的主要作用就是防止走回头路，大部分的时候都是必须的，但是像一般的二叉树结构，没有子节点到父节点的指针，不会走回头路就不需要 `visited`。



## 二、二叉树的最小高度

先来个简单的问题实践一下 BFS 框架吧，判断一棵二叉树的**最小**高度，这也是力扣第 111 题「[二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)」：

![image-20230719100201660](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307191002728.png)

这道题还是很简单的，只需要找到离根节点最近的叶子结点就行。

```java
if (cur.left == null && cur.right == null) 
    // 到达叶子节点
```

直接上代码：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) return 0;

        int count = 1;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while(!q.isEmpty()){
            int qs = q.size();
            for(int i=0; i<qs; i++){
                TreeNode cur = q.poll();
                if(cur.left==null && cur.right==null)
                    return count;
                if(cur.left!=null)
                    q.offer(cur.left);
                if(cur.right!=null)
                    q.offer(cur.right);
            }
            count++;
        }
        return count;
    }
}
```

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307191004075.jpeg)

这里注意这个 `while`循环和 `for`的配合，**`while`循环控制一层一层往下走，`for`循环利用 `qs`变量控制从左到右遍历每一层二叉树的节点。**

这一点很重要，这个形式在普通 BFS 问题中都很常见，但是在 [Dijkstra 算法模板框架](https://labuladong.github.io/algo/di-yi-zhan-da78c/shou-ba-sh-03a72/dijkstra-s-6d0b2/) 中我们修改了这种代码模式，读完并理解本文后你可以去看看 BFS 算法是如何演变成 Dijkstra 算法在加权图中寻找最短路径的。

话说回来，二叉树本身是很简单的数据结构，我想上述代码你应该可以理解的，其实其他复杂问题都是这个框架的变形，再探讨复杂问题之前，我们解答两个问题：

**1、为什么 BFS 可以找到最短距离，DFS 不行吗**？

首先，你看 BFS 的逻辑，`depth` 每增加一次，队列中的所有节点都向前迈一步，这保证了第一次到达终点的时候，走的步数是最少的。

DFS 不能找最短路径吗？其实也是可以的，但是时间复杂度相对高很多。你想啊，DFS 实际上是靠递归的堆栈记录走过的路径，你要找到最短路径，肯定得把二叉树中所有树杈都探索完才能对比出最短的路径有多长对不对？而 BFS 借助队列做到一次一步「齐头并进」，是可以在不遍历完整棵树的条件下找到最短距离的。

形象点说，DFS 是线，BFS 是面；DFS 是单打独斗，BFS 是集体行动。这个应该比较容易理解吧。

**2、既然 BFS 那么好，为啥 DFS 还要存在**？

BFS 可以找到最短距离，但是空间复杂度高，而 DFS 的空间复杂度较低。

还是拿刚才我们处理二叉树问题的例子，假设给你的这个二叉树是满二叉树，节点数为 `N`，对于 DFS 算法来说，空间复杂度无非就是递归堆栈，最坏情况下顶多就是树的高度，也就是 `O(logN)`。

但是你想想 BFS 算法，队列中每次都会储存着二叉树一层的节点，这样的话最坏情况下空间复杂度应该是树的最底层节点的数量，也就是 `N/2`，用 Big O 表示的话也就是 `O(N)`。

由此观之，BFS 还是有代价的，一般来说在找最短路径的时候使用 BFS，其他时候还是 DFS 使用得多一些（主要是递归代码好写）。

BFS时间复杂度相对较低，而DFS空间复杂度相对较低。



## 三、解开密码锁的次数

这是力扣第 752 题「[打开转盘锁](https://leetcode.cn/problems/open-the-lock/)」，比较有意思：

![image-20230719101937147](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307191019222.png)

> 我自己的思路：
>
> 题目有四个拨轮，我们现在只考虑某一个拨轮的变化，该拨轮只能有两个变化的方向，那就是向上转和向下转两种可能。因此对于一串数字而言，每次只能拨动一个拨轮的一个数字，每个拨动有两种可能，那四个拨轮就有8种可能，也就是说，我将一个数字变成另一个数字有8种可能。
>
> 那我就需要探讨这个8种可能的数字，哪些是死亡数字，哪些是目标数字，哪些是可以加入到队列中的数字。
>
> 因此本题仍然使用**队列是否为空**作为外部循环的终止条件，内部循环则需要遍历每一个节点（也就是每一个数字）的8种可能。
>
> 可以把它想象成一幅图，我们每次需要遍历一个节点的相邻节点，把相邻节点加入队列中等待下一次的遍历；也可以想象成一个多叉树，每一串数字是一个节点，每一个节点的每一位数字都会衍生出两个子节点。因此本题可以使用BFS框架来解题。

题目中描述的就是我们生活中常见的那种密码锁，如果没有任何约束，最少的拨动次数很好算，就像我们平时开密码锁那样直奔密码拨就行了。

但现在的难点就在于，不能出现 `deadends`，应该如何计算出最少的转动次数呢？

**第一步，我们不管所有的限制条件，不管 `deadends` 和 `target` 的限制，就思考一个问题：如果让你设计一个算法，穷举所有可能的密码组合，你怎么做**？

穷举呗，再简单一点，如果你只转一下锁，有几种可能？总共有 4 个位置，每个位置可以向上转，也可以向下转，也就是有 8 种可能对吧。

比如说从 `"0000"` 开始，转一次，可以穷举出 `"1000", "9000", "0100", "0900"...` 共 8 种密码。然后，再以这 8 种密码作为基础，对每个密码再转一下，穷举出所有可能...

**仔细想想，这就可以抽象成一幅图，每个节点有 8 个相邻的节点**，又让你求最短距离，这不就是典型的 BFS 嘛，框架就可以派上用场了，先写出一个「简陋」的 BFS 框架代码再说别的：

```java
// 将 s[j] 向上拨动一次
String plusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '9')
        ch[j] = '0';
    else
        ch[j] += 1;
    return new String(ch);
}
// 将 s[i] 向下拨动一次
String minusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '0')
        ch[j] = '9';
    else
        ch[j] -= 1;
    return new String(ch);
}

// BFS 框架，打印出所有可能的密码
void BFS(String target) {
    Queue<String> q = new LinkedList<>();
    q.offer("0000");
    
    while (!q.isEmpty()) {
        int sz = q.size();
        /* 将当前队列中的所有节点向周围扩散 */
        for (int i = 0; i < sz; i++) {
            String cur = q.poll();
            /* 判断是否到达终点 */
            System.out.println(cur);

            /* 将一个节点的相邻节点加入队列 */
            for (int j = 0; j < 4; j++) {
                String up = plusOne(cur, j);
                String down = minusOne(cur, j);
                q.offer(up);
                q.offer(down);
            }
        }
        /* 在这里增加步数 */
    }
    return;
}
```

> PS：这段代码当然有很多问题，但是我们做算法题肯定不是一蹴而就的，而是从简陋到完美的。不要完美主义，咱要慢慢来，好不。

**这段 BFS 代码已经能够穷举所有可能的密码组合了，但是显然不能完成题目，有如下问题需要解决**：

1、会走回头路。比如说我们从 `"0000"` 拨到 `"1000"`，但是等从队列拿出 `"1000"` 时，还会拨出一个 `"0000"`，这样的话会产生死循环。

2、没有终止条件，按照题目要求，我们找到 `target` 就应该结束并返回拨动的次数。

3、没有对 `deadends` 的处理，按道理这些「死亡密码」是不能出现的，也就是说你遇到这些密码的时候需要跳过。

如果你能够看懂上面那段代码，真得给你鼓掌，只要按照 BFS 框架在对应的位置稍作修改即可修复这些问题：

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        // 记录需要跳过的死亡数字
		 Set<String> deadendsets = new HashSet<>();
        for(String nus : deadends) deadendsets.add(nus);
        // 记录一个数字之前是不是已经访问过
        Set<String> visited = new HashSet<>();
        // 记录拨动的次数
        int count = 0;
        // 使用结果对列
        Queue<String> q = new LinkedList<>();
        q.offer("0000");
        while(!q.isEmpty()){
            int qs = q.size();
            for(int i=0; i<qs; i++){
                String cur = q.poll();
                if(deadendsets.contains(cur))
                    continue;
                if(cur.equals(target))
                    return count;
                // 将一个未遍历的节点加入队列中
                for(int j=0; j<4; j++){
                    //向上拨动
                    String ups = upOne(cur, j);
                    if(!visited.contains(ups)){
                        q.offer(ups);
                        visited.add(ups);
                    }
                    
                    // 向下拨动
                    String downs = downOne(cur, j);
                    if(!visited.contains(downs)){
                        q.offer(downs);
                        visited.add(downs);
                    }
                }
            }
            count++;
        }
        return count;
    }
    
    // 向上拨动
    public String upOne(String num, int i){
        char[] chs = num.toCharArray();
        if(chs[i] == '9')
            chs[i] = '0';
        else
            chs[i] += 1;
        return new String(chs);
    }
    
    // 向下拨动
    public String downOne(String num, int i){
        char[] chs = num.toCharArray();
        if(chs[i] == '0')
            chs[i] = '9';
        else
            chs[i] -= 1;
        return new String(chs);
    }
}
```

至此，我们就解决了这道题目，有一个比较小的优化，可以不需要 `deadendsets`这个哈希集合，可以直接将这些元素初始化到 `visited`集合中，效果是一样的，可能更加优雅一些。



## 四、双向 BFS 优化

你以为到这里 BFS 算法就结束了？恰恰相反。BFS 算法还有一种稍微高级一点的优化思路：**双向 BFS**，可以进一步提高算法的效率。

篇幅所限，这里就提一下区别：**传统的 BFS 框架就是从起点开始向四周扩散，遇到终点时停止；而双向 BFS 则是从起点和终点同时开始扩散，当两边有交集的时候停止**。

为什么这样能够能够提升效率呢？其实从 Big O 表示法分析算法复杂度的话，它俩的最坏复杂度都是 `O(N)`，但是实际上双向 BFS 确实会快一些，我给你画两张图看一眼就明白了：


![img](https://labuladong.github.io/algo/images/BFS/1.jpeg)

![img](https://labuladong.github.io/algo/images/BFS/2.jpeg)

图示中的树形结构，如果终点在最底部，按照传统 BFS 算法的策略，会把整棵树的节点都搜索一遍，最后找到 `target`；而双向 BFS 其实只遍历了半棵树就出现了交集，也就是找到了最短距离。从这个例子可以直观地感受到，双向 BFS 是要比传统 BFS 高效的。

**不过，双向 BFS 也有局限，因为你必须知道终点在哪里**。比如我们刚才讨论的二叉树最小高度的问题，你一开始根本就不知道终点在哪里，也就无法使用双向 BFS；但是第二个密码锁的问题，是可以使用双向 BFS 算法来提高效率的，代码稍加修改即可：

```java
int openLock(String[] deadends, String target) {
    Set<String> deads = new HashSet<>();
    for(String s: deadends) deads.add(s);
    Set<String> visited = new HashSet<>();
    int steps = 0;
    Set<String> q1 = new HashSet<>();
    Set<String> q2 = new HashSet<>();
    q1.offer("0000");
    q2.offer(target);
    
    while(!q1.isEmpty() && !q2.isEmpty()){
        // 哈希集合在遍历的过程中不能修改，用temp存储扩充结果
        Set<String> temp = new HashSet<>();
        // 把q1中的所有点向周围扩散
        for(String cur: q1){
            if(deads.contains(cur))
                continue;
            if(q2.contains(cur))
                return steps;
            
            visited.add(cur);
            
            // 将一个节点未遍历的节点加入集合
            for(int j=0; j<4; j++){
                //向上拨动
                String up = upOne(cur, j);
                if(!visited.contains(up))
                    temp.add(up);
                // 向下拨动
                String down = downOne(cur, j);
                if(!visited.contains(down))
                    temp.add(down);
            }
        }
        // 增加步数
        steps++;
        // 下次把q2作为q1进行扩散
        q1 = q2;
        q2 = temp;
    }
    return -1;
}
```

双向 BFS 还是遵循 BFS 算法框架的，只是**不再使用队列，而是使用 HashSet 方便快速判断两个集合是否有交集**。

另外的一个技巧点就是 **while 循环的最后交换 `q1` 和 `q2` 的内容**，所以只要默认扩散 `q1` 就相当于轮流扩散 `q1` 和 `q2`。

其实双向 BFS 还有一个优化，就是在 while 循环开始时做一个判断：

```java
// ...
while (!q1.isEmpty() && !q2.isEmpty()) {
    if (q1.size() > q2.size()) {
        // 交换 q1 和 q2
        temp = q1;
        q1 = q2;
        q2 = temp;
    }
    // ...
```

为什么这是一个优化呢？

因为按照 BFS 的逻辑，队列（集合）中的元素越多，扩散之后新的队列（集合）中的元素就越多；在双向 BFS 算法中，如果我们每次都选择一个较小的集合进行扩散，那么占用的空间增长速度就会慢一些，效率就会高一些。

不过话说回来，**无论传统 BFS 还是双向 BFS，无论做不做优化，从 Big O 衡量标准来看，时间复杂度都是一样的**，只能说双向 BFS 是一种 trick，算法运行的速度会相对快一点，掌握不掌握其实都无所谓。最关键的是把 BFS 通用框架记下来，反正所有 BFS 算法都可以用它套出解法。













