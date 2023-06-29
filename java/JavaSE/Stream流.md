# Stream流

## 1.1 体验Stream流

![image-20230618093002322](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306180930376.png)

```java
public class StreamTest1 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>(6);
        list.add("林青霞");
        list.add("柳岩");
        list.add("张曼玉");
        list.add("王祖贤");
        list.add("张敏");
        list.add("张无忌");

        ArrayList<String> zhangList = new ArrayList<>();
        for (String s: list){
            if (s.startsWith("张"))
                zhangList.add(s);
        }

        System.out.println(zhangList);

        ArrayList<String> threeList = new ArrayList<>();
        for (String s: zhangList){
            if (s.length() == 3)
                threeList.add(s);
        }

        for (String s: threeList)
            System.out.println(s);

        System.out.println("---------------------");
        // 使用Stream流进行改进
//        list.stream().filter(s->s.startsWith("张")).filter(s->s.length()==3).forEach(s->System.out.println(s));
        list.stream().filter(s->s.startsWith("张")).filter(s->s.length()==3).forEach(System.out::println);
    }
}
```

注意：**Stream流通常是和lambda表达式一起用的**

- Stream流的好处

  直接阅读代码的字面意思既可以完美的展示无关逻辑方式的语义：获取流，过滤姓张，过滤长度为3，逐一打印

  Stream流把真正的函数式编程风格引入到了java中



## 1.2 Stream流的常见生成方式

- Stream流的思想

  ![image-20230618094432597](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306180944644.png)

- Stream流的使用

  - 生成流

    通过数据源（集合，数组等）生成流

    比如：list.stream()

  - 中间操作

    一个流后面可以跟随零个或多个中间操作，其目的主要是打开流，做出某种程度的数据过滤/映射,然后返回一个新的流，交给下一个操作使用

    比如：filter()

  - 终结操作

    一个流只能有一个终结操作，当这个操作执行后，流就被使用光了，无法再被操作。所以必定是流的最后一个操作

    forEach()



> **Stream流的常见生成方式**

- Collection体系的集合可以使用默认方法stream()生成流
  - `default Stream<E> stream()`
- Map体系的集合间接的生成流
  - 把Map转为Set集合，间接的生成流
- 数组
  - 通过Stream接口的静态方法of(T... values)生成流（传入的是可变参数，当然可以传入数组）



```java
public class StreamTest2 {
    public static void main(String[] args) {
        //Collection体系生成流的方式
        List<String> list = new ArrayList<>();
        Stream<String> listStream = list.stream();

        Set<String> set = new HashSet<>();
        Stream<String> setStream = set.stream();

        // Map体系集合生成流的方式
        Map<String, Integer> map = new HashMap<>();
        Stream<String> mapStream = map.keySet().stream();
        Stream<Integer> valuesStream = map.values().stream();
        Stream<Map.Entry<String, Integer>> entryStream = map.entrySet().stream();

        //数组可以通过Stream接口的of(T... values)生成流
        String[] strArr = {"hello", "world", "java"};
        Stream<String> strArrStream = Stream.of(strArr);
        Stream<String> strArrStream2 = Stream.of("hello", "world", "java");
        Stream<Integer> intStream = Stream.of(100,200,300);
    }
}
```



## 1.3 Stream流的中间操作

- 概念

  中间操作的意思是，执行完此方法以后，Stream流依然可以继续执行其他的操作

- 常见方法

  | 方法名                                   | 说明                                                        |
  | ---------------------------------------- | ----------------------------------------------------------- |
  | Stream filter(Predicate predicate)       | 用于对流中的数据进行过滤                                    |
  | Stream limit(long maxSize)               | 返回此流中的元素组成的流，截取前指定参数个数的数据          |
  | Stream skip(long n)                      | 跳过指定参数个数的数据，返回由该流剩余元素组成的流          |
  | static Stream concat(Stream a, Stream b) | 合并a和b两个流为一个流                                      |
  | Stream distinct()                        | 返回由该流的不同元素（根据Object.equals(Object))组成的流    |
  | Stream sorted()                          | 返回由此流的元素组成的流，根据自然顺序排序                  |
  | Stream sorted(Comparator comparator)     | 返回由该流的元素组成的流，根据提供的Comparator进行排序      |
  | Stream map(Function mapper)              | 返回由给定函数应用于此流的元素的结果组成的流                |
  | IntStream mapToInt(ToIntFunction mapper) | 返回一个IntStream，其中包含将给定函数应用于此流的元素的结果 |

