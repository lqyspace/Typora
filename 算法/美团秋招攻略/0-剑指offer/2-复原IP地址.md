# 93-复原IP地址

[93-复原IP地址](https://leetcode.cn/problems/restore-ip-addresses/description/?envType=study-plan-v2&envId=meituan-2023-fall-sprint)，难度中等

![image-20230826012057131](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308260120207.png)

解题思路：回溯算法

```java
class Solution {
    List<String> res = new LinkedList<>();
    List<String> trace = new LinkedList<>();
    public List<String> restoreIpAddresses(String s) {
		backtrace(s, 0);
        return res;
    }
    public void backtrace(String s, int start){
        //base case
        if(trace.size()==4 && start==s.length()){
            res.add(String.join(".", trace));
        }
        if(trace.size()>=4) return;

        
        for(int i=start+1; i<=start+3 && i<=s.length(); i++){
            if(s.charAt(start)=='0' && i-start>=2){
                continue;
            }
            String sub = s.substring(start, i);
            if(Integer.parseInt(sub)>255)
                continue;

            trace.add(sub);
            backtrace(s, i);
            trace.remove(trace.size()-1);
        }
    }
}
```

题解代码：

```java
class Solution {
    List<String> res = new LinkedList<>();
    LinkedList<String> track = new LinkedList<>();

    public List<String> restoreIpAddresses(String s) {
        backtrack(s, 0);
        return res;
    }

    // 回溯算法框架
    void backtrack(String s, int start) {
        if (start == s.length() && track.size() == 4) {
            // base case，走到叶子节点
            // 即整个 s 被成功分割为合法的四部分，记下答案
            res.add(String.join(".", track));
        }
        for (int i = start; i < s.length(); i++) {
            if (!isValid(s, start, i)) {
                // s[start..i] 不是合法的 ip 数字，不能分割
                continue;
            }
            if (track.size() >= 4) {
                // 已经分解成 4 部分了，不能再分解了
                break;
            }
            // s[start..i] 是一个合法的 ip 数字，可以进行分割
            // 做选择，把 s[start..i] 放入路径列表中
            track.addLast(s.substring(start, i + 1));
            // 进入回溯树的下一层，继续切分 s[i+1..]
            backtrack(s, i + 1);
            // 撤销选择
            track.removeLast();
        }
    }

    // 判断 s[
    boolean isValid(String s, int start, int end) {
        int length = end - start + 1;

        if (length == 0 || length > 3) {
            return false;
        }

        if (length == 1) {
            // 如果只有一位数字，肯定是合法的
            return true;
        }

        if (s.charAt(start) == '0') {
            // 多于一位数字，但开头是 0，肯定不合法
            return false;
        }

        if (length <= 2) {
            // 排除了开头是 0 的情况，那么如果是两位数，怎么着都是合法的
            return true;
        }

        // 现在输入的一定是三位数
        if (Integer.parseInt(s.substring(start, start + length)) > 255) {
            // 不可能大于 255
            return false;
        } else {
            return true;
        }
    }
}
```

