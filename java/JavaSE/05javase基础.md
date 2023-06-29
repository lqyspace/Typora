[TOC]



# 七、Map集合

## 1 Map集合概述

- Interface Map<K,V>   K: 键的类型，V: 值的类型
- 将键映射到值的对象：不能包含重复的键；每个键最多映射到一个值
- 创建Map集合的对象：采用多态的方式、具体的实现类HashMap

```java
public class hashmap01 {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();
        map.put("01", "aa");
        map.put("02", "bb");
        map.put("03", "cc");
        map.put("03", "dd");
        System.out.println(map);
    }
}
```

总结：

- 键的唯一性，其实是HashMap保证的，HashMap用了哈希表，哈希表用来保证键的唯一性
- 第一次put相同的键是添加，第二次put相同的键则是修改键的值。



## 2 Map集合的基本功能

![image-20230310170312884](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303101703922.png)

```java
public class HashMapDemo02 {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();

        // V put(K key, V valeu)：添加元素
        map.put("张无忌", "赵敏");
        map.put("郭靖", "黄蓉");
        map.put("杨过", "小龙女");
//        System.out.println(map.put("黑龙", "鲤鱼"));// 返回的是null

        System.out.println(map);

        // V remove(Object key)
//        System.out.println(map.remove("郭靖")); // 黄蓉
//        System.out.println(map.remove("杨过")); // 小龙女

//        map.clear();// {}
        System.out.println(map.containsKey("郭靖")); // true
        System.out.println(map.containsValue("小龙女"));// true
        System.out.println(map.isEmpty());// false
        System.out.println(map.size()); // 3
        map.clear();
        System.out.println(map.isEmpty());//true
        System.out.println(map);//
    }
}
```



## 3 Map集合的获取功能

![image-20230310174301139](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303101743170.png)

```java
public class HashMapDemo03 {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();

        map.put("张无忌", "赵敏");
        map.put("郭靖", "黄蓉");
        map.put("杨过", "小龙女");

        System.out.println(map.get("郭靖"));// 郭靖
        System.out.println(map.get("杨过"));// 黄蓉
        System.out.println(map.get("张三丰"));//null

        System.out.println(map.keySet());// 获取键的集合

        System.out.println(map.values());// 获取值得集合
    }
}
```

```java
黄蓉
小龙女
null
[杨过, 郭靖, 张无忌]
[小龙女, 黄蓉, 赵敏]
```



## 4 Map集合的遍历

- Map集合中操作
  - 获取所有键的集合。用keySet()方法实现
  - 遍历键的集合，获取每一个键。用增强for实现
  - 根据键去找值。用get(Object key)方法实现
- 集合遍历的方式
  - 根据键找值
  - 键值对对象找键和值

> entrySet()方法

```java
Set<Map.Entry<K,V>> entrySet() 返回此地图中包含的映射的Set视图
```

```java
public class HashMapDemo04 {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();

        map.put("张无忌", "赵敏");
        map.put("郭靖", "黄蓉");
        map.put("杨过", "小龙女");

        // 获取所有键值对对象的集合
        Set<Map.Entry<String, String>> entrySet = map.entrySet();
        for (Map.Entry<String, String> m: entrySet){
            String key = m.getKey();
            String value = m.getValue();
            System.out.println(key + ", "+value);
        }
    }
}
```

输出：

```java
杨过, 小龙女
郭靖, 黄蓉
张无忌, 赵敏
```



- 集合遍历的两种方法

  ```java
  public class HashMapDemo04 {
      public static void main(String[] args) {
          Map<String, String> map = new HashMap<>();
  
          map.put("张无忌", "赵敏");
          map.put("郭靖", "黄蓉");
          map.put("杨过", "小龙女");
  
          // 方式一：键找值
          Set<String> keySet = map.keySet();
          for (String key: keySet){
              String value = map.get(key);
              System.out.println(key + "," + value);
          }
          System.out.println("-----------------");
          
          // 方式二：获取所有键值对对象的集合
          Set<Map.Entry<String, String>> entrySet = map.entrySet();
          for (Map.Entry<String, String> m: entrySet){
              String key = m.getKey();
              String value = m.getValue();
              System.out.println(key + ", "+value);
          }
      }
  }
  ```



## 5 案例 HashMap集合练习

- 案例需求
  - 创建一个HashMap集合，键是学生对象(Student)，值是居住地(String)。存储多个元素，并遍历
  - 要求保证键的唯一性：如果学生对象的成员变量值相同，我们就认为是同一个对象
    - 要重写hashCode和equals两个方法
- 代码实现
  - 学生类

```java
public class Student {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Student student = (Student) o;

        if (age != student.age) return false;
        return name != null ? name.equals(student.name) : student.name == null;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }
}
```

测试类：

```java
public class HashMapDemo {
    public static void main(String[] args) {
        HashMap<Student, String> hm = new HashMap<>();
        Student s1 = new Student("林青霞",33);
        Student s2 = new Student("张曼玉",34);
        Student s3 = new Student("王祖贤",32);
        Student s4 = new Student("王祖贤",32);

        hm.put(s1, "西安");
        hm.put(s2, "河北");
        hm.put(s3, "山西");
        hm.put(s4, "北京");

        Set<Student> stus = hm.keySet();
        for (Student key: stus){
            String value = hm.get(key);
            System.out.println(key.getName() + "," + key.getAge() + "," + value);
        }
    }
}
```

输出：

```java
王祖贤,32,北京
张曼玉,34,河北
林青霞,33,西安
```



## 6 案例2 集合嵌套之ArrayList嵌套HashMap

- 案例需求
  - 创建一个ArrayList集合，存储三个元素，每一个元素都是HashMap
  - 每一个HashMap的键和值都是String，并遍历

```java
public class ArrayListIncludeHashMapDemo {
    public static void main(String[] args) {
        ArrayList<HashMap<String, String>> ahm = new ArrayList<HashMap<String, String>>();
        HashMap<String, String> hm1 = new HashMap<>();
        hm1.put("孙策","大乔");
        hm1.put("周瑜", "小乔");
        ahm.add(hm1);

        HashMap<String, String> hm2 = new HashMap<>();
        hm2.put("郭靖","黄蓉");
        hm2.put("杨过", "小龙女");
        ahm.add(hm2);

        HashMap<String, String> hm3 = new HashMap<>();
        hm3.put("令狐冲","任盈盈");
        hm3.put("林平之", "岳灵珊");
        ahm.add(hm3);

        for (HashMap<String,String> hm: ahm){
            Set<String> keys = hm.keySet();
            for (String k : keys){
                String val = hm.get(k);
                System.out.println(k + "," + val);
            }
        }
    }
}
```

输出：

```java
孙策,大乔
周瑜,小乔
杨过,小龙女
郭靖,黄蓉
令狐冲,任盈盈
林平之,岳灵珊
```



## 7 案例3 集合嵌套之HashMap嵌套ArrayList