- filter代码演示

  ```java
  public class Stream3 {
      public static void main(String[] args) {
          ArrayList<String> list = new ArrayList<>();
          list.add("林青霞");
          list.add("柳岩");
          list.add("张曼玉");
          list.add("王祖贤");
          list.add("张敏");
          list.add("张无忌");
  
          // 需求1：把list中张字开头的在控制台输出
          list.stream().filter(s->s.startsWith("张")).forEach(System.out::println);
          System.out.println("-----------------------");
  
          // 需求2：把list集合中长度为3的元素在控制台输出
          list.stream().filter(s->s.length()==3).forEach(System.out::println);
          System.out.println("-----------------------");
  
          // 需求3：把list集合中以张开头的，长度为3的元素在控制台输出
          list.stream().filter(s->s.startsWith("张")).filter(s->s.length()==3).forEach(System.out::println);
      }
  }
  ```

- limit&skip代码演示

  ```java
  public class StreamTest3 {
      public static void main(String[] args) {
          ArrayList<String> list = new ArrayList<>();
          list.add("林青霞");
          list.add("柳岩");
          list.add("张曼玉");
          list.add("王祖贤");
          list.add("张敏");
          list.add("张无忌");
  
          // 取前3个数据在控制台输出
          list.stream().limit(3).forEach(System.out::println);
          System.out.println("---------------------------");
  
          // 跳过3个元素，把剩下的元素在控制台输出
          list.stream().skip(3).forEach(System.out::println);
  
          System.out.println("---------------------------");
          // 跳过2个元素，把剩下的元素中前2个在控制台输出
          list.stream().skip(2).limit(2).forEach(System.out::println);
      }
  }
  ```

  输出：

  ```java
  林青霞
  柳岩
  张曼玉
  ---------------------------
  王祖贤
  张敏
  张无忌
  ---------------------------
  张曼玉
  王祖贤
  ```

  skip：**实则是跳过前几个元素**

- concat&distinct代码演示

  ```java
  public class StreamTest4 {
      public static void main(String[] args) {
          ArrayList<String> list = new ArrayList<>();
          list.add("林青霞");
          list.add("柳岩");
          list.add("张曼玉");
          list.add("王祖贤");
          list.add("张敏");
          list.add("张无忌");
  
          // 需求1 取前4个组成一个流
          Stream<String> s1 = list.stream().limit(4);
  
          // 需求2 跳过2个元素组成一个流
          Stream<String> s2 = list.stream().skip(2);
  
          // 需求3 合并需求1和需求2的流，并在控制台输出结果
  //        Stream.concat(s1, s2).forEach(System.out::println);
  
          // 需求4 在控制台输出需求3的结果，要求字符串不能重复
          Stream.concat(s1,s2).distinct().forEach(System.out::println);
  
      }
  }
  ```

  注：**如果在使用流的时候使用了终止操作，那么之后再使用这个流的时候就会报错**

