[TOC]

# Base Case和备忘录初始值怎么确定

看下力扣第 931 题「[下降路径最小和](https://leetcode.cn/problems/minimum-falling-path-sum/)」，输入为一个 `n * n` 的二维数组 `matrix`，请你计算从第一行落到最后一行，经过的路径和最小为多少。

函数签名如下：

```java
int minFallingPathSum(int[][] matrix);
```

就是说你可以站在 `matrix` 的第一行的任意一个元素，需要下降到最后一行。

每次下降，可以向下、向左下、向右下三个方向移动一格。也就是说，可以从 `matrix[i][j]` 降到 `matrix[i+1][j]` 或 `matrix[i+1][j-1]` 或 `matrix[i+1][j+1]` 三个位置。

请你计算下降的「最小路径和」，比如说题目给了一个例子：

![img](https://labuladong.github.io/algo/images/备忘录基础/title.jpg)

我们前文写过两道「路径和」相关的文章：[动态规划之最小路径和](https://labuladong.github.io/algo/di-er-zhan-a01c6/yong-dong--63ceb/dong-tai-g-52f34/) 和 [用动态规划算法通关魔塔](https://labuladong.github.io/algo/di-er-zhan-a01c6/yong-dong--63ceb/dong-tai-g-f43a3/)。

今天这道题也是类似的，不算是困难的题目，所以**我们借这道题来讲讲 base case 的返回值、备忘录的初始值、索引越界情况的返回值如何确定**。

不过还是要通过 [动态规划的标准套路](https://labuladong.github.io/algo/di-ling-zh-bfe1b/dong-tai-g-1e688/) 介绍一下这道题的解题思路，首先我们可以定义一个 `dp` 数组：

```java
int dp(int[][] matrix, int i, int j);
```

这个 `dp` 函数的含义如下：

**从第一行（`matrix[0][..]`）向下落，落到位置 `matrix[i][j]` 的最小路径和为 `dp(matrix, i, j)`**。

根据这个定义，我们可以把主函数的逻辑写出来：

```java
int minFallingPathSum(int[][] matrix) {
    int n = matrix.length;
    int res = Integer.MAX_VALUE;

    // 终点可能在最后一行的任意一列
    for (int j = 0; j < n; j++) {
        res = Math.min(res, dp(matrix, n - 1, j));
    }

    return res;
}
```

因为我们可能落到最后一行的任意一列，所以要穷举一下，看看落到哪一列才能得到最小的路径和。

接下来看看 `dp` 函数如何实现。

对于 `matrix[i][j]`，只有可能从 `matrix[i-1][j], matrix[i-1][j-1], matrix[i-1][j+1]` 这三个位置转移过来。

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308041408944.jpeg)

**那么，只要知道到达 `(i-1, j), (i-1, j-1), (i-1, j+1)` 这三个位置的最小路径和，加上 `matrix[i][j]` 的值，就能够计算出来到达位置 `(i, j)` 的最小路径和**：

```java
int dp(int[][] matrix, int i, int j) {
    // 非法索引检查
    if (i < 0 || j < 0 ||
        i >= matrix.length ||
        j >= matrix[0].length) {
        // 返回一个特殊值
        return 100000;
    }
    // base case
    if (i == 0) {
        return matrix[i][j];
    }
    // 状态转移
    return matrix[i][j] + min(
            dp(matrix, i - 1, j), 
            dp(matrix, i - 1, j - 1),
            dp(matrix, i - 1, j + 1)
        );
}

int min(int a, int b, int c) {
    return Math.min(a, Math.min(b, c));
}
```

当然，上述代码是暴力穷举解法，我们可以用备忘录的方法消除重叠子问题，完整代码如下：

```java
class Solution {
    int minFallingPathSum(int[][] matrix) {
        int n = matrix.length;
        int res = Integer.MAX_VALUE;
        // 备忘录里的值初始化为 66666
        memo = new int[n][n];
        for (int i = 0; i < n; i++) {
            Arrays.fill(memo[i], 100000);
        }
        // 终点可能在 matrix[n-1] 的任意一列
        for (int j = 0; j < n; j++) {
            res = Math.min(res, dp(matrix, n - 1, j));
        }
        return res;
    }

    // 备忘录
    int[][] memo;

    int dp(int[][] matrix, int i, int j) {
        // 1、索引合法性检查
        if (i < 0 || j < 0 ||
            i >= matrix.length ||
            j >= matrix[0].length) {
            
            return 100000;
        }
        // 2、base case
        if (i == 0) {
            return matrix[i][j];
        }
        // 3、查找备忘录，防止重复计算
        if (memo[i][j] != 100000) {
            return memo[i][j];
        }
        // 进行状态转移
        memo[i][j] = matrix[i][j] + min(
                dp(matrix, i - 1, j), 
                dp(matrix, i - 1, j - 1),
                dp(matrix, i - 1, j + 1)
            );
        return memo[i][j];
    }

    int min(int a, int b, int c) {
        return Math.min(a, Math.min(b, c));
    }
}
```

动态规划的迭代解法：

```java
class Solution {
    public int minFallingPathSum(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int[][] dp = new int[m][n];// 以(i, j)结尾的最小下降和
        
        for(int i=0; i<m; i++){
            for(int j=0; j<n; j++){
                // 寻找(i, j)上方临近的最小点
                if(i==0) {
                    dp[i][j] = matrix[i][j];
                    continue;
                }
                int min = Integer.MAX_VALUE;
                if(j-1>=0)
                    min = Math.min(min, dp[i-1][j-1]);
                if(j<n-1)
                    min = Math.min(min, dp[i-1][j+1]);
                min = Math.min(min, dp[i-1][j]);
                dp[i][j] = min + matrix[i][j];
            }
        }
        int res = Integer.MAX_VALUE;
        for(int j=0; j<n; j++)
            res = Math.min(res, dp[m-1][j]);
        return res;
    }
}
```

**拓展延伸一下，建议大家做题时，除了题意本身，一定不要忽视题目给定的其他信息**。

本文举的例子，测试用例数据范围可以确定「什么是特殊值」，从而帮助我们将思路转化成代码。

除此之外，数据范围还可以帮我们估算算法的时间/空间复杂度。

比如说，有的算法题，你只想到一个暴力求解思路，时间复杂度比较高。如果发现题目给定的数据量比较大，那么肯定可以说明这个求解思路有问题或者存在优化的空间。

除了数据范围，有时候题目还会限制我们算法的时间复杂度，这种信息其实也暗示着一些东西。

比如要求我们的算法复杂度是 `O(NlogN)`，你想想怎么才能搞出一个对数级别的复杂度呢？

肯定得用到 [二分搜索](https://labuladong.github.io/algo/di-ling-zh-bfe1b/wo-xie-le--3c789/) 或者二叉树相关的数据结构，比如 `TreeMap`，`PriorityQueue` 之类的对吧。

再比如，有时候题目要求你的算法时间复杂度是 `O(MN)`，这可以联想到什么？

可以大胆猜测，常规解法是用 [回溯算法](https://labuladong.github.io/algo/di-ling-zh-bfe1b/hui-su-sua-c26da/) 暴力穷举，但是更好的解法是动态规划，而且是一个二维动态规划，需要一个 `M * N` 的二维 `dp` 数组，所以产生了这样一个时间复杂度。

如果你早就胸有成竹了，那就当我没说，毕竟猜测也不一定准确；但如果你本来就没啥解题思路，那有了这些推测之后，最起码可以给你的思路一些方向吧？

总之，多动脑筋，不放过任何蛛丝马迹，你不成为刷题小能手才怪。