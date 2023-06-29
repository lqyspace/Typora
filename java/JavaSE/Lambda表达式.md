# Lambda

## 1.1 体验Lambda表达式

- 案例需求

  启动一个线程类，在控制台输出一句话：多线程启动了

- 实现方式一

  - 实现步骤
    - 定义一个类MyRunnable实现Runnable接口，重写run() 方法
    - 创建MyRunnable类的对象
    - 创建一个Thread类的对象，把MyRunnable的对象作为构造参数传递
    - 启动线程

- 实现方式二

  - 匿名内部类的方式改进

- 实现方式三

  - Lambda表达式的方式改进

- 代码

  ```java
  //方式一的线程类
  public class MyRunnable implements Runnable {
      @Override
      public void run() {
      System.out.println("多线程程序启动了");
      }
  }
  
  public class LambdaDemo {
      public static void main(String[] args) {
          //方式一
          // MyRunnable my = new MyRunnable();
          // Thread t = new Thread(my);
          // t.start();
          //方式二
          // new Thread(new Runnable() {
          // @Override
          // public void run() {
          // System.out.println("多线程程序启动了");
          // }
          // }).start();
          //方式三
          new Thread( () -> {
          System.out.println("多线程程序启动了");
          } ).start();
      }
  }
  ```



## 1.2 Lambda表达式的标准格式

![image-20230616204643378](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306162046501.png)

![image-20230616204734457](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306162047525.png)



![image-20230616205631267](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306162056319.png)

```java
//接口
public interface Eatable {
	void eat();
}
//实现类
public class EatableImpl implements Eatable {
    @Override
    public void eat() {
    	System.out.println("一天一苹果，医生远离我");
    }
}
//测试类
public class EatableDemo {
    public static void main(String[] args) {
        //在主方法中调用useEatable方法
        Eatable e = new EatableImpl();// 因为抽象类无法实例化，但是可以用它的子类进行实例化
        useEatable(e);
        
		// 匿名内部类
        useEatable(new Eatable() {
            @Override
            public void eat() {
                System.out.println("一天一苹果，医生远离我");
            }
        });
        
		// Lambda表达式
        useEatable(()->{
            System.out.println("依吞吃苹果");
        });
    }
    private static void useEatable(Eatable e){
        e.eat();
    }
}
```

![image-20230616210813685](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306162108743.png)

```java
public class FlyDemo {
    public static void main(String[] args) {
        // 在主方法调用useFlyable
        // 匿名内部类
        useFlyable(new Flyable() {
            @Override
            public void fly(String s) {
                System.out.println(s);
                System.out.println("飞机自驾游");
            }
        });

        System.out.println("------------------");

        // lambda
        useFlyable((String s)->{
            System.out.println(s);
            System.out.println("轮船");
        });
        System.out.println("-------------------");
        Flyable f = new FlyImp();
        useFlyable(f);
    }
    private static void useFlyable(Flyable fly){
        fly.fly("风和日丽，鸟语花香");
    }
}
```

输出：

```
风和日丽，鸟语花香
飞机自驾游
------------------
风和日丽，鸟语花香
轮船
-------------------
风和日丽，鸟语花香我要飞得更高
```

![image-20230616210950416](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306162109462.png)

```java
public interface AddApple {
    int add(int x, int y);
}

public class AddImpl implements AddApple{
    @Override
    public int add(int x, int y) {
        return x+y;
    }
}


public class AddAppleDemo {
    public static void main(String[] args) {
        // 在主方法中调用useAddApple方法
        useAddApple((int x, int y)->{
            return x+y;
        });

        // 匿名内部类
        useAddApple(new AddApple() {
            @Override
            public int add(int x, int y) {
                return x+y;
            }
        });

        // 实现类
        AddApple a = new AddImpl();
        useAddApple(a);
    }

    private static void useAddApple(AddApple a){
        int sum = a.add(10, 20);
        System.out.println(sum);
    }
}
```

输出：

```java
30
30
30
```



![image-20230616212651817](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306162126865.png)

- 省略规则
  - 参数类型可以省略，但是有多个参数的情况下，不能只省略一个
  - 如果参数有且仅有一个，那么小括号可以省略
  - 如果代码块的语句只有一条，可以省略大括号和分号，和return关键字（大括号和分号省略后，如果有return，return也必须省略掉）