- sorted代码演示

  ```java
  public class StreamTest5 {
      public static void main(String[] args) {
          ArrayList<String> arr = new ArrayList<>();
          arr.add("linqingxia");
          arr.add("zhangmanyu");
          arr.add("wangzuxian");
          arr.add("liuyan");
          arr.add("zhangmin");
          arr.add("zhangwuji");
  
          // 需求1 按照字母顺序把数据输出在控制台上
          arr.stream().sorted().forEach(System.out::println);
  
          System.out.println("---------------------");
          // 需求2 按照字符串长度把数据输出在控制台上
          arr.stream().sorted(new Comparator<String>() {
              @Override
              public int compare(String o1, String o2) {
                  return o1.length() - o2.length();
              }
          }).forEach(System.out::println);
  
          System.out.println("--------------------------");
  //        arr.stream().sorted((s1, s2)->s1.length() - s2.length()).forEach(System.out::println);
          System.out.println("-------------------------------");
          arr.stream().sorted((s1, s2)->{
              int num1 = s1.length() - s2.length();
              int num2 = num1==0? s1.compareTo(s2):num1;
              return num2;
          }).forEach(System.out::println);
      }
  }
  ```

  输出：

  ```java
  linqingxia
  liuyan
  wangzuxian
  zhangmanyu
  zhangmin
  zhangwuji
  ---------------------
  liuyan
  zhangmin
  zhangwuji
  linqingxia
  zhangmanyu
  wangzuxian
  --------------------------
  -------------------------------
  liuyan
  zhangmin
  zhangwuji
  linqingxia
  wangzuxian
  zhangmanyu
  ```

- map和mapToInt代码演示

  ```java
  public class StreamTest6 {
      public static void main(String[] args) {
          ArrayList<String> m = new ArrayList<>();
          m.add("10");
          m.add("20");
          m.add("30");
          m.add("40");
          m.add("50");
          m.add("60");
  
          // 需求 将集合中的字符串数据转换为整数后在控制台输出
  //        <R> Stream<R> map(Function<? super T,? extends R> mapper)
          // Function接口中的方法    R apply(T t)
          
  //        m.stream().map(s->Integer.parseInt(s)).forEach(System.out::println);
  //        m.stream().map(Integer::parseInt).forEach(System.out::println);
  
  //        IntStream mapToInt(ToIntFunction<? super T> mapper)
          // IntStream 表示原始int流
          // ToIntFunction接口中的方法 int applyAsInt(T value)
          
  //        m.stream().mapToInt(Integer::parseInt).forEach(System.out::println);
          int sum = m.stream().mapToInt(Integer::parseInt).sum();
          System.out.println(sum);
      }
  }
  ```
  
  注：`sum`这个函数在 `IntStream`接口中才有

**总结：**

1、**filter**：根据条件过滤，筛选

2、**limit**：获取前几个元素；**skip**：跳过前几个元素

3、**concat**：合并两个流的元素；**distinct**：对流中的元素进行去重

4、**sorted**：对流中的元素进行排序，默认是自然排序，也可以自定义比较器排序

5、**map**：主要是将一个函数映射到流中的元素

6、**mapToInt**：通过映射，将流中的元素映射为 IntStream 类型



## 1.4 Stream流的终结操作

- 概念

  终结操作的意识是，执行完此方法以后，Stream流将不再执行其他的操作

- 常见方法

  | 方法名                        | 说明                     |
  | ----------------------------- | ------------------------ |
  | void forEach(Consumer action) | 对此流的每个元素执行操作 |
  | long count()                  | 返回此流中的元素数       |

- 代码演示

  ```java
  public class StreamDemo {
      public static void main(String[] args) {
          //创建一个集合，存储多个字符串元素
          ArrayList<String> list = new ArrayList<String>();
          
          list.add("林青霞");
          list.add("张曼玉");
          list.add("王祖贤");
          list.add("柳岩");
          list.add("张敏");
          list.add("张无忌");
          //需求1：把集合中的元素在控制台输出
          // list.stream().forEach(System.out::println);
          
          //需求2：统计集合中有几个以张开头的元素，并把统计结果在控制台输出
          long count = list.stream().filter(s -> s.startsWith("张")).count();
          System.out.println(count);
      }
  }
  ```

  



## 1.5 Stream流的综合应用

![image-20230618141705055](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306181417105.png)

