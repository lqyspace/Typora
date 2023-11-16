# TreeSet与TreeMap实现自定义比较器

## 1. 常见集合

- 键值：Map集合等
- 单值存储：数组，List，Set，Queue集合等
- TreeMap，TreeSet等实现了排序的功能，默认是从小到大的排序，根据字典序排序等。
- TreeSet与TreeMap可以代表大部分集合实现自定义比较器的操作。

## 2. TreeMap默认排序与自定义比较器实现

> 测试以**Integer**为键类型的默认排序：根据键值从小到大排序

```java
package testtest;

import java.util.Map;
import java.util.TreeMap;

public class Test19 {
    static TreeMap<Integer, Student> integerMap = new TreeMap<>();
    public static void main(String[] args) {
        for(int i=10; i>0; i--){
            integerMap.put(i, new Student(i, "小王" + i));
        }
        for(Map.Entry<Integer, Student> entry: integerMap.entrySet()){
            System.out.println(entry.toString());
        }
    }
}
class Student{
    int age;
    String name;

    public Student(int age, String name) {
        this.age = age;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

输出：

```java
1=Student{age=1, name='小王1'}
2=Student{age=2, name='小王2'}
3=Student{age=3, name='小王3'}
4=Student{age=4, name='小王4'}
5=Student{age=5, name='小王5'}
6=Student{age=6, name='小王6'}
7=Student{age=7, name='小王7'}
8=Student{age=8, name='小王8'}
9=Student{age=9, name='小王9'}
10=Student{age=10, name='小王10'}
```



> 自定义比较器，根据键值从大到小进行排序

1、初始化TreeMap时实现匿名内部类Comparator，实现根据Integer键从大到小排序

```java
package testtest;

import java.util.Comparator;
import java.util.Map;
import java.util.TreeMap;

public class Test19_1 {
    // 匿名内部类实现比较器
    static TreeMap<Integer, TStudent> integerMap = new TreeMap<>(new Comparator<Integer>() {
        @Override
        public int compare(Integer o1, Integer o2) {
            return o2 - o1;// 从大到小
        }
    });
    public static void main(String[] args) {
        for(int i=1; i<11; i++){
            integerMap.put(i, new TStudent(i, "小王"+i));
        }
        for(Map.Entry<Integer, TStudent> entry: integerMap.entrySet()){
            System.out.println(entry.toString());
        }
    }
}

class TStudent{
    int age;
    String name;

    public TStudent(int age, String name) {
        this.age = age;
        this.name = name;
    }