```java
public interface AddApple {
    int add(int x, int y);
}
public interface FlyAble {
    void fly(String s);
}

public class LambdaDemo {
    public static void main(String[] args) {
        useAdd((int x, int y)->{
            return x+y;
        });
        // 参数类型可以省略
        useAdd((x, y)->{
            return x+y;
        });
        System.out.println("------------------");
        useFly((String s)->{
            System.out.println(s);
            System.out.println("是你吗？");
        });
        // 如果参数仅有一个，小括号也可以省略
        useFly(s -> {
            System.out.println(s);
            System.out.println("是我呀？");
        });

        System.out.println("------------------------");

        // 如果代码块的语句只有一条，那么可以省略大括号和分号
        useFly(s -> System.out.println(s+" hello!"));
        // 如果代码块的语句只有一条，那么可以省略大括号和分号，如果有return，return也要省略
        useAdd((x, y)-> x+y);
    }

    private static void useFly(FlyAble f){
        f.fly("我是一只小小小鸟");
    }
    private static void useAdd(AddAble a){
        int sum = a.add(10,30);
        System.out.println(sum);
    }
}
```

输出：

```java
40
40
------------------
我是一只小小小鸟
是你吗？
我是一只小小小鸟
是我呀？
------------------------
我是一只小小小鸟 hello!
40
```



## 1.7 Lambda表达式的注意事项

- 使用Lambda表达式必须要**有接口**，并且要求接口中**有且仅有一个抽象方法**

- 必须要有上下文环境，才能推导出Lambda对应的接口

  - 根据**局部变量**的赋值得知Lambda对应的接口

    Runnable r = () -> System.out.println("Lambda表达式");

  - 根据**调用方法的参数**得知Lambda对应的接口

    new Thread(()->System.out.println("Lambda表达式")).start();

```java
public class LambdaDemo {
    public static void main(String[] args) {
        useInter(()->{
            System.out.println("你是谁？");
        });
        // 使用Lambda必须要有接口，并且要求接口中有且仅有一个抽象方法
        useInter(()-> System.out.println("你是谁？"));

        // 必须要有上下文环境，才能推导出Lambda对应的接口
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("匿名内部类");
            }
        }).start();

        Runnable r = ()-> System.out.println("匿名内部类");
        new Thread(r).start();

        new Thread(()-> System.out.println("Lambda表达式！")).start();

    }

    public static void useInter(Inter inter){
        inter.show();
    }
}
```



## 1.8 Lambda表达式和匿名内部类的区别

```java
public interface Animal {
    void method();
//    void show();
}

public abstract class Inter {
    public abstract void show();
}

public class Student {
    public void study(){
        System.out.println("爱生活，爱Java");
    }
}

public class LambdaDemo {
    public static void main(String[] args) {
        // 匿名内部类
        useAnimal(new Animal() {
            @Override
            public void method() {
                System.out.println("接口");
            }
        });
//        useInter(new Inter() {
//            @Override
//            public void show() {
//                System.out.println("抽象类");
//            }
//        });
//        useStudent(new Student(){
//            @Override
//            public void study() {
//                System.out.println("具体类");
//            }
//        });
//
//        System.out.println("-------------------------");
        //lambda
//        useAnimal(()-> System.out.println("接口"));
//        useInter(()-> System.out.println("抽象类"));// 必须是接口
//        useStudent(()-> System.out.println("具体类"));// 必须是接口
//        useAnimal(new Animal() {
//            @Override
//            public void method() {
//                System.out.println("1");
//            }
//
//            @Override
//            public void show() {
//                System.out.println(2);
//            }
//        });
    }

    private static void useStudent(Student s){
        s.study();
    }

    private static void useInter(Inter i){
        i.show();
    }

    private static void useAnimal(Animal a){
        a.method();
    }
}
```

- 所需类型不同
  - 匿名内部类：可以是接口，也可以是抽象类，还可以是具体类
  - Lambda表达式：只能是接口
- 使用限制不同
  - 如果接口中有且仅有一个抽象方法，可以使用Lambda表达式，也可以使用匿名内部类
  - 如果接口中多于一个抽象方法，只能使用匿名内部类，而不能使用Lanbda表达式
- 实现原理不同
  - 匿名内部类：编译之后，会产生一个单独的.class字节码文件
  - Lambda表达式：编译之后，没有一个单独的.class字节码文件。对应的字节码会在运行的时候动态生成



# 接口的组成更新

## 1.1 接口的组成更新

- 常量
  - public static final
