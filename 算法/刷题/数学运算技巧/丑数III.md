# 丑数III

力扣1201.[丑数III](https://leetcode.cn/problems/ugly-number-iii/)，难度中等

![image-20230616133439530](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306161334381.png)

首先，我们来仔细理解一下这道题的题意，这道题和前面的 [丑数II](https://leetcode.cn/problems/ugly-number-ii/submissions/) 这道题的题意有点区别：

[丑数II](https://leetcode.cn/problems/ugly-number-ii/submissions/) 这道题在定义丑数的时候，定义的质数只能是1,2,3,5这几个数，而本题在定义丑数的时候，只要能被 `a`，`b`，`c` 中的一个整除的数就是丑数，因此本题并不是特别难实现。

比如输入`n = 7, a = 3, b = 4, c = 5`，那么算法输出 10，因为符合条件的丑数序列为`3, 4, 5, 6, 8, 9, 10, ...`，其中第 7 个数字是 10。

有了之前几道题的铺垫，你肯定可以想到把`a, b, c`的倍数抽象成三条有序链表：

```java
1*3 -> 2*3 -> 3*3 -> 4*3 -> 5*3 -> 6*3 -> 7*3 ->...
1*4 -> 2*4 -> 3*4 -> 4*4 -> 5*4 -> 6*4 -> 7*4 ->...
1*5 -> 2*5 -> 3*5 -> 4*5 -> 5*5 -> 6*5 -> 7*5 ->...
```

然后将这三条链表合并成一条有序链表并去除重复元素，这样合并后的链表元素就是丑数序列，我们从中找到第`n`个元素即可：

```java
1*3 -> 1*4 -> 1*5 -> 2*3 -> 2*4 -> 3*3 -> 2*5 ->...
```

有了这个思路，可以直接写出代码：

```java
public int nthUglyNumber(int n, int a, int b, int c) {
    // 可以理解为三个有序链表的头结点的值
    // 由于数据规模较大，用 long 类型
    long productA = a, productB = b, productC = c;
    // 可以理解为合并之后的有序链表上的指针
    int p = 1;

    long min = -666;

    // 开始合并三个有序链表，获取第 n 个节点的值
    while (p <= n) {
        // 取三个链表的最小结点
        min = Math.min(Math.min(productA, productB), productC);
        p++;
        // 前进最小结点对应链表的指针
        if (min == productA) {
            productA += a;
        }
        if (min == productB) {
            productB += b;
        }
        if (min == productC) {
            productC += c;
        }
    }
    return (int) min;
}
```

这个思路应该是非常简单的，但是提交之后并不能通过所有测试用例，会超时。

注意题目给的数据范围非常大，`a, b, c, n`的大小可以达到 10^9，所以即便上述算法的时间复杂度是`O(n)`，也是相对比较耗时的，应该有更好的思路能够进一步降低时间复杂度。

**这道题的正确解法难度比较大，难点在于你要把一些数学知识和 [二分搜索技巧](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485044&idx=1&sn=e6b95782141c17abe206bfe2323a4226&scene=21#wechat_redirect) 结合起来才能高效解决这个问题**。

首先，我们可以定义一个单调递增的函数`f`：

`f(num, a, b, c)`计算`[1..num]`中，能够整除`a`或`b`或`c`的数字的个数，显然函数`f`的返回值是随着`num`的增加而增加的（单调递增）。

**题目让我们求第`n`个能够整除`a`或`b`或`c`的数字是什么，也就是说我们要找到一个最小的`num`，使得`f(num, a, b, c) == n`**。

这个**`num`**就是第`n`个能够整除`a`或`b`或`c`的数字。

根据 [二分查找的实际运用](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247496627&idx=1&sn=84d01f9f73566a7c686bc2be25350de0&scene=21#wechat_redirect) 给出的思路模板，我们得到一个单调函数`f`，想求参数`num`的最小值，就可以运用搜索左侧边界的二分查找算法了：

```java
int nthUglyNumber(int n, int a, int b, int c) {
    // 题目说本题结果在 [1, 2 * 10^9] 范围内，
    // 所以就按照这个范围初始化两端都闭的搜索区间
    int left = 1, right = (int) 2e9;
    // 搜索左侧边界的二分搜索
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (f(mid, a, b, c) < n) {
            // [1..mid] 中符合条件的元素个数不足 n，所以目标在右半边
            left = mid + 1;
        } else {
            // [1..mid] 中符合条件的元素个数大于 n，所以目标在左半边
            right = mid - 1;
        }
    }
    return left;
}

// 函数 f 是一个单调函数
// 计算 [1..num] 之间有多少个能够被 a 或 b 或 c 整除的数字
long f(int num, int a, int b, int c) {
    // 下文实现
}
```

搜索左侧边界的二分搜索代码模板在 [二分查找框架详解](https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485044&idx=1&sn=e6b95782141c17abe206bfe2323a4226&scene=21#wechat_redirect) 中讲过，没啥可说的，关键说一下函数`f`怎么实现，这里面涉及容斥原理以及最小公因数、最小公倍数的计算方法。

首先，我把`[1..num]`中能够整除`a`的数字归为集合`A`，能够整除`b`的数字归为集合`B`，能够整除`c`的数字归为集合`C`，那么`len(A) = num / a, len(B) = num / b, len(C) = num / c`，这个很好理解。

但是`f(num, a, b, c)`的值肯定不是`num / a + num / b + num / c`这么简单，因为你注意有些数字可能可以被`a, b, c`中的两个数或三个数同时整除，如下图：

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306191718373.jpeg)

**按照容斥原理，这个集合中的元素应该是：A + B + C - A ∩ B - A ∩ C - B ∩ C + A ∩ B ∩ C**。结合上图应该很好理解。

问题来了，`A, B, C`三个集合的元素个数我们已经算出来了，但如何计算像`A ∩ B`这种交集的元素个数呢？

其实也很容易想明白**，`A ∩ B`的元素个数就是`num / lcm(a, b)`，其中`lcm`是计算最小公倍数（Least Common Multiple）的函数**。

类似的，`A ∩ B ∩ C`的元素个数就是`num / lcm(lcm(a, b), c)`的值。

现在的问题是，最小公倍数怎么求？

直接记住定理吧**：`lcm(a, b) = a \* b / gcd(a, b)`，其中`gcd`是计算最大公因数（Greatest Common Divisor）的函数**。

现在的问题是，最大公因数怎么求？这应该是经典算法了，我们一般叫辗转相除算法（或者欧几里得算法）。

好了，套娃终于套完了，我们可以把上述思路翻译成代码就可以实现`f`函数，注意本题数据规模比较大，有时候需要用`long`类型防止`int`溢出：

```java
// 计算最大公因数（辗转相除/欧几里得算法）
long gcd(long a, long b) {
    if (a < b) {
        // 保证 a > b
        return gcd(b, a);
    }
    if (b == 0) {
        return a;
    }
    return gcd(b, a % b);
}

// 最小公倍数
long lcm(long a, long b) {
    // 最小公倍数就是乘积除以最大公因数
    return a * b / gcd(a, b);
}

// 计算 [1..num] 之间有多少个能够被 a 或 b 或 c 整除的数字
long f(int num, int a, int b, int c) {
    long setA = num / a, setB = num / b, setC = num / c;
    long setAB = num / lcm(a, b);
    long setAC = num / lcm(a, c);
    long setBC = num / lcm(b, c);
    long setABC = num / lcm(lcm(a, b), c);
    // 集合论定理：A + B + C - A ∩ B - A ∩ C - B ∩ C + A ∩ B ∩ C
    return setA + setB + setC - setAB - setAC - setBC + setABC;
}
```

实现了`f`函数，结合之前的二分搜索模板，时间复杂度下降到对数级别，即可高效解决这道题目了。



































