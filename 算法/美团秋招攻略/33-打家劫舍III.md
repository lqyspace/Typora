# 337-打家劫舍III

[337-打家劫舍III](https://leetcode.cn/problems/house-robber-iii/description/?envType=study-plan-v2&envId=meituan-2023-fall-sprint)，难度中等

![image-20230904160830621](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309041608685.png)

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
    public int rob(TreeNode root) {
        if(root==null) return 0;
        int[] res = dfs(root);
        return Math.max(res[0], res[1]);
    }

    public int[] dfs(TreeNode root){
        if(root==null) return new int[]{0, 0};

        // arr[0]表示不强
        // arr[1]表示抢
        int[] left = dfs(root.left);
        int[] right = dfs(root.right);

        // 偷，如果当前偷的话，那么子节点就不能偷了
        int do_it = root.val + left[0] + right[0];
        // 不偷,如果当前不偷的话，那么下一个子节点，可以选择偷还是不偷，也就是说可以根据偷或者不偷的大小进行判断，是否偷不偷
        int not_do = Math.max(left[0], left[1]) + Math.max(right[0], right[1]);
        return new int[]{not_do, do_it};
    }
}
```

解法2：

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
    HashMap<TreeNode, Integer> memo = new HashMap<>();
    public int rob(TreeNode root) {
        if(root==null) return 0;
        if(memo.containsKey(root))
            return memo.get(root);
        int not_do = rob(root.left) + rob(root.right);
        int do_it = root.val + (root.left==null? 0: rob(root.left.left) + rob(root.left.right)) + (root.right==null? 0: rob(root.right.left)+rob(root.right.right));
        memo.put(root, Math.max(not_do, do_it));
        return memo.get(root);
    }
}
```