- 抽象方法
  - public abstract
- Java8 以后加入了 默认方法、静态方法
- Java9 以后加入了 私有方法



## 1.2 接口中的默认方法

- 格式

  public default 返回值类型 方法名(参数列表){}

- 范例

  ```java
  public default void show(){
      System.out.println("show3");
  }
  ```

- 注意事项

  - 默认方法不是抽象方法，所以不强制被重写，但是也可以被重写，重写的时候需要把default关键字去掉
  - public 可以省略，default不能被省略
  - 当默认方法被重写后，调用的就是重写的方法

```java
public interface MyInterface {
    void show1();
    void show2();
//    public default void show3(){
//        System.out.println("show3");
//    }
    default void show3(){
        System.out.println("show3");
    }
}

public class MyInterfaceImplOne implements MyInterface{
    @Override
    public void show1() {
        System.out.println("One show1");
    }

    @Override
    public void show2() {
        System.out.println("One show2");
    }

    @Override
    public void show3() {
        System.out.println("One show3");
    }
}

public class MyInterfaceDemo {
    public static void main(String[] args) {
        MyInterface mi = new MyInterfaceImplOne();
        mi.show1();
        mi.show2();
        mi.show3();
    }
}
```

输出：

```java
One show1
One show2
One show3
```



## 1.3 接口中的静态方法

- 格式

  public static 返回值类型 方法名(参数列表){}

- 范例

  ```java
  public static void show(){
      System.out.println("");
  }
  ```

- 注意事项

  - 静态方法只能被接口调用，不能通过实现类名或者对象名调用
  - public可以省略，但是static不能被省略

- 代码

  ```java
  public interface Flyable {
      static void test(){
          System.out.println("Flyable 中的静态方法被调用了");
      }
  }
  
  public interface Inter {
      void show();
      default void method(){
          System.out.println("Inter 中的默认方法被执行了");
      }
      public static void test(){
          System.out.println("Inter 中的静态方法被执行了");
      }
  }
  
  public class InterImpl implements Inter, Flyable{
      @Override
      public void show() {
          System.out.println("Inter 中的show方法执行了");
      }
  }
  
  public class InterDemo {
      public static void main(String[] args) {
          Inter i = new InterImpl();
          i.show();
          i.method();
          // i.test() 静态方法只能被接口调用
           // InterImpl.test() 不能被实现类调用
          Inter.test();
          Flyable.test();
  
      }
  }
  ```

  输出：

  ```java
  Inter 中的show方法执行了
  Inter 中的默认方法被执行了
  Inter 中的静态方法被执行了
  Flyable 中的静态方法被调用了
  ```

  

## 1.4 接口中的私有方法

- 私有方法产生的原因

  Java9中新增了带方法体的私有方法，这其实在Java8中就已经埋下伏笔：Java8允许接口中定义带方法体的默认方法和静态方法。这样可能会导致一个问题，当两个默认方法或者静态方法包含一段相同的代码实现时，程序必然考虑将这段代码抽取为一个共性方法，而这个共性方法是不需要给别人使用的，因此就需要把这个共性方法给私有化，因此这是Java9中新增私有方法的必然性。

- 定义格式

  - 格式1

    private 返回值类型 方法名(参数列表){}

  - 范例1

    ```java
    private void show(){}
    ```

  - 格式2

    private static 返回值类型 方法名(参数列表){}

  - 范例2

    ```java
    private static void method(){}
    ```

- 注意事项

  - 默认方法可以调用私有的**静态方法**和**非静态方法**
  - 静态方法可以调用**私有的**静态方法和**非私有的**静态方法

```java
public interface Inter {
    default void show1(){
        System.out.println("show1开始执行");
//        System.out.println("初级工程师");
//        System.out.println("中级工程师");
//        System.out.println("高级工程师");
        show();
        System.out.println("show1执行结束");
    }

    default void show2(){
        System.out.println("show2开始执行");
//        System.out.println("初级工程师");
//        System.out.println("中级工程师");
//        System.out.println("高级工程师");
//        show();
        method();
        System.out.println("show2执行结束");
    }

    private void show(){
//        System.out.println("初级工程师");
//        System.out.println("中级工程师");
//        System.out.println("高级工程师");
        method();
    }
    private static void method(){
        System.out.println("初级工程师");
        System.out.println("中级工程师");
        System.out.println("高级工程师");
    }
    static void method1(){
//        System.out.println("method1开始执行");
////        System.out.println("初级工程师");
////        System.out.println("中级工程师");
////        System.out.println("高级工程师");
//        method();
//        System.out.println("method1执行结束");
        method2();
    }
    static void method2(){
        System.out.println("method2开始执行");
//        System.out.println("初级工程师");
//        System.out.println("中级工程师");
//        System.out.println("高级工程师");
        method();
        System.out.println("method2执行结束");
    }
}

public class InterImpl implements Inter{
}

public class InterDemo {
    public static void main(String[] args) {
        Inter i = new InterImpl();
        i.show2();
        i.show1();
        Inter.method1();
        Inter.method2();
    }
}
```





