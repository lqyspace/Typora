[TOC]

# 1、接口的默认方法

**在接口中新增了两个默认方法：static方法、default方法，并且都支持方法体。**

**static方法**

```java
public interface DefalutTest {
    static int a =5;
    default void defaultMethod(){
        System.out.println("DefalutTest defalut 方法");
    }

    int sub(int a,int b);

    static void staticMethod() {
        System.out.println("DefalutTest static 方法");
    }
}
```

接口里面的静态方法可以，即static修饰的有方法体的方法不会被继承或者实现，但是静态变量可以被继承，但是不可以被重新复制，因为静态变量的默认修饰符是`public static final`。

接口中的static方法不能被继承，也不能被实现类调用，只能被自身调用。

**default方法**

准备一个子接口继承DefalutTest接口：

```
public interface SubTest extends DefalutTest{

}
```

准备一个子接口的实现类：

```
public class SubTestImp implements SubTest{

    @Override
    public int sub(int a, int b) {
        // TODO Auto-generated method stub
        return a-b;
    }

}
```

创建一个子接口实现类对象，并调用对象中的default方法：

```
public class Main {

    public static void main(String[] args) {
        SubTestImp stl = new SubTestImp();
        stl.defaultMethod();

    }
}
```

执行结果：DefalutTest defalut 方法。

**default方法的相关总结：**

- default方法可以被**子接口继承**亦可被实现类所调用。
- default方法被继承时可以被子接口覆盖
- 如果一个类实现了多个接口，且这些接口中并无继承关系，这些接口中若有相同的（同名，同参数）default方法，则接口实现类会报错，接口实现类必须通过特殊语法指定该实现类要实现哪个接口的default方法。
  - 如果这些接口有继承关系，那么调用default方法的时候会调用子接口的default方法
  - 如果这些接口不存在继承关系，则可以在自己的实现类中定义一个与default同名的方法，那么就不会实现报错



> **额外补充：**
>
> Java中静态属性和静态方法的继承
>
> java中的静态属性和静态方法可以被继承，但是不能被重写而是被隐藏了，既没有多态：
>
> - 静态方法和静态属性是属于类的，与类绑定在一起的，调用的时候可以直接它通过类名调用，不需要继承机制就可以调用。如果子类里面定义了同名的静态方法和静态属性，那么此时父类的静态方法和静态属性被称之为“隐藏”，即优先调用子类的静态方法和静态属性。如果要调用父类的静态方法和静态属性，直接通过类名调用方法或是属性即可完成。
> - 多态的实现是基于继承，接口和重写，有了继承和重写才可以实现父类引用指向子类的对象。重写后的子类的优先级要高于父类的优先级，但是隐藏没有这个优先级。
> - 静态属性和静态方法都可以被隐藏而不可以被重写，因此不能实现多态，也就不能实现弗父类的引用指向不同的子类对象。
>
> 总结：
>
> - 如果子类定义了与父类同名的静态属性和静态变量，那么子类调用的一定是子类的静态方法和静态属性
> - 如果子类没有定义与父类同名的静态方法和静态属性，那么子类调用的一定是父类的静态方法和静态属性



# 2、Lambda表达式

Lambda是一个匿名函数，我们可以将Lambda表达式理解为一段可以传递的代码（将代码像数据一样传递）。使用它可以写出简洁、灵活的代码。作为一种更紧凑的代码风格，使java语言表达能力得到提升。Lambda表达式可以看成是匿名内部类，使用Lambda表达式时，接口必须是函数式接口。

Lambda表达式在java语言中引入了一种新的语法元素和操作。这种操作符号为“->”,Lambda操作符或箭头操作符，它将Lambda表达式分割为两部分。 左边：指Lambda表达式的所有参数 右边：指Lambda体，即表示Lambda表达式需要执行的功能。

Lambda表达式的基本格式：

```java
<函数式接口>  <变量名> = (参数1，参数2...) -> {
	//方法体
}
```

说明：

- (参数1，参数2…)表示参数列表；->表示连接符；{}内部是方法体
- =右边的类型会根据左边的函数式接口类型自动推断；
- 如果形参列表为空，只需保留()；
- 如果形参只有1个，()可以省略，只需要参数的名称即可；
- 如果执行语句只有1句，且无返回值，{}可以省略，若有返回值，则若想省去{}，则必须同时省略return，且执行语句也保证只有1句；
- 形参列表的数据类型会自动推断；
- lambda不会生成一个单独的内部类文件；
- lambda表达式若访问了局部变量，则局部变量必须是final的，若是局部变量没有加final关键字，系统会自动添加，此后在修改该局部变量，会报错。



# 3、函数式接口

- Supplier接口
- Consumer接口
- Predicate接口
- Function接口



# 4、方法引用和构造器引用

## 方法引用

当要传递给Lambda体的操作已经有实现方法，可以直接使用方法引用（实现抽象方法的列表，必须要和方法引用的方法参数列表一致）。

