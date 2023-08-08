# 和等于k的最长子数组长度

[力扣325](https://leetcode.cn/problems/maximum-size-subarray-sum-equals-k/?show=1)，难度中等

![image-20230714170611494](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307141706549.png)

```java
class Solution {
    ArrayList<Integer> preSum = new ArrayList<>();
    public int maxSubArrayLen(int[] nums, int k) {
        int n=nums.length;
        int res = Integer.MIN_VALUE;
        int index = -1;
        preSum.add(0);
        for(int i = 1; i<=n; i++)
            preSum.add(preSum.get(i-1) + nums[i-1]);
        for(int i=0; i<preSum.size(); i++){
            if(preSum.contains(preSum.get(i)+k)){
                index = preSum.lastIndexOf(preSum.get(i)+k);
                // 需要注意：确保 index-i为正数，0也不可以
                int tmp = index - i <= 0? 0: index - i;
                res = Math.max(res, tmp);
            }
        }
        return res==Integer.MIN_VALUE? 0: res;
    }
}
```

本代码的思路是：**先计算出前缀和，然后利用前缀和，找出差值为k的(i, j)坐标**。

但是不幸的是，超时！！！因为题目中有两道循环，虽然时间复杂度是 `O(n)`，但是 `nums.length`的范围在 `[1, 2e5]`内，因此花的时间还是很大的。

看了别人的题解，代码如下：

```java
class Solution {
    public int maxSubArrayLen(int[] nums, int k) {
		int n = nums.length;
        int preSum = 0;
        // 为了记录值第一次出现的位置
        HashMap<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);// 0索引在数组之外，所以记录为-1,索引相减的时候也可以的得到正确值
        int res = 0;
        for(int i=0; i<n; i++){
            preSum += nums[i];
            
            map.putIfAbsent(preSum, i);
            int need = preSum - k;
            if(map.containsKey(need)){
                res = Math.max(res, i - map.get(need));
            }
        }
        return res;
    }
}
```

两个代码后面都利用相减的值判断是否存在的条件，只不过我的那个代码多一个一个循环，因此会造成不必要的时间浪费。





**滑动窗口的解法：**

```java
class Solution {
    public int maxSubArrayLen(int[] nums, int k) {
		int left = 0, right = 0;
        int res = -1;
        int winSum = 0;
        while(right<nums.length){
            winSum += nums[right++];
            
            while(winSum>k && left <= right){
                preSum -= nums[left++];
            }
            if(winSum == k)
                res = Math.max(res, right - left + 1);
        }
        return res==-1? 0: res;
    }
}
```

这个解法是有问题的，比如有这样的一个实例：

```
[1,-1,5,-2,3]
3
```

当 `right==3`时，此时的 `winSum==5>3`，因此会开始缩小窗口，很明显这是不对的，经过内层循环，`winSum`又会变为0，因此，用滑动窗口是不对的。