- 案例需求

  - 创建一个HashMap集合，存储三个元素，每一个键值对元素的键都是String，值是ArrayList
  - 每一个ArrayList的元素都是String，并遍历

- 代码实现

  ```java
  public class HashMapincludeArrayList {
      public static void main(String[] args) {
          HashMap<String, ArrayList<String>> hma = new HashMap<String, ArrayList<String>>();
          ArrayList<String> sq = new ArrayList<>();
          sq.add("诸葛亮");
          sq.add("赵云");
          hma.put("三国", sq);
  
          ArrayList<String> xyj = new ArrayList<>();
          xyj.add("唐僧");
          xyj.add("孙悟空");
          hma.put("西游记", xyj);
  
          ArrayList<String> sh = new ArrayList<String>();
          sh.add("武松");
          sh.add("宋江");
          hma.put("水浒传", sh);
  
          Set<String> str = hma.keySet();
          for (String k: str){
              ArrayList<String> as = hma.get(k);
              for (String ss : as){
                  System.out.println(k + "," + ss);
              }
          }
      }
  }
  ```

输出：

```java
水浒传,武松
水浒传,宋江
西游记,唐僧
西游记,孙悟空
三国,诸葛亮
三国,赵云
```



## 8 案例 统计字符串中的每个字符出现的次数

![image-20230310202607382](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303102026416.png)

注：**键是字符**，类型应该是**Character**；值是字符出现的次数，类型应该是**Integer**。

```java
public class HashMapDemo {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入一个字符串：");
        String str = sc.nextLine();

//        HashMap<Character, Integer> hm = new HashMap<>();
        TreeMap<Character, Integer> hm = new TreeMap<>();/// 无参构造方法，对键进行排序

        for (int i=0; i<str.length(); i++){
            char ch = str.charAt(i);
            // key = hm.get(ch); key==null
            if (hm.containsKey(ch)){
                hm.put(ch, hm.get(ch)+1);
            }else{
                hm.put(ch, 1);
            }
        }

        StringBuilder sb = new StringBuilder();

        Set<Map.Entry<Character, Integer>> set = hm.entrySet();
        for (Map.Entry<Character, Integer> s : set){
            sb.append(s.getKey()+"("+s.getValue()+")");
        }

        System.out.println(sb.toString());
    }
}
```

输出：

```java
请输入一个字符串：
hfiahfuiha
a(2)f(2)h(3)i(2)u(1)
```



注意：TreeMap同样和TreeSet一样具有排序的功能



# 八、Collections

## 1 Collections概述

- 是针对集合操作的工具类
- 里面全是静态方法



## 2 Collections常用方法

- public static <T extends Comparable<? super T>> void sort(List<T> list): 将指定的列表按升序排序
- public static void reverse(List<?> list): 反转指定列表中元素的顺序
- public static void shuffle(List<?> list): 使用默认的随机源随机排列指定的列表

```java
public class CollectionsDemo {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(10);
        list.add(20);
        list.add(40);
        list.add(50);
        list.add(60);
        list.add(30);

        Collections.sort(list);
        System.out.println(list);
        Collections.reverse(list);
        System.out.println(list);
        Collections.shuffle(list);
        System.out.println(list);
    }
}
```



## 3 案例 ArrayList存储学生对象并排序

需求：ArrayList存储学生对象，使用Collections对ArrayList进行排序

要求：按照年龄的从大到小排序，年龄相同时，按照姓名的字母表顺序排序

```java
public class CollectionsArrayListDemo {
    public static void main(String[] args) {
        List<Student> list = new ArrayList<Student>();
        Student stu1 = new Student("linqingxia",34);
        Student stu2 = new Student("wangzuxian",31);
        Student stu3 = new Student("liyuchun",32);
        Student stu4 = new Student("liuxiang",35);
        Student stu5 = new Student("linqingxia",34);

        list.add(stu1);
        list.add(stu2);
        list.add(stu3);
        list.add(stu4);
        list.add(stu5);// 列表中是可以存在重复元素的

        for (Student s : list){
            System.out.println(s.getName() + "," + s.getAge());
        }

        System.out.println("-------------------------");

        Collections.sort(list, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                int num = o1.getAge() - o2.getAge();
                int num2 = num==0 ? o1.getName().compareTo(o2.getName()) : num;
                return num2;
            }
        });

        for (Student s: list){
            System.out.println(s.getName() + "," + s.getAge());
        }
    }
}
```

输出：

```java
linqingxia,34
wangzuxian,31
liyuchun,32
liuxiang,35
linqingxia,34
-------------------------
wangzuxian,31
liyuchun,32
linqingxia,34
linqingxia,34
liuxiang,35
```



## 4 案例 模拟斗地主

![image-20230310225825947](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303102258979.png)

```java
public class DouDiZhu {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();

//       ♦2，。。。，♦A
//        ...
        // 定义花色数组
        String[] colors = {"♦", "♣", "♥", "♣"};
        String[] numbers = {"2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "k", "A"};
        for (String color: colors){
            for (String num: numbers){
                list.add(color + num);
            }
        }

        list.add("大王");
        list.add("小王");
//        System.out.println(list);

        // 洗牌
        Collections.shuffle(list);

        //发牌
        ArrayList<String> no1 = new ArrayList<>();
        ArrayList<String> no2 = new ArrayList<>();
        ArrayList<String> no3 = new ArrayList<>();
        ArrayList<String> no4 = new ArrayList<>();

        for (int i=0; i<list.size(); i++){
            String poker = list.get(i);
            if(i>=list.size() - 3){
                no4.add(poker);
            }else if(i%3==0){
                no1.add(poker);
            }else if(i%3==1){
                no2.add(poker);
            }else if (i%3==2){
                no3.add(poker);
            }
        }

        // 看牌
        lookPoker("林青霞", no1);
        lookPoker("柳岩", no2);
        lookPoker("风清扬", no3);
        lookPoker("底牌", no4);

    }

    public static void lookPoker(String name, ArrayList<String> arr){
        System.out.print(name + "的牌是:");
        for (String poker: arr){
            System.out.print(poker + " ");
        }
        System.out.println();
    }
}
```

输出：

```java
林青霞的牌是:♣2 ♣7 ♣J ♣6 ♦10 ♣7 ♣3 ♥Q ♦5 ♥8 ♥J ♣8 ♥6 ♥3 ♣2 ♣Q ♥4 
柳岩的牌是:♥k ♣3 ♦k 大王 ♣A ♣J ♣5 ♣9 ♣8 ♦7 ♣k ♦A ♣10 ♥2 ♦J ♣Q ♥9 
风清扬的牌是:♣9 小王 ♥7 ♣6 ♣10 ♦9 ♦6 ♣4 ♣5 ♣4 ♥5 ♦2 ♥A ♣A ♦8 ♥10 ♦4 
底牌的牌是:♦Q ♣k ♦3 
```

对牌进行排序：