# 方法引用

## 1.1 体验方法引用

- 方法引用出现的原因

  在使用Lambda表达式的时候，我们实际上传递进去的代码就是一种解决方案：拿参数做操作

  那么考虑一种情况：如果我们在Lambda中所指定的操作方案，在其他的地方存在相同的方案，那么是否还有必要再写重复的逻辑呢？答案肯定是没有必要的

  那么我们又如何使用已经存在的方案呢？

  这就是接下来要使用的方法引用

- 代码演示

  ```java
  public interface Printable {
      void printString(String s);
  }
  
  public class PrintabDemo {
      public static void main(String[] args) {
  //        usePrintable((s)->{
  //            System.out.println(s);
  //        });
          usePrintable(s -> System.out.println(s));
  
          System.out.println("爱生活爱Java");
  
          // 方法引用符：::
          // 原本接口的方法应该传入一个字符串s，但是我们可以根据这个方法的参数
          // 可以推导出这个方法需要的是一个接口，接口里面只有一个抽象方法，抽象方法只有一个
          // 参数s，因此可以推导出这个参数s是要传递给 System.out 对象下的 println 方法
          // 方法引用实际上是将 s 这个参数传递给 println 方法，只是你没有看到而已
          // 因此可以遵循：可推导的就是可省略
          usePrintable(System.out::println);
      }
  
      private static void usePrintable(Printable p){
          p.printString("爱生活爱Java");
      }
  }
  ```

  > **方法引用符：::**
  > 原本接口的方法应该传入一个字符串s，但是我们可以根据usePrintable这个方法的参数可以推导出这个方法需要的是一个接口，接口里面只有一个抽象方法，抽象方法只有一个参数s，因此可以推导出这个参数s是要传递给 System.out 对象下的 println 方法
  >
  > 方法引用实际上是将 s 这个参数传递给 println 方法，只是你没有看到而已
  >
  > 遵循：可推导的就是可省略



## 1.2 方法引用符

方法引用符：`::`

该符号为引用运算符，而它所在的表达式称为方法引用

> 回顾一下我们在体验方法引用中的代码

- Lambda表达式：usePrintable(s -> System.out.println(s));

  分析：拿到参数s之后通过Lambda表达式，传递给System.out.println方法去处理

- 方法引用：usePrintable(System.out::println);

  分析：直接使用System.out中的println方法来取代Lambda，代码更加简洁



推导与省略

- 如果使用Lambda，那么根据 ”可推导就是可省略“ 的原则，无需指定参数类型，也无需指定重载形式，它们都将被自动推导
- 如果使用方法引用，也是同样可以根据上下文进行推导
- 方法引用是Lambda的孪生兄弟

```java
public interface Printable {
    void printInt(int i);
}

public class PrintableDemo {
    public static void main(String[] args) {
        // lambda
        usePrintable(i -> System.out.println(i));

        //方法引用
        usePrintable(System.out::println);
    }

    private static void usePrintable(Printable p){
        p.printInt(666);
    }
}
```



## 1.3 Lambda表达式支持的引用方法

常见的引用方式：

- 引用类方法
- 引用对象的实例方法
- 引用类的实例方法
- 引用构造器



## 1.4 引用类方法

引用类方法，其实就是引用类的静态方法

- 格式：类名::静态方法

- 范例：Integer::parseInt

  Integer类的方法：public static int parseInt(String s) 将此String转换为int类型数据

代码示例：

- 定义一个接口(Converter)，里面定义一个抽象方法

  int convert(String s);

