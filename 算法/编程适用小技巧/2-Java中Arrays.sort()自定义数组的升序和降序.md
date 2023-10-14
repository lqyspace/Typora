# Java中Arrays.sort()自定义数组的升序和降序排列

> 默认升序

```java
import java.util.Arrays;
 
public class Testexample {
    public static void main(String[] args) {
        int[] a = {9, 8, 7, 2, 3, 4, 1, 0, 6, 5};        
        Arrays.sort(a);
        for(int arr:a) {
            System.out.print(arr + " ");
        }
    }
}
```

> 自定义降序

1、利用 `Collections.reverseOrder()` 方法

```java
import java.util.Arrays;
import java.util.Collections;
 
public class Testexample {
    public static void main(String[] args) {
        int[] a = {9, 8, 7, 2, 3, 4, 1, 0, 6, 5};
        Arrays.sort(a, Collections.reverseOrder());
        for(int arr:a) {
            System.out.print(arr + " ");
        }
    }
}
```

2、实现Comparator接口复写的compare()方法，代码如下：

```java
public class Test26 {
    public static void main(String[] args) {
        // 知乎第3题
        String str = "zhihu";
        System.out.println(new Test26().lengt(str));
        Integer[] nums = new Integer[]{1,2,2,4};
        
        Arrays.sort(nums,new Comparator<Integer>() {

            @Override
            public int compare(Integer o1, Integer o2) {
                // TODO Auto-generated method stub
                return (int)(o2-o1);
            }
        });
    }
}
```

==注意==：要想改变默认的排序顺序，不能使用基本的数据类型(int, double, char)，而应该是他们对应的类或者对象；

```java
public class Test26 {
    public static void main(String[] args) {
        String str = "zhihu";
        System.out.println(new Test26().lengt(str));
        Integer[] nums = new Integer[]{1,2,2,4};
        
        Arrays.sort(nums,new Comparator<Integer>() {

            @Override
            public int compare(Integer o1, Integer o2) {
                // TODO Auto-generated method stub
                return (int)(o2-o1);
            }
        });
        Comparator cmp = new MyComparator();
        Arrays.sort(nums, cmp);
    }
}
class MyComparator implements Comparator<Integer>{
    @Override
    public int compare(Integer o1, Integer o2) {
        int i1 = (int) o1;
        int i2 = (int) o2;
        return i1 - i2;
    }
}
```

简单的写法：

```java
class Solution {
    public List<Integer> topStudents(String[] positive_feedback, String[] negative_feedback, String[] report, int[] student_id, int k) {
        Map<String, Integer> words = new HashMap<>();
        for (String word : positive_feedback) {
            words.put(word, 3);
        }
        for (String word : negative_feedback) {
            words.put(word, -1);
        }
        int n = report.length;
        int[] scores = new int[n];
        int[][] A = new int[n][2];
        for (int i = 0; i < n; i++) {
            int score = 0;
            for (String word : report[i].split(" ")) {
                score += words.getOrDefault(word, 0);
            }
            A[i] = new int[]{-score, student_id[i]};
        }
        Arrays.sort(A, (a, b) -> a[0] == b[0] ? a[1] - b[1] : a[0] - b[0]);
        List<Integer> topK = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            topK.add(A[i][1]);
        }
        return topK;
    }
}
```

