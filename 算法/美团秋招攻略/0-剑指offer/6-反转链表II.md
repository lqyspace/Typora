# 92-反转链表II

[92-反转链表](https://leetcode.cn/problems/reverse-linked-list-ii/?envType=study-plan-v2&envId=meituan-2023-fall-sprint)，难度中等

![image-20230828180311613](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308281803673.png)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    ListNode successor = null;
    public ListNode reverseBetween(ListNode head, int left, int right) {
		if(head == null || head.next == null) return head;
        return dfs(head, left, right);
    }
    
    public ListNode reverseN(ListNode head, int n){
        if(head==null || head.next==null) return head;
        if(n==1){
            successor = head.next;
            return head;
        }
        ListNode last = reverseN(head.next, n-1);
        head.next.next = head;
        head.next = successor;
        return last;
    }
    public ListNode dfs(ListNode head, int m, int n){
        if(m==1)
            return reverseN(head, n);
        head.next = dfs(head.next, m-1, n-1);
        return head;
    }
}
```

历史所有解法：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    ListNode successor = null;
    public ListNode reverseBetween(ListNode head, int left, int right) {
		if(head == null || head.next == null) return head;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode p = dummy;
        int i=0;
        while(i<left-1 && p!=null){
            p = p.next;
            i++;
        }
        p.next = reverseN(p.next, right-left+1);
        return dummy.next;
    }
    
    public ListNode reverseN(ListNode head, int n){
        if(head==null || head.next==null) return head;
        if(n==1){
            successor = head.next;
            return head;
        }
        ListNode last = reverseN(head.next, n-1);
        head.next.next = head;
        head.next = successor;
        return last;
    }
}
```

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
		if(head==null) return head;
        ListNode dummy = new ListNode(-1);
        ListNode a = dummy, b = dummy.next;
        ListNode p = head;
        int i=1;
        while(p!=null){
            while(p!=null && i<left){
                ListNode tmp = p.next;
                p.next = b;
                a.next = p;
                b = p.next;
                a = p;
                p = tmp;
                i++;
            }
            while(p!=null && i>=left && i<=right){
                ListNode tmp = p.next;
                p.next = b; 
                a.next = p;
                b = a.next;
                p = tmp;
                i++;
            }
            if(p!=null && i>right){
              while(b.next!=null)
                  b = b.next;
              while(p!=null){
                  ListNode tmp = p.next;
                  b.next = p;
                  b = p;
                  p = tmp;
              }
            }
        }
        return dummy.next;
    }
}
```