格式：使用操作符“::”将方法名和（类或者对象）分割开来。

有下列三种情况：

- 对象::实例方法
- 类::实例方法
- 类::静态方法

代码：

```java
package com.chen.test.JAVA8Features;

public class MethodRefDemo {
    public static void main(String[] args) {
        FunctionGeneric<String> strName = s -> System.out.println(s);
        strName.fun("Lambda表达式没有使用方法引用");
        
        //方法引用
        FunctionGeneric<String> strName2 = System.out::println;
        strName2.fun("使用方法引用");
    }
}
```

## 构造器引用

本质上：构造器引用和方法引用相识，只是使用了一个new方法。

使用说明：函数式接口参数列表和构造器参数列表要一致，该接口返回值类型也是构造器返回值类型.

格式：ClassName :: new

代码：

```java
package com.chen.test.JAVA8Features;

import java.util.function.Function;

public class MethodRefDemo {
    public static void main(String[] args) {

        //构造器引用
        Function<String, Integer> fun1 = (num) -> new Integer(num);
        Function<String, Integer> fun2 = Integer::new;

        //数组引用
        Function<Integer,Integer[]> fun3 = (num) ->new Integer[num];
        Function<Integer,Integer[]> fun4 = Integer[]::new;
    }
}
```



# 5、Stream API

StreamAPI它位于java.util.stream包中，StreamAPI帮助我们更好地对数据进行集合操作，它本质就是对数据的操作进行流水线式处理，也可以理解为一个更加高级的迭代器，主要作用是遍历其中每一个元素。简而言之，StreamAP提供了一种高效且易于使用的处理数据方式。

**Stream特点：**

- Stream自己不会存储数据
- Stream不会改变源对象。相反他会返回一个持有结果的新的Stream对象
- Stream的操作是延迟执行的。这就意味他们等到有结果以后才会执行
- 和list不同，Stream代表的是任意Java对象的序列，且stream输出的元素可能并没有预先存储在内存中，而是实时计算出来的 。他可以存储有限个或无限个元素。

- Stream的使用方法：

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

  **总结：**

  1、**filter**：根据条件过滤，筛选

  2、**limit**：获取前几个元素；**skip**：跳过前几个元素

  3、**concat**：合并两个流的元素；**distinct**：对流中的元素进行去重

  4、**sorted**：对流中的元素进行排序，默认是自然排序，也可以自定义比较器排序

  5、**map**：主要是将一个函数映射到流中的元素

  6、**mapToInt**：通过映射，将流中的元素映射为 IntStream 类型



# 6、Date/Time API（JSR 310）

Java 8引入了新的Date-Time API(JSR 310)来改进时间、日期的处理。时间和日期的管理一直是最令Java开发者痛苦的问题。java.util.Date和后来的java.util.Calendar一直没有解决这个问题。

因为上面这些原因，诞生了第三方库Joda-Time，可以替代Java的时间管理API。Java 8中新的时间和日期管理API深受Joda-Time影响，并吸收了很多Joda-Time的精华。新的java.time包包含了所有关于日期、时间、时区、Instant（跟日期类似但是精确到纳秒）、duration（持续时间）和时钟操作的类。新设计的API认真考虑了这些类的不变性（从java.util.Calendar吸取的教训），如果某个实例需要修改，则返回一个新的对象。



# 7、Optional类

官方库加入了谷歌的Optional，用来存放T类型的值或者null。它提供了一些有用的接口来避免显式的null检查。如果Optional实例持有一个非空值，则isPresent()方法返回true，否则返回false；orElseGet()方法，Optional实例持有null，则可以接受一个lambda表达式生成的默认值；map()方法可以将现有的Opetional实例的值转换成新的值；orElse()方法与orElseGet()方法类似，但是在持有null的时候返回传入的默认值。

```java
Optional< String > fullName = Optional.ofNullable( null );
System.out.println( "Full Name is set? " + fullName.isPresent() );        
System.out.println( "Full Name: " + fullName.orElseGet( () -> "[none]" ) ); 
System.out.println( fullName.map( s -> "Hey " + s + "!" ).orElse( "Hey Stranger!" ) );
```



# 8、Nashorn JavaScript引擎

Java 8提供了新的Nashorn JavaScript引擎，使得我们可以在JVM上开发和运行JS应用。Nashorn JavaScript引擎是javax.script.ScriptEngine的另一个实现版本，这类Script引擎遵循相同的规则，允许Java和JavaScript交互使用。

```java
ScriptEngine engine = ScriptEngineManager::getEngineByName( "JavaScript" );
System.out.println( "Result:" + engine.eval( "function f() { return 1; }; f() + 1;" ) );
// 输出 -> Result: 2
```

java8新特性的优点：

1. 速度快；
2. 代码少、简介（新增特性:lamdba表达式）；
3. 强大的Stream API；
4. 使用并行流和串行流；
5. 最大化较少空指针异常Optional。