- 定义一个测试类(ConverterDemo)，在测试类中提供两个方法

  一个方法是：useConverter(Converter c)

  一个方法是主方法，在主方法中调用useConverter方法

  ```java
  public interface Convert {
      int convert(String s);
  }
  
  public class ConverterDemo {
      public static void main(String[] args) {
          // 抽象方法中具体干了什么事，在这儿定义
          useConvert(s -> {
              return Integer.parseInt(s);
          });
  
          useConvert(s->Integer.parseInt(s));
  
          // 引用类方法
          useConvert(Integer::parseInt);
          // lambda表达式中被类方法替代的时候，它的形式参数全部传递给静态方法作为参数
  
      }
      public static void useConvert(Convert c){
          int number  = c.convert("666");
          System.out.println(number);
      }
  }
  ```

注意：**lambda表达式中被类方法替代的时候，它的形式参数全部传递给静态方法作为参数**



## 1.5 引用对象的实例方法

引用对象的实例方法，其实就引用类中的成员方法

- 格式：对象::成员方法

- 范例："HelloWorld"::toUpperCase

  String类中的方法：public String toUpperCase()将此String所有字符转换为大写

![image-20230617131214901](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306171312096.png)

```java
public class PrintString {
    public void printUpper(String s){
        System.out.println(s.toUpperCase());
    }
}

public interface Printer {
    void printUpperCase(String s);
}

public class PrinterDemo {
    public static void main(String[] args) {
        // lambda
//        usePrinter(s -> {// 抽象方法具体做的事情
////            String result = s.toUpperCase();
////            System.out.println(result);
//            System.out.println(s.toUpperCase());
//        });
        usePrinter(s-> System.out.println(s.toUpperCase()));

        // 方法引用:引用对象的实例方法
        PrintString ps = new PrintString();
        usePrinter(ps::printUpper);// 将ps中的方法的具体的实现作为lambda表达式的具体实现
        // lambda表达式被对象的实例方法代替的时候，它的形式参数全部传递给该方法作为参数
    }

    public static void usePrinter(Printer p){
        p.printUpperCase("HelloWorld");
    }
}
```

注：**lambda表达式被对象的实例方法代替的时候，它的形式参数全部传递给该方法作为参数**





## 1.6 引用类的实例方法

引用类的实例方法，其实就是引用类的成员方法

- 格式：类名::成员方法

- 范例：String::substring

  String 类中的方法：public String substring(int beginIndex, int endIndex)

  从beginIndex开始到endIndex结束，截取字符串。返回一个子串，子串的长度为endIndex-beginIndex

![image-20230617132839302](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306171328379.png)

```java
public interface MyString {
    String mySubString(String s, int begin, int end);
}


public class MyStringDemo {
    public static void main(String[] args) {
        // lambda表达式
        useMyString((s,i,j)->{
            return s.substring(i, j);
        });

        useMyString((s,i,j) -> s.substring(i,j));

        // 方法引用
        useMyString(String::substring);
        /*
        * lambda 表达式被类的实例方法替代的时候
        * 第一个参数作为调用者
        * 后面的参数全部传递给该方法作为参数
        * */

    }
    private static void useMyString(MyString ms){
        String s = ms.mySubString("helloworld", 1, 3);
        System.out.println(s);
    }
}
```

输出：

```java
el
el
el
```

注意：lambda 表达式被类的实例方法替代的时候，第一个参数作为调用者，后面的参数全部传递给该方法作为参数





总结一下：useMyString相当于一个间接方法，主要是通过这个间接方法达到调用接口的抽象方法的目的，真正实现抽象方法具体功能的地方是在lambda表达式中，或者匿名内部类中。



## 1.7 引用构造器

引用构造器，其实就是引用构造方法

- 格式：类名:: new
- 范例：Student::new 

![image-20230617140501475](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306171405560.png)

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
}

public interface StudentBuilder {
    Student build(String name, int age);
}

public class StudentDemo {
    public static void main(String[] args) {
        // lambda
        useStudentBuilder(((name, age) -> {
            return new Student(name, age);
        }));
        useStudentBuilder((name, age) -> new Student(name, age));

        // 方法引用,引用构造器
        useStudentBuilder(Student::new);

        // lambda表达式被构造器代替时，它的形式参数全部传给构造器作为参数
    }
    private static void useStudentBuilder(StudentBuilder sb){
        Student s = sb.build("林青霞", 23);
        System.out.println(s.getName() + "," + s.getAge());
    }
}
```

输出：

```java
林青霞,23
林青霞,23
林青霞,23
```

注意：**lambda表达式被构造器代替时，它的形式参数全部传给构造器作为参数**



