```java
public class DouDiZhu01 {
    public static void main(String[] args) {
        // 创建HashMap，键是编号，值是拍
        HashMap<Integer, String> hm = new HashMap<>();

        // 创建ArrayList,存储编号
        ArrayList<Integer> arr = new ArrayList<Integer>();

        String[] colors = {"♦", "♣", "♥", "♣"};
        String[] numbers = {"3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "k", "A", "2"};
        int index = 0;
        for (String num: numbers){
            for (String color: colors){
                hm.put(index, color+num);
                arr.add(index);
                index++;
            }
        }
        hm.put(index, "小王");
        arr.add(index);
        index++;
        hm.put(index, "大王");
        arr.add(index);

        // 洗牌
        Collections.shuffle(arr);

        /// 发牌，为了实现排序，使用TreeSet
        TreeSet<Integer> lqx = new TreeSet<>();
        TreeSet<Integer> fqy = new TreeSet<>();
        TreeSet<Integer> wzx = new TreeSet<>();
        TreeSet<Integer> dp = new TreeSet<>();

        for (int i=0; i<arr.size(); i++){
            int poker = arr.get(i);
            if(i>=arr.size() - 3){
                dp.add(poker);
            }else if(i%3==0){
                lqx.add(poker);
            }else if(i%3==1){
                fqy.add(poker);
            }else if (i%3==2){
                wzx.add(poker);
            }
        }
        lookPoker("林青霞", lqx, hm);
        lookPoker("风清扬", fqy, hm);
        lookPoker("王祖贤", wzx, hm);
        lookPoker("底牌", dp, hm);
    }

    public static void lookPoker(String name, TreeSet<Integer> tree, HashMap<Integer, String> hm){
        System.out.print(name + "的牌：");
        for (Integer nu: tree){
            System.out.print(hm.get(nu) + " ");
        }
        System.out.println();
    }
}
```

输出：

```java
林青霞的牌：♦4 ♣4 ♥4 ♣4 ♥5 ♣7 ♥7 ♣8 ♣8 ♣9 ♥10 ♥J ♦Q ♣k ♣k ♥A ♣2 
风清扬的牌：♣3 ♦5 ♣5 ♦6 ♣6 ♥6 ♥9 ♦10 ♦J ♣Q ♦k ♥k ♦A ♣A ♥2 ♣2 大王 
王祖贤的牌：♣3 ♥3 ♣5 ♣6 ♦7 ♣7 ♦8 ♥8 ♦9 ♣9 ♣10 ♣10 ♣J ♣J ♥Q ♣A ♦2 
底牌的牌：♦3 ♣Q 小王
```



# 九、File&递归&字节流

## 1 File

### 1.1 File概述

- File类的概述
  - 它是**文件和目录路径名**的抽象表示
  - **文件和目录**是可以通过File封装成对象的
  - 对于File而言，其封装的并不是一个真正的文件，仅仅是一个路径名而已。它可以是存在的，也可以是不存在的。将来是要通过具体的操作把这个路径的内容转换为具体存在的
  
- File类的构造方法

  ![image-20230311092246729](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303110922835.png)

- 示例代码

  ```java
  public class File01 {
      public static void main(String[] args) {
          // File(String pathName)
          File file = new File("C:\\Users\\HP\\Desktop\\stopwords.txt");
          System.out.println(file);// 打印的是C:\Users\HP\Desktop\stopwords.txt,所以说，File重写了toString方法
  
          // File(String parent, String child)
          File file1 = new File("C:\\Users\\HP\\Desktop", "stopwords.txt");
          System.out.println(file1); // C:\Users\HP\Desktop\stopwords.txt
  
          // File(File parent, String child)
          File f3 = new File("C:\\Users\\HP\\Desktop");
          File f4 = new File(f3, "stopwords.txt");
  
          System.out.println(f4);
      }
  }
  ```

  输出：

  ```java
  C:\Users\HP\Desktop\stopwords.txt
  C:\Users\HP\Desktop\stopwords.txt
  C:\Users\HP\Desktop\stopwords.txt
  ```

  

### 1.2 File的创建功能

![image-20230311093836243](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303110938296.png)

代码示例：

