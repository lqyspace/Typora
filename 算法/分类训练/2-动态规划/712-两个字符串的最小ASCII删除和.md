# 712-两个字符串的最小ASCII码删除和

[712-两个字符串的最小ASCII码删除和](https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/)，难度中等

![image-20231010214930873](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310102149933.png)

我还在想怎么收集那些最长的公共子序列，题解就已经把最长公共子序列的解法运用的淋漓尽致。

题解1：递归+备忘录

```java
class Solution {
    int[][] memo;
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        memo = new int[m][n];
        for(int[] row: memo){
            Arrays.fill(row, -1);
        }
        return dfs(s1, 0, s2, 0);
    }
    // 收集s[i...]和t[j...]相同字符串的最小删除和
    public int dfs(String s, int i, String t, int j){
        int res = 0;
        if(i==s.length()){
            // 说明s已经遍历完，所以t剩下的字符都得删除
            while(j<t.length()){
                res += t.charAt(j);
                j++;
            }
            return res;
        }
        if(j==t.length()){
            while(i<s.length()){
                res += s.charAt(i);
                i++;
            }
            return res;
        }

        if(memo[i][j]!=-1)
            return memo[i][j];

        if(s.charAt(i)==t.charAt(j)){
            memo[i][j] = dfs(s, i+1, t, j+1);
        }else{
            memo[i][j] = Math.min(dfs(s, i+1, t, j)+s.charAt(i), dfs(s, i, t, j+1)+t.charAt(j));
        }
        return memo[i][j];
    }
}
```



题解2：递归+备忘录

```java
class Solution {
    int[][] memo;
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        memo = new int[m][n];
        for(int[] row: memo){
            Arrays.fill(row, -1);
        }
        return dfs(s1, m-1, s2, n-1);
    }
    // 收集s[0...i]和t[0...j]相同字符串的最小删除和
    public int dfs(String s, int i, String t, int j){
        int res = 0;
        if(i==-1){
            // 说明s已经遍历完，所以t剩下的字符都得删除
            while(j>=0){
                res += t.charAt(j);
                j--;
            }
            return res;
        }
        if(j==-1){
            while(i>=0){
                res += s.charAt(i);
                i--;
            }
            return res;
        }

        if(memo[i][j]!=-1)
            return memo[i][j];

        if(s.charAt(i)==t.charAt(j)){
            memo[i][j] = dfs(s, i-1, t, j-1);
        }else{
            memo[i][j] = Math.min(dfs(s, i-1, t, j)+s.charAt(i), dfs(s, i, t, j-1)+t.charAt(j));
        }
        return memo[i][j];
    }
}
```



题解3：动态规划

```java
class Solution {
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[m+1][n+1];// 表示s1[0...i], s2[0...j]最少删除
        for(int i=1; i<=m; i++){
            dp[i][0] = s1.charAt(i-1) + dp[i-1][0];
        }
        for(int j=1; j<=n; j++){
            dp[0][j] = s2.charAt(j-1) + dp[0][j-1];
        }

        for(int i=1; i<=m; i++){
            for(int j=1; j<=n; j++){
                if(s1.charAt(i-1)==s2.charAt(j-1)){
                    dp[i][j] = dp[i-1][j-1];
                }else{
                    dp[i][j] = Math.min(dp[i-1][j]+s1.charAt(i-1), dp[i][j-1]+s2.charAt(j-1));
                }
            }
        }
        return dp[m][n];
    }
}
```