    @Override
    public String toString() {
        return "TStudent{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

输出：

```java
10=TStudent{age=10, name='小王10'}
9=TStudent{age=9, name='小王9'}
8=TStudent{age=8, name='小王8'}
7=TStudent{age=7, name='小王7'}
6=TStudent{age=6, name='小王6'}
5=TStudent{age=5, name='小王5'}
4=TStudent{age=4, name='小王4'}
3=TStudent{age=3, name='小王3'}
2=TStudent{age=2, name='小王2'}
1=TStudent{age=1, name='小王1'}
```



2、初始化TreeMap时，将comparator的实现类进行传参，实现根据Integer从大到小排序；

```java
package testtest;

import java.util.Map;
import java.util.TreeMap;

public class Test19_2 {
    static MyCompare com = new MyCompare();
    static TreeMap<Integer, Student> integerMap = new TreeMap<>(com);// 传参
    public static void main(String[] args) {
        for(int i=1; i<11; i++){
            integerMap.put(i, new Student(i, "小王" + i));
        }
        for (Map.Entry<Integer, Student> entry: integerMap.entrySet()){
            System.out.println(entry.toString());
        }
    }
}
```

```java
package testtest;

import java.util.Comparator;
// 实现一个比较器
public class MyCompare implements Comparator<Object> {
    @Override
    public int compare(Object o1, Object o2) {
        Integer i1 = (Integer) o1;
        Integer i2 = (Integer) o2;
        return i1 > i2? -1: 1;// 从大到小
    }
}
```

输出：

```java
10=Student{age=10, name='小王10'}
9=Student{age=9, name='小王9'}
8=Student{age=8, name='小王8'}
7=Student{age=7, name='小王7'}
6=Student{age=6, name='小王6'}
5=Student{age=5, name='小王5'}
4=Student{age=4, name='小王4'}
3=Student{age=3, name='小王3'}
2=Student{age=2, name='小王2'}
1=Student{age=1, name='小王1'}
```



> 键为对象时，进行自定义排序

1、Student对象实现comparable接口的compareTo方法，根据age从大到小排序

```java
package testtest;

import java.util.Map;
import java.util.TreeMap;

public class Test19_3 {
    static TreeMap<MyStudent, Integer> studentIntegerTreeMap = new TreeMap<>();

    public static void main(String[] args) {
        for (int i=1; i<11; i++){
            studentIntegerTreeMap.put(new MyStudent(i, "小王"+i), i);
        }
        for (Map.Entry<MyStudent, Integer> entry: studentIntegerTreeMap.entrySet()){
            System.out.println(entry.toString());
        }
    }
}

class MyStudent implements Comparable<Object>{
    int age;
    String name;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "MyStudent{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public MyStudent(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public int compareTo(Object o){
        MyStudent ms = (MyStudent) o;
        return this.age < ms.age? 1: -1;// 从大到小排序
    }

}
```

输出：

```java
MyStudent{age=10, name='小王10'}=10
MyStudent{age=9, name='小王9'}=9
MyStudent{age=8, name='小王8'}=8
MyStudent{age=7, name='小王7'}=7
MyStudent{age=6, name='小王6'}=6
MyStudent{age=5, name='小王5'}=5
MyStudent{age=4, name='小王4'}=4
MyStudent{age=3, name='小王3'}=3
MyStudent{age=2, name='小王2'}=2
MyStudent{age=1, name='小王1'}=1
```



2、初始化时实现匿名内部类Comparator，实现根据age从大到小排序

```java
package testtest;

import java.util.Comparator;
import java.util.Map;
import java.util.TreeMap;

public class Test19_4 {
    public static TreeMap<Student, Integer> studentMap = new TreeMap<>(new Comparator<Student>() {
        @Override
        public int compare(Student o1, Student o2) {
            Student s1 = (Student) o1;
            Student s2 = (Student) o2;
            return s1.age < s2.age? 1: -1; // 从大到小排序
        }
    });

    public static void main(String[] args) {
        for (int i=1; i<11; i++){
            studentMap.put(new Student(i, "小王"+i), i);
        }
        for(Map.Entry<Student, Integer> entry: studentMap.entrySet()){
            System.out.println(entry.toString());
        }
    }
}
```

输出：

```java
Student{age=10, name='小王10'}=10
Student{age=9, name='小王9'}=9
Student{age=8, name='小王8'}=8
Student{age=7, name='小王7'}=7
Student{age=6, name='小王6'}=6
Student{age=5, name='小王5'}=5
Student{age=4, name='小王4'}=4
Student{age=3, name='小王3'}=3
Student{age=2, name='小王2'}=2
Student{age=1, name='小王1'}=1
```



3、初始化TreeMap时，将Comparator的实现类进行传参，实现根据Integer从大到小排序

```java
package testtest;

import java.util.Comparator;
import java.util.Map;
import java.util.TreeMap;

public class Test19_5 {
    static MyCompare1 myCompare1 = new MyCompare1();
    static TreeMap<Student, Integer> studnetMap = new TreeMap<>(myCompare1);
    public static void main(String[] args) {
        for(int i=1; i<11; i++){
            studnetMap.put(new Student(i, "小王"+i), i);
        }
        for(Map.Entry<Student, Integer> entry: studnetMap.entrySet()){
            System.out.println(entry.toString());
        }
    }

}
class MyCompare1 implements Comparator<Object>{
    @Override
    public int compare(Object o1, Object o2) {
        Student s1 = (Student) o1;
        Student s2 = (Student) o2;
        return s1.age < s2.age? 1: -1;// 从大到小
    }
}

```

输出：

```java
Student{age=10, name='小王10'}=10
Student{age=9, name='小王9'}=9
Student{age=8, name='小王8'}=8
Student{age=7, name='小王7'}=7
Student{age=6, name='小王6'}=6
Student{age=5, name='小王5'}=5
Student{age=4, name='小王4'}=4
Student{age=3, name='小王3'}=3
Student{age=2, name='小王2'}=2
Student{age=1, name='小王1'}=1
```



## 3. TreeSet的默认比较器与自定义比较器

TreeSet的底层实现相似于TreeMap。



## 4. 总结

1、当要排序的对象实现目标为基本类型时

- 实现匿名内部类Comparator，作为集合的构造参数
- 实现Comparator的实现类作为集合的构造参数

2、当要排序的对象实现目标为对象时

- 实现匿名内部类Comparator，作为集合的构造参数

- 实现Comparator的实现类作为集合的构造参数

  ```java
  class MyCompare1 implements Comparator<Object>{
      @Override
      public int compare(Object o1, Object o2) {
          Student s1 = (Student) o1;
          Student s2 = (Student) o2;
          return s1.age < s2.age? 1: -1;// 从大到小
      }
  }
  ```

- 对象实现comparable接口的compareTo方法

  ```java
  package testtest;
  
  import java.util.Map;
  import java.util.TreeMap;
  
  public class Test19_3 {
      static TreeMap<MyStudent, Integer> studentIntegerTreeMap = new TreeMap<>();
  
      public static void main(String[] args) {
          for (int i=1; i<11; i++){
              studentIntegerTreeMap.put(new MyStudent(i, "小王"+i), i);
          }
          for (Map.Entry<MyStudent, Integer> entry: studentIntegerTreeMap.entrySet()){
              System.out.println(entry.toString());
          }
      }
  }
  
  class MyStudent implements Comparable<Object>{
      int age;
      String name;
  
      public int getAge() {
          return age;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      @Override
      public String toString() {
          return "MyStudent{" +
                  "age=" + age +
                  ", name='" + name + '\'' +
                  '}';
      }
  
      public MyStudent(int age, String name) {
          this.age = age;
          this.name = name;
      }
  
      public int compareTo(Object o){
          MyStudent ms = (MyStudent) o;
          return this.age < ms.age? 1: -1;// 从大到小排序
      }
  
  }
  ```