```java
public class File02 {
    public static void main(String[] args) {

        File f2 = new File("E:\\workspace");
        // 如果文件不存在就创建文件并返回true，如果文件存在就不创建文件并返回false
        File f1 = new File(f2, "java.txt");
        // 如果目录不存在就创建目录并返回true，如果目录存在就不创建目录并返回false
        File f3 = new File(f2, "JavaSe");
        // 如果目录不存在就创建目录并返回true，如果目录存在就不创建目录并返回false
        File f4 = new File(f2, "JavaWeb\\HTML");

        File f5 = new File(f2, "javase.txt");// 使用mkdir方法时，是把javase.txt当做一个目录

        try {
            System.out.println(f1.createNewFile());//创建一个文件，文件存在就不创建并返回false，文件不存在就创建文件并返回true
            System.out.println(f3.mkdir());// 创建单级目录
            System.out.println(f4.mkdirs());//创建多级目录
            System.out.println(f5.mkdir());
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

注意：创建文件就应该用创建文件的方式；创建目录就应该用创建目录的方式



### 1.3 File类判断和获取功能

![image-20230311095617618](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303110956680.png)

```java
public class File03 {
    public static void main(String[] args) throws IOException {
        File f1 = new File("java.txt");
        System.out.println(f1.isDirectory());
        System.out.println(f1.isFile());
        System.out.println(f1.exists());

        System.out.println(f1.getAbsolutePath());// 返回此抽象路径名的绝对路径名字字符串
        System.out.println(f1.getPath());// 将此抽象路径名转换为路径名字字符串
        System.out.println(f1.getName());// 返回此抽象路径名表示的文件或目录的名称

        System.out.println("---------------------------");
        File f2 = new File("E:\\workspace");
        System.out.println(f2.getAbsolutePath());
        System.out.println(f2.getPath());
        System.out.println(f2.getName());

        String[] arr = f2.list();
        for (String s: arr)
            System.out.println(s);

        System.out.println("---------------------------");

        File[] f3 = f2.listFiles();
        for (File f: f3){
            if (f.isFile()){
                System.out.println(f);
                System.out.println(f.getName());
            }
        }
    }
}
```

输出：

```java
false
true
true
D:\IntelIdea2022\workspace\JavaLearning01\java.txt
java.txt
java.txt
---------------------------
E:\workspace
E:\workspace
workspace
java.txt
JavaSe
javase.txt
JavaWeb
---------------------------
E:\workspace\java.txt
java.txt
```



### 1.4 File删除功能

> public boolean delete(): 删除由此抽象路径名表示的文件或目录

![image-20230311111047223](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111110285.png)

代码示例：

```java
public class File04 {
    public static void main(String[] args) throws IOException {
        File f1 = new File("src\\file\\java.txt");
        f1.createNewFile();
        System.out.println(f1.getName());
        System.out.println(f1.getPath());
        System.out.println(f1.getAbsolutePath());

        System.out.println("-----------------");
        System.out.println(f1.delete());

        File f2 = new File("src\\itcast");
        System.out.println(f2.mkdir());
//        System.out.println(f2.delete());

        File f3 = new File(f2, "java.txt");
//        f3.createNewFile();

        System.out.println("--------------------");
        // 删除itcast文件，此时itcast里面有文件
//        System.out.println(f2.delete());// 因为itcast里面有内容，，所以不能直接删，要先把内容删掉，才能删这个目录
        System.out.println(f3.delete());// true
        System.out.println(f2.delete()); // true

    }
}
```

输出：

```java
java.txt
src\file\java.txt
D:\IntelIdea2022\workspace\JavaLearning01\src\file\java.txt
-----------------
true
false
--------------------
true
true
```





## 2 递归

### 2.1 递归的介绍

![image-20230311111523370](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111115444.png)

![image-20230311111538193](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111115247.png)

![image-20230311111627609](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111116663.png)

进栈过程：

![image-20230311111900166](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111119235.png)

首先调用main方法，main方法进栈，在main的内存区域中声明一个int类型result变量，然后jc函数进栈，根据递归，继续进栈，当最后满足递归终止条件时出栈。出栈时将上一层的值返回给当层，然后层层出栈，最终计算出值，main出栈。

出栈过程：

![image-20230311111924034](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111119100.png)



### 2.2 递归的案例

![image-20230311122045256](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111220308.png)

```java
public class File05 {
    public static void main(String[] args) {
        File f1 = new File("E:\\workspace");
        dg(f1);
    }
    public static void dg(File file){
//        String[] files = file.list();
        File[] files = file.listFiles();
        if (files.length>0){
            System.out.print(file.getPath() + "目录下：");
            for (File f: files){
                System.out.print(f.getName() + " ");
            }
        }
        System.out.println();
        for (File str : files){
            if (str.isDirectory()){
                dg(str);
            }
        }
    }
}
```

输出：

```java
E:\workspace目录下：java.txt JavaSe javase.txt JavaWeb 
E:\workspace\JavaSe目录下：javase.txt 
E:\workspace\javase.txt目录下：java.txt 
E:\workspace\JavaWeb目录下：HTML 
```

另一种：

```java
public class File05 {
    public static void main(String[] args) {
        File f1 = new File("E:\\workspace");
        dg(f1);
    }
    public static void dg(File file){
//        String[] files = file.list();
        File[] files = file.listFiles();
        if (files.length>0){
//            System.out.print(file.getPath() + "目录下：");
            for (File f: files){
                if (f.isDirectory()){
                    dg(f);
                }else
                    System.out.println(f.getName() + " ");
            }
        }
    }
}
```

输出：

```java
E:\workspace\java.txt 
E:\workspace\JavaSe\javase.txt 
E:\workspace\javase.txt\java.txt 
```



## 3 字节流

### 3.1 IO流概述

- IO：输入/输出(Input/Output)
- 流：是一种抽象概念，是对数据传输的总称。也就是说数据在设备之间的传输称为流，流的本质使数据传输
- IO流就是用来处理设备间数据传输问题的
  - 常见的应用：文件复制；文件上传；文件下载

### 3.2 IO流的分类

- 按照数据流向分类
  - 输入流：读数据
  - 输出流：写数据
- 按照数据类型分类
  - 字节流
    - 字节输入流
    - 字节输出流
  - 字符流
    - 字符输入流
    - 字符输出流
- IO流的使用场景
  - 如果操作的是纯文本文件，优先使用字符流
  - 如果操作的是图片，视频，音频等二进制文件。优先使用字节流
  - 如果不确定文件类型，优先使用字节流。字节流是万能的流。



###  3.3 字节流写数据

- 字节流抽象基类
  - InputStream：这个抽象类是表示字节输入流的所有类的超类
  - OutputStream：这个抽象类表示字节输出流的所有类的超类
  - 子类名特点：子类名称都是以父类名称作为子类名称的后轴
- FileOutputStream：创建文件输出流已制定的名称写入文件
  - FileOutputStream(String name)：创建文件输出流以指定的名称写入文件
- 使用字节输出流写数据的步骤
  - 创建字节输出流对象（调用系统功能创建了文件，创建字节输出流对象，让字节输出流对象指向文件）
  - 调用字节输出流对象的写数据方法
  - 释放资源（关闭此文件输出流并释放与此相关联的任何系统资源）

```java
public class FileOutputStreamDemo01 {
    public static void main(String[] args) throws IOException {
        //创建字节输出流对象
        FileOutputStream fos = new FileOutputStream("src\\file\\fos.txt");
        /*
        * FileOutputStream做了三件事情：
        *   1. 调用系统功能创建文件
        *   2. 创建了字节输出流对象
        *   3. 让字节输出流对象指向创建好的文件
        * */
        
		byte[] bys = new byte[]{97,98,99};
        
//        void write(int b) 将指定的字节写入此文件输出流。
        fos.write(97);// 97 的ascii码是a ，'0' 的ASCII码：48
//        fos.write(bys); // abc
        fos.write(bys,0,2); // ab

        fos.close();// 关闭此文件输出流并释放与此相关联的任何系统资源
    }
}
```



> **字节输出流写数据的3种方式**

![image-20230311135959794](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111359860.png)

```java
public class FileOutPutStreamDemo02 {
    public static void main(String[] args) throws IOException {
        // 构造方法:FileOutputStream(String name) 或 FileOutputStream(File file) ;二者等价
//        FileOutputStream fos = new FileOutputStream("src\\file\\myfos.txt");
        FileOutputStream fos = new FileOutputStream(new File("src\\file\\myfos.txt"));

        // void write(int b) 将指定的字节写入此文件输出流中
//        fos.write(97);
//        fos.write(98);
//        fos.write(99);
//        fos.write(100);
//        fos.write(101);

//        void write(byte[] b) 将b.length字节从指定的字节数组写入此文件输出流
//        byte[] bys = {97, 98, 99, 100, 101, 102};
//        字符串有一个: byte[] getBytes()方法，返回字符串对应的字节数组
        byte[] bys = "abcdefg".getBytes();
//        fos.write(bys);


//        void write(byte[] b, int off, int len)：将len字节从指定的字节数组开始，从偏移量off开始写入此文件输出流
        fos.write(bys, 1, 3);// 这里指 98, 99, 100 对应的字节

    }
}
```



> **字节流写数据的两个小问题**

- 字节流写数据如何实现换行
  - windows: \r\n
  - linux: \n
  - mac: \r
- 字节流写数据如何实现追加
  - public FileOutputStream(String name, boolean append)
  - 创建文件输出流以指定的名称写入文件。如果第二个参数为true，则字节将写入文件的末尾而不开始开头。

- 示例代码

  ```java
  public class FileOutputStreamDemo03 {
      public static void main(String[] args) throws IOException {
          FileOutputStream fos = new FileOutputStream("src\\file\\fos.txt", true);
          for (int i=0; i<10; i++){
              fos.write("hello".getBytes());
              fos.write("\r\n".getBytes());
          }
          fos.close();
      }
  }
  ```



> **字节流写数据加异常处理**

- 异常处理格式

  - try-catch-finally

  ```java
  try{
      可能出现异常的代码
  }catch(异常类名 变量名){
      异常的处理代码
  }finally{
      执行所有的清除操作
  }
  ```

  - finally特点
    - 被finally控制的语句一定会执行，除非JVM退出

- 示例代码

```java
public class FileOutputStreamDemo04 {
    public static void main(String[] args) {
        FileOutputStream fos = null;
        try {
            fos = new FileOutputStream("src\\file\\myfos.txt");
            fos.write("hello".getBytes());
        } catch (IOException e) {
            e.printStackTrace();
        }finally {
            if (fos!=null){
                try {
                    fos.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



### 3.4 字节流读数据

- 字节输入流

  - FileInputStream(String name): 通过打开与实际文件的链接来创建一个FileInputStream，该文件由文件系统中的路径名name命名。

- 字节输入流读取数据的步骤

  - 创建字节输入流对象
  - 调用字节输入流的读方法
  - 释放资源

- 示例代码：一次读一个字节的数据

  ```java
  public class FileInputStreamDemo01 {
      public static void main(String[] args) throws IOException {
          // 创建字节输入流对象
          FileInputStream fis = new FileInputStream("src\\file\\myfos.txt");
          int by;
          /*
          * fis.read()：读数据
          * by = fis.read()：把读取到的数据赋值给by
          * by != -1：判断读取的数据是否是-1，读取的数据不是-1，说明还没到末尾
          * */
          while ((by=fis.read())!=-1){
              System.out.print((char)by);
          }
  
          fis.close();
      }
  }
  ```

  

  

  ### 3.5 字节流复制案例（一次读一个字节数据）
  
  > abstract int read() 从输入流读取数据的下一个字节
  >
  > int read(byte[] b) 从输入流中读取一些字节数，并将它们存储到缓冲器阵列b
  
  ```java
  public class FileInputDemo01 {
      public static void main(String[] args) throws IOException {
          FileInputStream fis = new FileInputStream("src\\file\\myfos.txt");
  
          // 调用字节输入流对象的读数据方法
          // int read(byte[] b): 从该输入流读取最多b.length个字节的数据到一个字节数组
          byte[] bys = new byte[10];
  
          // 第一次读取数据
          int len = fis.read(bys);
          System.out.println(len);// 5
  //        System.out.println(new String(bys)); // hello     (后面跟了5个空格)
          System.out.println(new String(bys, 0, len)); // hello（后面没有空格）
  
  
          // 第二次读取数据
          len = fis.read(bys);
          System.out.println(len);
  //        System.out.println(new String(bys));
          System.out.println(new String(bys, 0, len));
  
          // 第三次读取数据
          len = fis.read(bys);
          System.out.println(len);
  //        System.out.println(new String(bys));
          System.out.println(new String(bys, 0, len));
  
          /*
          * hello\r\n
          * world\r\n
          *
          * 第一次读取：hello
          * 第二次读取：\r\nwor
          * 第三次读取：ld\r\nr  因为第二次读取的时候读的是五个，所以第三次读取的时候只能读四个，只把bys数组的前四个给替换掉了
          *           第五个r仍然保留着，因为第三次读取是 ld\r\nr；可以使用new String(byte[] b, int off, int len)修改
          * */
  
          // 再多读两次
  //        len = fis.read(bys);// 当读取长度是-1时，说明已经读到末尾了
  //        System.out.println(len);
  //        len = fis.read(bys);
  //        System.out.println(len);
  
  //        byte[] bys = new byte[1024]; // 1024及其整数倍
  //        int len;
  //        while ((len=fis.read(bys))!=-1){
  //            System.out.println(new String(bys, 0, len));
  //        }
  //        fis.close();
  
      }
  }
  ```
  
  输出：
  
  ```
  5
  hello
  5
  
  wor
  4
  ld
  ```
  
  改进：
  
  ```java
  public class FileInputDemo01 {
      public static void main(String[] args) throws IOException {
          FileInputStream fis = new FileInputStream("src\\file\\myfos.txt");
  
          // 调用字节输入流对象的读数据方法
          // int read(byte[] b): 从该输入流读取最多b.length个字节的数据到一个字节数组
  //        byte[] bys = new byte[5];
  //
  //        // 第一次读取数据
  //        int len = fis.read(bys);
  //        System.out.println(len);
  ////        System.out.println(new String(bys));
  //        System.out.println(new String(bys, 0, len));
  //
  //
  //        // 第二次读取数据
  //        len = fis.read(bys);
  //        System.out.println(len);
  ////        System.out.println(new String(bys));
  //        System.out.println(new String(bys, 0, len));
  //
  //        // 第三次读取数据
  //        len = fis.read(bys);
  //        System.out.println(len);
  ////        System.out.println(new String(bys));
  //        System.out.println(new String(bys, 0, len));
  
          /*
          * hello\r\n
          * world\r\n
          *
          * 第一次读取：hello
          * 第二次读取：\r\nwor
          * 第三次读取：ld\r\nr  因为第二次读取的时候读的是五个，所以第三次读取的时候只能读四个，只把bys数组的前四个给替换掉了
          *           第五个r仍然保留着，因此第三次读取是 ld\r\nr；可以使用new String(byte[] b, int off, int len)修改，这样做保证了读了几个转码几个。同时也说明了len指的不是字节数组的长度，而是读取的字节的个数，第三次读取的时候只有 ld\r\n ，因此只读到了四个。
          * */
  
          // 再多读两次
  //        len = fis.read(bys);// 当读取长度是-1时，说明已经读到末尾了
  //        System.out.println(len);
  //        len = fis.read(bys);
  //        System.out.println(len);
  
          byte[] bys = new byte[1024]; // 1024及其整数倍
          int len;
          while ((len=fis.read(bys))!=-1){
              System.out.println(new String(bys, 0, len));
          }
          fis.close();
  
      }
  }
  ```
  
  输出：
  
  ```java
  hello
  world
  fbha
  fssfees
  csvsd
  casa
  afafefvs
  ```
  
  注意：
  
  - 声明了byte[]数组，是为了把读取到的数据存放到这个byte[]数组中
  - int read(byte[] b): 从该输入流读取**最多b.length个字节**的数据到一个字节数组
  - len并不是字节数组的长度，而是读取到的字节的个数
  
  
  
  ![image-20230311150718776](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111507838.png)

```java
public class CopyTextDemo {
    public static void main(String[] args) throws IOException {
        // 创建字节输入流对象
        FileInputStream fis = new FileInputStream("src\\file\\窗里窗外.txt");
        // 创建字节输出流对象
        FileOutputStream fos = new FileOutputStream("src\\file\\窗里窗外copy.txt");

        int b;

        while ((b=fis.read())!=-1){
            fos.write(b);
        }

        fos.close();
        fis.close();
    }
```



### 3.6 字节流读数据（一次读一个字节数组数据）

- 一次读一个字节数组的方法

  - public int read(byte[] b)：从输入流读取最多b.length个字节的数据
  - 返回的是读入缓冲区的总字节数，也就是实际的读取字节的个数

- 示例代码

  ![image-20230311160821402](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303111608472.png)

- 流程：

  - 在实现文件的拷贝时，如果一个个字节来读写，经常操作文件这样的效率太低了。首先创建一个字节数组来保存一次性读取多个字节的数据，再把字节数组中的数据一次性写入文件。这里的缓冲区相当于字节数组。

  - 代码案例：

    ```java
    import java.io.*;
    public class P07 {
    public static void main(String[] args) throws Exception {
            // TODO Auto-generated method stub
                    //创建一个字节输入流，读取text文件夹下test08.txt数据
                    InputStream in = new FileInputStream("text/test08");
                    //创建一个字节输出流，用来读取数据并在text文件夹下创建一个test09.txt文件
                    OutputStream out = new FileOutputStream("text/test09");
                    //定义字节数组使用缓存区读写文件数据
                    byte bt[]=new byte[1024];
                    //定义一个int类型的变量num,保存读取读到缓冲区的字节数
                    int num;
                    //复制文件前的系统时间
                    long startTime=System.currentTimeMillis();
                    //while循环判断读取的字节是否读到文件的末尾
                    while((num=in.read(bt))!=-1){
                        out.write(bt,0,num);
                    }
                    //复制文件后的系统时间
                    long endTime=System.currentTimeMillis();
                    System.out.println("复制文件所使用的时间是："+(endTime-startTime)+"毫秒");
                    //关闭流
                    in.close();
                    out.close();
      }
    }
    ```

    

### 3.7 字节流复制图片的案例

```java
public class CopyJpgDemo {
    public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("src\\file\\mn.jpg");

        FileOutputStream fos = new FileOutputStream("src\\mn.jpg");

        byte[] bys = new byte[1024];
        int len;
        while ((len=fis.read(bys))!=-1){
            fos.write(bys);
        }
        fos.close();
        fis.close();
    }
}
```





## 4 字节缓冲流

### 4.1 字节缓冲流构造方法

> **字节缓冲流介绍**

- 字节缓冲流介绍

  - BufferedOutputStream：该类实现缓冲输出流。通过设置这样的输出流，应用程序可以向底层输出流写入字节，而不必为写入的每个字节导致底层的系统调用

    - 通过设置一个缓冲区，我们可以一次性将这个数据写入到文件中，底层调用的次数就减少了，而不必像OutputStream每次写入一个字节都会进行一次底层调用，因此提高了效率。
    - 原理：BufferedOutputStream继承于FileOutputStream，提供缓冲输出流功能。缓冲输出流相对于普通输出流的优势：它提供了一个缓冲数组，只有缓冲数组满了或者手动flush时才会向磁盘写入数据，避免了频繁的IO操作。核心思想是，**提供一个缓冲数组，写入时首先操作缓冲数组**。

  - BufferedInputStream：创建BufferedInputStream将创建一个内部缓冲区数组。当从流中读取或跳过字节时，内部缓冲区将根据需要从所包含的输入流中 重新填充，一次很多字节

    - 可以减少访问磁盘的次数，提高文件的读取性能，它是InputStream类的子类

    - BufferedInputStream相对于普通输入流优点是，它有一个缓冲数组，每次调用read()方法，先从缓冲区读取数据，如果读取数据失败，从文件读取新数据到缓冲区，再把缓冲区的内容显示出来

    - 代码实现：

      ```java
      import java.io.*;
      public class P08 {
      public static void main(String[] args) throws Exception {
              // TODO Auto-generated method stub
              //创建一个带有缓冲区的输入流
              BufferedInputStream bi = new BufferedInputStream(new FileInputStream("text/test10"));
              //定义字节数组
              byte[] bt = new byte[1024];
              int num = 0;
              //判断是否读到文件的末尾
              while ((num = bi.read(bt)) != -1) {
              //读取的字节转为字符串对象
              String s = new String(bt, 0, num);
              System.out.println("读取的内容是："+s);
              }
              //关闭流
              bi.close();
              }
      }
      ```



> **字节缓冲流构造方法**

```java
BufferedOutputStream(OutputStream out)
BufferedInputStream(InputStream in)
```



为什么构造方法需要的是字节流，而不是具体文件的路径？

- 字节缓冲流仅仅提供缓冲区，而真正的读写操作还得依靠基本的字节流对象进行操作

代码示例：

```java
public class BufferedStreamDemo {
    public static void main(String[] args) throws IOException {
//        FileOutputStream fos = new FileOutputStream("bos.txt");
//        BufferedOutputStream bos = new BufferedOutputStream(fos);
//        字节缓冲输出流
//        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("src\\Buffered\\bos.txt"));
//        bos.write("hello\r\n".getBytes());
//        bos.write("world\r\n".getBytes());
//        bos.close();

//        字节缓冲输入流
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("src\\Buffered\\bos.txt"));

//        一次读取一个数据
//        int by;
//        while ((by=bis.read())!=-1){
//            System.out.print((char)by);
//        }

//        一次读取一个字节数组数据
        byte[] bys = new byte[1024];
        int len;
        while ((len=bis.read(bys))!=-1){
            System.out.print(new String(bys, 0, len));
        }

    }
}
```

输出：

```java
hello
world
```



### 4.2 案例 复制视频

使用四种方式：

```java
public class CopyFileDemo {
    public static void main(String[] args) throws IOException {
        long startTime = System.currentTimeMillis();
        method4();
        long endTime = System.currentTimeMillis();
        System.out.println("共耗时："+(endTime - startTime));
    }

    // 字符缓冲流一次读写一个字节数组 : 23 ms
    public static void method1() throws IOException {
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("src\\字节流复制图片.avi"));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("src\\Buffered\\字节流复制图片.avi"));
        byte[] bys = new byte[1024];
        int len;
        while ((len=bis.read(bys))!=-1){
            bos.write(bys, 0, len);
        }
        bis.close();
        bos.close();
    }

    //字节缓冲流一次读写一个字节：426 ms
    public static void method2() throws IOException {
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("src\\字节流复制图片.avi"));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("src\\Buffered\\字节流复制图片.avi"));
        int by;
        while ((by=bis.read())!=-1){
            bos.write(by);
        }
        bis.close();
        bos.close();
    }

//    基本字节流一次读写一个字节:51330ms
    public static void method3() throws IOException {
        FileInputStream bis = new FileInputStream("src\\字节流复制图片.avi");
        FileOutputStream bos = new FileOutputStream("src\\Buffered\\字节流复制图片.avi");
        int by;
        while ((by=bis.read())!=-1){
            bos.write(by);
        }
        bis.close();
        bos.close();
    }

    //基本字节流一次读写一个字节数组:73ms
    public static void method4() throws IOException {
        FileInputStream bis = new FileInputStream("src\\字节流复制图片.avi");
        FileOutputStream bos = new FileOutputStream("src\\Buffered\\字节流复制图片.avi");
        byte[] bys = new byte[1024];
        int len;
        while ((len=bis.read(bys))!=-1){
            bos.write(bys, 0, len);
        }
        bis.close();
        bos.close();
    }

}
```



### 4.3 为什么会出现字符流

- 字符流的介绍
  - 由于字节流操作中文不是特别方便，所以java就提供了字符流
  - 字符流=字节流+编码表
- 中文的字节存储方式
  - 用字节流复制文本文件时，文本文件也会有中文，但是没有问题，原因是最终底层操作会自动进行字节拼接成中文，如何识别中文的呢？
    - 汉子在存储的时候，无论选择哪种编码存储，第一个字节都是负数



### 4.4 编码表

基础知识：

- 计算机中存储的信息都是用二进制数表示的，我们在屏幕上看到的英文，汉字等字符是二进制数转换之后的结果。
- 按某种规则，将字符存储到计算机中，称之为编码。反之将存储在计算机中的二进制数按照某种规则解析出来，称为解码。
- 按照A编码存储，必须按照A解码显示，否则会导致乱码现象。

字符集：

- 什么是字符集

  是一个系统支持的所有字符的集合，包括各国文字，标点符号，图形符号，数字等

  计算机要准确的存储和识别各种字符集符号，就需要进行字符编码，一套字符集必然至少有一套字符编码，常见的字符集有ASCII字符集，GBXXX字符集，Unicode字符集等。

![image-20230314131310634](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141313752.png)



### 4.5 字符串中的编码解码

![image-20230314132522119](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141325173.png)

```java
public class FileInputDemo {
    public static void main(String[] args) throws UnsupportedEncodingException {
//        String s = "abc";//[97, 98, 99]
        // 汉字存储的时候一定是负数
        String s = "中国"; //[-28, -72, -83, -27, -101, -67]
//        byte[] bys = s.getBytes("UTF-8");//[-28, -72, -83, -27, -101, -67]
        byte[] bys = s.getBytes("GBK");//[-42, -48, -71, -6] 编码
        System.out.println(Arrays.toString(bys));

//        String ss = new String(bys);// 解码
        String ss = new String(bys, "GBK");// 解码
        System.out.println(ss);
    }
}
```



## 5 字符流

### 5.1 字符流抽象基类

- Reader：字符输入流的抽象类
- Writer：字符输出流的抽象类



### 5.2 字符流中和编码解码问题相关的两个类

- InputStreamReader

  - InputStreamReader是**从字节流到字符流**的桥梁：它读取字节，并使用指定的[`charset`](../../java/nio/charset/Charset.html)将其**解码为字符**。它使用的字符集可以由名称指定，也可以被明确指定，或者可以接受平台的默认字符集。

    每个调用InputStreamReader的read（）方法之一可能会导致从底层字节输入流读取一个或多个字节。  为了使字节有效地转换为字符，**可以从底层流读取比满足当前读取操作所需的更多字节**。 

    为了最大的效率，请考虑在BufferedReader中包装一个InputStreamReader。  例如： 

    ```java
      BufferedReader in
       = new BufferedReader(new InputStreamReader(System.in)); 
    ```

- 构造方法：

![image-20230314145040376](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141450424.png)

- OutputStreamWriter

  - OutputStreamWriter是**从字符流到字节流**的桥梁：使用指定的[`charset`将](../../java/nio/charset/Charset.html)写入的字符**编码为字节**。它使用的字符集可以由名称指定，也可以被明确指定，或者可以接受平台的默认字符集。

    每次调用write（）方法都会使编码转换器在给定的字符上被调用。  所得到的字节在写入底层输出流之前累积在缓冲区中。 请注意，传递给write（）方法的字符不会缓冲。 

    为了最大的效率，请考虑在BufferedWriter中包装一个OutputStreamWriter，以避免频繁的转换器调用。  例如： 

    ```java
      BufferedWriter out
       = new BufferedWriter(new OutputStreamWriter(System.out));
    ```

- 构造方法：

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141449986.png)



代码示例：

```java
public class ConverDemo {
    public static void main(String[] args) throws IOException {
//        OutputStreamWriter(OutputStream out) 创建一个使用默认字符编码的OutputStreamWriter。
//        OutputStreamWriter(OutputStream out, String charsetName) 创建一个使用命名字符集的OutputStreamWriter。
//        FileOutputStream fos = new FileOutputStream("osw.txt");
//        OutputStreamWriter osw = new OutputStreamWriter(fos);

//        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("osw.txt"), "utf-8");
//        osw.write("中国");
//        osw.close();

        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("osw.txt"), "GBK");
        osw.write("中国");
        osw.close();

        
// InputStreamReader(InputStream in) 创建一个使用默认字符集的InputStreamReader。  
//InputStreamReader(InputStream in, String charsetName) 创建一个使用命名字符集的InputStreamReader。  

        InputStreamReader isr = new InputStreamReader(new FileInputStream("osw.txt"), "GBK");

//         一次读取一个字符数据
        int ch;
        while ((ch=isr.read())!=-1){
            System.out.print((char)ch);
        }

        isr.close();
    }
}
```



### 5.3 字符流写数据的5种方式

- 方法介绍

![image-20230314150619966](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141506020.png)

- 刷新和关闭的方法

![image-20230314150642194](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141506236.png)

```java
public class OutputStreamWriterDemo {
    public static void main(String[] args) throws IOException {
//        OutputStreamWriter(OutputStream out) 创建一个使用默认字符编码的OutputStreamWriter。
//        OutputStreamWriter(OutputStream out, String charsetName) 创建一个使用命名字符集的OutputStreamWriter。
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("src\\streamdemo\\osw.txt"));

        // void write(int c)：写入一个字符
//        osw.write(97);
//        osw.flush();// 刷新缓冲，这样数据才能进去
//        osw.write(98);
//        osw.flush();
//        osw.write(99);
//        osw.close();// 先刷新，再关闭流

        // void write(char[] chs)：写入一个字符数组
        char[] chs = {'a', 'b', 'c', 'd', 'e'};
//        osw.write(chs);

        //void wrote(char[] chs, int off, int len)：写入一个字符数组的一部分
//        osw.write(chs, 0, chs.length);
//        osw.write(chs, 1, 3);

        // void write(Srting str):写一个字符串
//        osw.write("abcde");

        //void write(): 写一个字符串数组的一部分
//        osw.write("abcde", 0, "abcde".length());
        osw.write("abcde", 1, 3);
//

        osw.close();
    }
}
```



### 5.4 字符输流读数据2种方式

- 方法介绍

![image-20230314152011214](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141520263.png)

- 代码示例：

```java
public class InputStreamReaderDemo {
    public static void main(String[] args) throws IOException {
        InputStreamReader isr = new InputStreamReader(new FileInputStream("src\\streamdemo\\osw.txt"));

        // 一次读一个字符数据
        // int read()
//        int ch;
//        while ((ch=isr.read())!=-1){
//            System.out.print((char)ch);
//        }

        char[] chs = new char[1024];
        int len;
        while ((len=isr.read(chs))!=-1){
            System.out.println(new String(chs, 0, len));// 字符串的构造方法
        }
        isr.close();
    }
}
```



### 5.5 案例 复制java文件

```java
public class CopyJavaDemo {
    public static void main(String[] args) throws IOException {
        InputStreamReader isr = new InputStreamReader(new FileInputStream("src\\streamdemo\\OutputStreamWriterDemo.java"));
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("src\\OutputStreamWriterDemo.java"));

        char[] chs = new char[1024];
        int len;
        while ((len=isr.read(chs))!=-1){
            osw.write(chs, 0, len);
        }
        isr.close();
        osw.close();
    }
}
```

**改进版：**

![image-20230314155324946](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303141553011.png)

注意：

- 使用FileReader和FileWriter两个子类，可以方便书写。

- 如果在复制的过程中涉及到编码解码的问题，还是需要使用InputStreamReader和OutputStreamWriter。

- FileReader和FileWriter两个子类是InputStreamReader和OutputStreamWriter的子类。

```java
public class CopyJavaImproveDemo {
    public static void main(String[] args) throws IOException {
        FileReader fr = new FileReader("src\\streamdemo\\OutputStreamWriterDemo.java");
        FileWriter fw = new FileWriter("src\\OutputStreamWriterDemo.java");
        char[] chs = new char[1024];
        int len;
        while ((len=fr.read(chs))!=-1){
            fw.write(chs, 0, len);
        }
//        int ch;
//        while ((ch= fr.read())!=-1) {
//            fw.write(ch);
//        }
        fr.close();
        fw.close();
    }
}
```



### 5.6 字符缓冲流

- 字符缓冲流
  - BufferedWriter：将文本写入字符输出流，缓冲字符，以提供单个字符，数组和字符串的高效输入，可以指定缓冲区的大小，或者可以接受默认值大小。默认值足够大，可用于大多数用途
  - BufferedReader：从字符输入流读取文本，缓冲字符，已提供字符，数组和行的高效读取，可以指定缓冲区大小或者使用默认的缓冲区大小。默认值足够大，可用于大多数用途。
- 构造方法
  - BufferedWriter(Writer out)
  - BufferedReader(Reader in)

- 代码实例

  ```java
  public class BufferedStramDemo01 {
      public static void main(String[] args) throws IOException {
  //        FileWriter fw = new FileWriter("fw.txt");
  //        BufferedWriter bw = new BufferedWriter(fw);
  
  //        BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));
  //        bw.write("hello\r\n");
  //        bw.write("world\r\n");
  //        bw.close();
  
          BufferedReader br = new BufferedReader(new FileReader("bw.txt"));
  
          // 一次读一个字符数据
  //        int ch;
  //        while ((ch=br.read())!=-1){
  //            System.out.print((char) ch);
  //        }
  
          char[] chs = new char[1024];
          int len;
          while ((len=br.read(chs))!=-1){
              System.out.println(new String(chs, 0, len));
          }
          br.close();
      }
  }
  ```

> 案例：复制java文件

![image-20230319135653069](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191357470.png)

```java
public class CopyJavaDemo {
    public static void main(String[] args) throws IOException {
        BufferedReader br =  new BufferedReader(new FileReader("src\\algorithm\\RadixSortDemo.java"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("src\\RadixSortDemo.java"));
        // 一次写一个字符数据
//        int ch;
//        while ((ch= br.read())!=-1){
//            bw.write(ch);
//        }

        char[] chs = new char[1024];
        int len;
        while ((len=br.read(chs))!=-1){
//            bw.write(new String(chs, 0, len));
            bw.write(chs, 0, len);
        }

        bw.close();
        br.close();

    }
}
```



> **字符缓冲流的特有功能**

- BufferedWriter：
  - void newLine()：写一行行分隔符，行分隔符字符串有系统属性定义；如果是windows系统，则是 \r\n，如果是linux ，则是 \n， 如果是mac，则是 \r。
- BufferedReader：
  - String readLine()：读一行文字，结果包含行的内容的字符串。不包括任何终止字符（即只读内容，不读换行符号），如果流的结尾已经到达，则为null。

```java
public class BufferedStreamDemo02 {
    public static void main(String[] args) throws IOException {
//        BufferedWriter bw = new BufferedWriter(new FileWriter("bww.txt"));
//
//        for (int i=0; i<10; i++){
//            bw.write("hello");
////            bw.write("\r\n");
//            bw.newLine();// 在内容末尾添加换行符
//            bw.flush();
//        }
//
//        bw.close();

        BufferedReader br =new BufferedReader(new FileReader("bww.txt"));
//        第一次读取数据
//        String line = br.readLine();
//        System.out.println(line);
//
//        line = br.readLine();
//        System.out.println(line);
//        line = br.readLine();
//        System.out.println(line);// null
//        line = br.readLine();
//        System.out.println(line);// null

        String line;
        while ((line=br.readLine())!=null){
//            System.out.print(line);// hello0hello1
            System.out.println(line);// 可以换行
        }
    }
}
```



案例代码：

```java
public class CopyDemo01 {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("src\\algorithm\\RadixSortDemo.java"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("src\\RadixSortDemo.java"));
        String line;
        while ((line=br.readLine())!=null){
            bw.write(line);
            bw.newLine();
            bw.flush();
        }

        br.close();
        bw.close();
    }
}
```



### 5.7 IO流小结

![image-20230319144014625](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191440785.png)

![image-20230319144127267](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191441433.png)

![image-20230319144140963](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191441023.png)

案例总结：集合到文件

```java
public class ArrayListStramDemo {
    public static void main(String[] args) throws IOException {
        ArrayList<String> arr = new ArrayList<>();
        arr.add("hello");
        arr.add("hwolrd");
        arr.add("java");

        BufferedWriter bw = new BufferedWriter(new FileWriter("arrbw.txt"));
        for (String s:arr){
            bw.write(s);
            bw.newLine();
            bw.flush();
        }
        bw.close();
    }
```



案例总结：文件到集合

```java
public class ArrayListStreamDemo02 {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("arrbw.txt"));
        ArrayList<String> arr = new ArrayList<>();
        String line;
        while ((line=br.readLine())!=null){
            arr.add(line);
        }
        br.close();

        for (String s: arr){
            System.out.println(s);
        }
    }
}
```



案例：点名器

```java
public class DianMingQi {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("bw.txt"));
        ArrayList<String> arr = new ArrayList<>();
        String line;
        while ((line=br.readLine())!=null){
            arr.add(line);
        }

        Random r = new Random();
        System.out.println(arr.get(r.nextInt(arr.size())));

    }
}
```

![image-20230319151316025](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191513100.png)

![image-20230319151332565](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191513652.png)

![image-20230319151418403](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191514481.png)

![image-20230319151850994](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191518092.png)

![image-20230319151907815](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191519897.png)

![image-20230319151934843](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191519926.png)

![image-20230319151952606](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191519703.png)

![image-20230319152004963](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191520042.png)