```java
public class StreamTest7 {
    public static void main(String[] args) {
        //创建集合
        ArrayList<String> manList = new ArrayList<String>();
        manList.add("周润发");
        manList.add("成龙");
        manList.add("刘德华");
        manList.add("吴京");
        manList.add("周星驰");
        manList.add("李连杰");

        ArrayList<String> womanList = new ArrayList<String>();
        womanList.add("林心如");
        womanList.add("张曼玉");
        womanList.add("林青霞");
        womanList.add("柳岩");
        womanList.add("林志玲");
        womanList.add("王祖贤");

        // 男演员只要名字为三个字的前三人
//        Stream<String> s1 = manList.stream().filter(s->s.length()==3).limit(3);
        // 女演员只要姓林的，并且不要第一个
//        Stream<String> s2 = womanList.stream().filter(s->s.startsWith("林")).skip(1);
        // 合并
//        Stream<String> s3 = Stream.concat(s1,s2);
        // 将上一步操作后的元素作为构造方法的参数创建演员对象，遍历数组
//        s3.map(Actor::new).forEach(a-> System.out.println(a.getName()));
//        s3.map(Actor::new).forEach(System.out::println);

        // 接下来将以上的全部整合在一块
        Stream.concat(manList.stream().filter(name->name.length()==3).limit(3),
                womanList.stream().filter(name->name.startsWith("林")).skip(1))
                .map(Actor::new).forEach(p-> System.out.println(p.getName()));
    }
}
```

输出：

```java
周润发
刘德华
周星驰
林青霞
林志玲
```





## 1.6 Stream流的收集操作

- 概念

  对数据使用Stream流的方式操作完毕以后，可以把流中的数据收集到集合中。

- 常用方法

  | 方法名                         | 说明               |
  | ------------------------------ | ------------------ |
  | R collect(Collector collector) | 把结果收集到集合中 |

- 工具类Collectors提供了具体的收集方式

  | 方法名                                                       | 说明                   |
  | ------------------------------------------------------------ | ---------------------- |
  | public static Collector toList()                             | 把元素收集到List集合中 |
  | public static Collector toSet()                              | 把元素收集到Set集合中  |
  | public static Collector toMap(Function keyMapper, Function valueMapper) | 把元素收集到Map集合中  |

- 代码演示

  ```java
  public class StreamTest8 {
      public static void main(String[] args) {
          //创建List集合对象
          List<String> list = new ArrayList<String>();
          list.add("林青霞");
          list.add("张曼玉");
          list.add("王祖贤");
          list.add("柳岩");
  
          // 得到名字长度为3的流
          Stream<String> listSrteam = list.stream().filter(name->name.length()==3);
          // 整合到List集合中
          List<String> names = listSrteam.collect(Collectors.toList());
  
          for (String name: names){
              System.out.println(name);
          }
          System.out.println("--------------------------");
  
          Set<Integer> set = new HashSet<Integer>();
          set.add(10);
          set.add(20);
          set.add(30);
          set.add(33);
          set.add(35);
  
          // 大于25
          Stream<Integer> setStream = set.stream().filter(age->age>25);
          // 收集到Set中并遍历
          Set<Integer> ages = setStream.collect(Collectors.toSet());
          for (Integer i: ages)
              System.out.println(i);
  
          System.out.println("--------------------------");
  
          // 定义一个字符数组，每一个字符数组有姓名和年龄组成
          String[] strArray = {"林青霞,30", "张曼玉,35", "王祖贤,33", "柳岩,25"};
          // 得到年龄大于28的流
          Stream<String> arrStream = Stream.of(strArray).filter(s->Integer.parseInt(s.split(",")[1])>28);
  
          // 把使用Stream流操作完毕的数据收集到Maap集合中并遍历，字符串中的姓名作键，年龄作值
          Map<String, Integer> map = arrStream.collect(Collectors.toMap(s->s.split(",")[0],
                  s->Integer.parseInt(s.split(",")[1])));
  
          Set<String> keySet = map.keySet();
          for (String key: keySet){
              Integer val = map.get(key);
              System.out.println(key + "," + val);
          }
      }
  }
  ```

  输出：

  ```java
  林青霞
  张曼玉
  王祖贤
  --------------------------
  33
  35
  30
  --------------------------
  林青霞,30
  王祖贤,33
  张曼玉,35
  ```

  



























