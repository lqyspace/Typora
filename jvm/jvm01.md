[TOC]

# 1、类加载



## 1.1 Java运行时一个类是什么时候被加载的

![image-20230323162048648](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231620784.png)

- 注意：按需加载，需要用到的时候才加载

- 如下代码：

  ```java
  public class Hello {
      // 常量
      public static final int a = 123;
  
  //    静态变量
      public static int b;
  
      // 实例变量
      public int abc;
  
      // -XX:+TraceClassLoading 监控类的加载
      public static void main(String[] args) {
          User user = new User();
          user.working();
      }
  }
  ```

- ![image-20230323165541623](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231655676.png)



## 1.2 JVM一个类的加载过程

![image-20230323165828728](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231658844.png)

![image-20230323165907151](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231659270.png)

- **加载**：classpath、jar包、网络、某个磁盘位置下的类以class二进制字节流读进来，在内存中生成一个代表这个类的java.lang.Class对象放入元空间（在JDK8及以后，永久代改为元空间）。此阶段我们程序员可以干预，我们自定义类加载器来实现类的加载。
- **验证**：验证Class文件的字节流中包含的信息符合《Java虚拟机规范》的全部约束要求，保证虚拟机的安全。
- **准备**：类变量赋默认初始值，int为0，long为0L，boolean为false，引用类型为null；常量赋正式值。
  - ![image-20230323171027738](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231710798.png)
  - 在准备阶段：**常量**赋正式值，比如a赋值为123,；**类变量**赋值为0，在此阶段222还未赋值给b。此时的**实例变量**还没有初始化，要等我们new一个对象的时候才开始初始化。
  - 在加载类的时候，只是给常量，还有类变量进行初始化，实例变量要等到 new 的时候才开始初始化。
- **解析**：**把符号引用解析为直接引用**：就是把一个类的class文件读进来以后变成一个java.lang.Class的对象在元空间里面，这个时候需要把符号引用解析为直接引用。
- **初始化**：当我们 new 一个类的对象，访问一个类的静态属性，修改一个类的静态属性，调用一个类的静态方法，用反射API对一个类进行调用；初始化当前类，其父类也会被初始化..... 这些都会触发类的初始化。
- **使用**：使用这个类 
- **卸载**：三个条件同时满足
  - 该类所有的实例都已经被GC，也就是JVM中不存在该Class的任何实例
  -  加载该类的ClassLoaer已经被GC
  - 该类的java.lang.Class对象没有在任何地方被引用，如不能在任何地方通过反射访问该类的方法；



## 1.3 JVM一个类的初始化过程

![image-20230323172521322](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231725455.png)

而在初始化阶段，才真正初始化类的变量和其他资源。

```java
public class Test02 {
    /// 静态常量  == 准备阶段赋正式值 静态常量
    public static final String staticConstantField = "静态常量";

    // 静态变量 == 准备阶段赋值为null，初始化阶段赋值为 “静态变量”
    public static String staticField = "静态变量";

    // 变量 == 创建对象时赋值
    public String field = "变量";

    // 静态初始化块 == 初始化阶段执行
    static {
        System.out.println(staticConstantField);
        System.out.println(staticField);
        System.out.println("静态初始化块");
    }

    // 初始化块 == 创建对象的时候执行
    {
        System.out.println(field);
        System.out.println("初始化块");
    }

    // 构造器 == 创建对象的时候执行
    public Test02(){
        System.out.println("构造器");
    }

    public static void main(String[] args) {

    }
}
```

输出：

```java
静态常量
静态变量
静态初始化块
```

总结;

- 准备阶段
  - 静态常量赋正式值
  - 静态变量赋值为0，布尔值，null，或者其他的初始化值
- 初始化阶段
  - 静态变量赋正式值
  - **执行静态初始化块**
- 创建对象阶段
  - 普通初始化块，创建对象时执行
  - 构造器，在创建对象时执行
  - 类变量：创建对象时赋值



代码;

```java
public class Test02 {
    /// 静态常量  == 准备阶段赋正式值 静态常量
    public static final String staticConstantField = "静态常量";

    // 静态变量 == 准备阶段赋值为null，初始化阶段赋值为 “静态变量”
    public static String staticField = "静态变量";

    // 变量 == 创建对象时赋值
    public String field = "变量";

    // 静态初始化块 == 初始化阶段执行
    static {
        System.out.println(staticConstantField);
        System.out.println(staticField);
        System.out.println("静态初始化块");
    }

    // 初始化块 == 创建对象的时候执行
    {
        System.out.println(field);
        System.out.println("初始化块");
    }

    // 构造器 == 创建对象的时候执行
    public Test02(){
        System.out.println("构造器");
    }

    public static void main(String[] args) {
        new Test02();
    }
}
```

输出：

```java
静态常量
静态变量
静态初始化块
变量
初始化块
构造器
```



## 1.4 继承时父子类的初始化顺序

![image-20230323175447974](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231754059.png)

代码1：

父类：

```java
public class ParentClass {
    public static String p_StaticField = "父类---静态变量";

    // 变量
    public String p_Field = "父类---变量";

    protected int i=0;
    protected int j=0;

    //静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("父类---静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("父类---初始化器");
    }

    public ParentClass(){
        System.out.println("父类---构造器");
        System.out.println("i=" + i + ",j=" + j);
        i=1;
        j=1;
    }
}
```

子类：

```java
public class ChildClass extends ParentClass{
    public static String p_StaticField = "子类---静态变量";

    // 变量
    public String p_Field = "子类---变量";

    protected int i=0;
    protected int j=0;

    //静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("子类---静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("子类---初始化器");
    }

    public ChildClass(){
        System.out.println("子类---构造器");
        System.out.println("i=" + i + ",j=" + j);
        i=1;
        j=1;
    }
    public static void main(String[] args) {

    }
}
```

输出：

```java
父类---静态变量
父类---静态初始化块
子类---静态变量
子类---静态初始化块
```

总结：

- 初始化的时候，先初始化父类，再初始化子类；
- 如果没有 new 对象，那么静态变量，静态代码块会被执行；其他的代码还没执行



代码2：

父类：

```java
public class ParentClass {
    public static String p_StaticField = "父类---静态变量";

    // 变量
    public String p_Field = "父类---变量";

    protected int i=0;
    protected int j=0;

    //静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("父类---静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("父类---初始化器");
    }

    public ParentClass(){
        System.out.println("父类---构造器");
        System.out.println("i=" + i + ",j=" + j);
        i=1;
        j=1;
    }
}
```

子类：

```java
public class ChildClass extends ParentClass{
    public static String p_StaticField = "子类---静态变量";

    // 变量
    public String p_Field = "子类---变量";

    protected int i=0;
    protected int j=0;

    //静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("子类---静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("子类---初始化器");
    }

    public ChildClass(){
        System.out.println("子类---构造器");
        System.out.println("i=" + i + ",j=" + j);
        i=1;
        j=1;
    }
    public static void main(String[] args) {
        new ChildClass();
    }
}
```

输出：

```java
父类---静态变量
父类---静态初始化块
子类---静态变量
子类---静态初始化块
父类---变量
父类---初始化器
父类---构造器
i=0,j=0
子类---变量
子类---初始化器
子类---构造器
i=0,j=0
```

总结：

- 先初始化父类，再初始化子类
- 静态变量和静态代码块在类加载的时候执行
- 类变量和普通初始化块在创建类的时候执行



如果new一个父类：

```java
public static void main(String[] args) {
        new ParentClass();
//        new ChildClass();
    }
```

输出：

```java
父类---静态变量
父类---静态初始化块
子类---静态变量
子类---静态初始化块
父类---变量
父类---初始化器
父类---构造器
i=0,j=0
```

因为new的父类对象，因此只需要把父类的变量和初始化块进行执行，子类的变量和初始化块不需要执行。



## 1.5 究竟什么是类加载器

![image-20230323191106650](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231911783.png)

- 这个类加载器可以是c++实现的，也可以是java实现的
- 类加载器是一个程序，一个代码



## 1.6 JVM有哪些类加载器

![image-20230323191403099](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231914229.png)

![image-20230323191526937](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231915021.png)



## 1.7 JVM中不同的类加载器加载哪些文件

![image-20230323191913971](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231919123.png)

- 根的类加载器是由c++实现的

- 使用 `BufferedReader.class.getClassLoader()`可以打印类的加载器

  ```java
  System.out.println(BufferedReader.class.getClassLoader());
  ```

- ```java
  System.setProperty("java.ext.dirs", "d:/");
  ```

- 应用程序类加载器（Application ClassLoader）：系统的类加载器

  - sun.misc.Launcher$AppClassLoader
  - 加载用户类路径（ClassPath）上所有的类库
  - 自己新建的类，maven的jar包等都是应用程序类加载器加载（AppClassLoader）的

- AppClassLoader、ExtClassLoader都是用java语言实现的

- ExtClassLoader：

  ![image-20230323194650915](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231947340.png)

  

  

  - AppClassLoader：

    ![image-20230323194849382](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231948462.png)

    

  - 自定义的类加载器：你想加载哪里就加载哪里





## 1.8 JVM三层类加载器之间的关系是继承吗

![image-20230323191526937](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231915021.png)

他们三者之间没有继承关系

BootstrapClassLoader是用c++语言实现的，而ExtClassLoader，AppClassLoader是用java实现的，所以没有继承关系



## 1.9 你了解JVM类加载的双亲委派模型吗？

![image-20230323200128971](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303232001170.png)

- 假如加载String，这个类是rt.jar包里面的一个类，首先是请求AppClassLoader这个类加载起去加载，但是呢，它自己不去加载，它委派给他的上一层，但是ExtClassLoader也不去加载，它也委派给它的上一层，然后到了BootstrapClassLoader就得尝试去加载，BootstrapClassLoader就去rt.jar包下去加载，所以它就加载成功了，因为String类刚好在rt.jar包下。把这个String类加载到JVM中，此时就完成了，类加载结束了。
- 假如加载ext中的一个包，然后也是从AppClassLoader这个加载器开始加载，但是这个加载器不自己加载，而是交给上一层ExtClassLoader去加载，但是ExtClassLoader也不自己加载，也是交给上一层BootstrapClassLoader去加载，但是BootstrapClassLoader加载时找不到这个类，然后他就把这个信息反馈给ExtClassLoader这个加载器，然后ExtClassLoader这个加载器开始加载，然后就找到了这个ext的包，把它加载到JVM内存中，然后就加载成功了，此时类加载完成。
- 假如此时加载用户自己写的一个类User，然后也是先请求AppClassLoader这个加载器，然后这个加载器不自己加载，它委派给它的一层ExtClassLoader这个加载器去加载，然后ExtClassLoader也不自己去加载，它也委派给它的一层去加载，然后BoostrapClassLoader去加载，BootstrapClassLoader没有加载出来，就告诉ExtClassLoader去加载，ExtClassLoader也没有加载出来，然后就告诉AppClassLoader去加载，然后AppClassLoader就在自己的ClassPath的路径下去找，然后找到了，就加载成功了，JVM里面就有了这个类。
- 假如有一个类，在BoostrapClassLoader，ExtClassLoader，AppClassLoader里面都没有找到，那么就会报一个ClassNotFountException
- 总结：双亲委派模型工作工程：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一层次的类加载器都是如此。因此所有的加载器请求最终都应该传送到最顶层的启动类加载器中，只有当上一层的类加载器反馈自己无法完成这个加载请求（它的搜索范围中没有这个类）时，下一层类加载器才会尝试自己去加载。



> **为什么要设计双亲委派模型，有什么好处？**

- 1、确保安全，避免java核心内库被修改
  - 比如String，Object这些类必须交给核心内库进行加载，不会调用自定义的String或者Object，从而保证安全
- 2、避免重复加载
- 3、保证类的唯一性

如果你写了一个java.lang.String的类去运行，发现会抛出如下异常：

```java
public class String {
    public static void main(String[] args) {
        System.out.println("String类");
    }
}
```

报错：

```java
错误：在类java.lang.Strig中找不到main方法，请将main方法定义为：public static void main(String[] args)
```

因为在BoostrapClassLoader的rt.jar中的String并没有main方法，因此会报错，这也说明加载类一定会从最顶层开始加载，逐层往下。

注意：在自定义类时，不要使用java核心内库的包名：`java.lang`，否则会报一个 `SecurityException`。



> **可以打破双亲委派模型吗？如何打破双亲委派模型？**

可以

想要打破这种双亲委派模型，那么就**自定义一个类加载器**，继承ClassLoader类，然后 **重写其中的loadClass方法**，使其不进行双亲委派即可。



> **如何自定义自己的类加载器**

1、继承ClassLoader

2、覆盖findClass（String name）方法 或者 loadClasss（）；

3、覆盖findClass（String name）不会打破双亲委派；覆盖loadClass（）方法会覆盖双亲委派。

4、loadClass（）方法在ClassLoader的类中，而findClass在URLClassLoader类中有具体的实现，AppClassLoader和ExtClassLoader只是继承了URLClassLoader类中的findClass方法，并没有覆盖loadClass方法，因为loadClass方法是双亲委派方法。

5、我们重写findClass方法，就不用调用URLClassLoader类里面的findClass方法。



## 2.0 ClassLoader中的loadClass()，findClass()，defineClass()区别

- loadClass() 就是主要进行类加载的方法，默认的双亲委派机制就实现在这个方法中。
- findClass() 根据名称或位置加载.class字节码；
- defineClass() 把字节码转化为java.lang.Class

1、当我们想要自定义一个类加载器的时候，并且想要破坏双亲委派模型时，需要重写loadClass()方法

2、如果我们自定义一个类加载器，但是不想破坏双亲委派模型的时候，可以重写findClass方法，findClass是JDK1.2以后的ClassLoader新添加的一个方法，这个方法只抛出一个异常，没有默认实现；

JDK1.2之后已不再提倡用户直接覆盖loadClass方法，而是建议把自己的类加载逻辑实现到findClass（）方法中；

**所以如果你想定义一个自己的类加载器，并且要遵守双亲委派模型，那么可以继承ClassLoader，并且在findClass中实现你自己的加载逻辑即可**；



## 2.1 加载一个类采用Class.forName()和ClassLoader有什么区别

![image-20230324102019208](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241020398.png)

总结：

- 使用Class.forName()进行加载的时候会初始化这个类，静态变量，静态初始化块都属于类的信息
  - 所以使用Class.forName进行加载的时候会初始化这个类，所以会打印静态变量，静态初始化块的信息
- 而使用ClassLoader加载一个类，只会执行加载，链接，不会进行类的初始化，如果使用Class.forName进行加载一个类的时候会进行初始化。
  - **需要访问一下这个类**，才会初始化，比如使用**newInstance()**方法，此时不仅初始化了这个类，而且也创建了一个类的实例；或者访问一下这个类的静态变量，此时就会加载类，并且对类进行初始化。

```java
public class Children extends ParentClass{
    public static String p_StaticField = "子类---静态变量";

    // 变量
    public String p_Field = "子类---变量";

    protected int i=0;
    protected int j=0;

    //静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("子类---静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("子类---初始化器");
    }

    public Children(){
        System.out.println("子类---构造器");
        System.out.println("i=" + i + ",j=" + j);
        i=1;
        j=1;
    }
    public static void main(String[] args) {
//        new ParentClass();
//        new ChildClass();
//        System.out.println(BufferedReader.class.getClassLoader());
//        System.out.println(Sdp.class.getClassLoader());
//        System.setProperty("java.ext.dirs", "d:/");
    }
}
```

```java
public class ParentClass {
    public static String p_StaticField = "父类---静态变量";

    // 变量
    public String p_Field = "父类---变量";

    protected int i=0;
    protected int j=0;

    //静态初始化块
    static {
        System.out.println(p_StaticField);
        System.out.println("父类---静态初始化块");
    }

    // 初始化块
    {
        System.out.println(p_Field);
        System.out.println("父类---初始化器");
    }

    public ParentClass(){
        System.out.println("父类---构造器");
        System.out.println("i=" + i + ",j=" + j);
        i=1;
        j=1;
    }
}
```

测试1：

```java
public class Test02 {
    public static void main(String[] args) throws ClassNotFoundException {
        // Class.forName进行加载的时候会初始化这个类
        Class.forName("com.myjvm.loader.Children");

        // 使用ClassLoader加载类的时候
//        Class clazz = Test02.class.getClassLoader().loadClass("com.myjvm.loader.Children");
//        System.out.println(Test02.class.getClassLoader());
    }
}
```

输出：

```java
父类---静态变量
父类---静态初始化块
子类---静态变量
子类---静态初始化块
```



测试2：

```java
public class Test02 {
    public static void main(String[] args) throws ClassNotFoundException {
        // Class.forName进行加载的时候会初始化这个类
//        Class.forName("com.myjvm.loader.Children");

        // 使用ClassLoader加载类的时候
        Class clazz = Test02.class.getClassLoader().loadClass("com.myjvm.loader.Children");
//        System.out.println(Test02.class.getClassLoader());
    }
}
```

没有输出



测试3：

```java
public class Test02 {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException {
        // Class.forName进行加载的时候会初始化这个类
//        Class.forName("com.myjvm.loader.Children");

        // 使用ClassLoader加载类的时候
        Class clazz = Test02.class.getClassLoader().loadClass("com.myjvm.loader.Children");
        clazz.newInstance();
//        System.out.println(Test02.class.getClassLoader());
    }
}
```

输出：

```java
父类---静态变量
父类---静态初始化块
子类---静态变量
子类---静态初始化块
父类---变量
父类---初始化器
父类---构造器
i=0,j=0
子类---变量
子类---初始化器
子类---构造器
i=0,j=0
```



**Class.forName()的底层实现：**

![image-20230324105229484](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241052553.png)

使用native方法调用本地的c，c++方法进行加载



**loadClass()的底层实现：**

![image-20230324105433250](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241054361.png)

调用的是双亲委派模型进行实现。



**跟踪类的加载信息：**

```java
public class Test02 {
    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException {
        // Class.forName进行加载的时候会初始化这个类
//        Class.forName("com.myjvm.loader.Children");

        // 使用ClassLoader加载类的时候
//        Class clazz = Test02.class.getClassLoader().loadClass("com.myjvm.loader.Children");
//        clazz.newInstance();
//        System.out.println(Test02.class.getClassLoader());

        // -XX:+TraceClassLoading 监控类的加载
//        Children children = null; // 这个方式并不能加载类，需要访问一下这个类才能加载类
        String s = Children.p_StaticField;
    }
}
```

此时输出里就会有：类的加载，类的初始化的相关信息。



总结：

- **Class.forName得到的class是已经完成初始化的**
- **Classloader.loaderClass得到的class是还没有初始化的**



# 2 Tomcat的类加载机制

## 2.1 你了解tomcat的类加载机制吗

![image-20230324133321505](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241333661.png)

- 最上面的三个是Java的类加载器

  - BootstrapClassLoader是JVM自身的类加载器，而ExtClassLoader，AppClassLoader是java的类加载器

- 下面的五个是Tomcat的类加载器

  - 在原来的java的类加载器基础上，Tomcat新增了**3个基础类加载器**和**每个web应用的类加载器**+**JSP类加载器**

    ![image-20230324151439567](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241514747.png)

  - Catalina类加载器还有一个名称：Server类加载器

  - 最开始的tomcat版本，比如5.几的tomcat的目录下是有common，shared，server的目录的；而现在的版本做了简化，没有common，shred和server的目录，所以conf目录下的 caalina.properties的配置文件里并没有配置shared和server的相关配置。

    ![image-20230324141109538](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241411652.png)

  - tomcat5版本：common类加载器加载common目录下的jar或class文件，Catalina类加载器加载server目录下的jar或class文件，Shared类加载器加载shared目录下的jar或class文件

  - 新的版本的tomcat对类加载器进行了简化，现在的catalina类加载，shared类加载器现在都由common类加载器所取代。

    ![image-20230324143227185](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241432248.png)

    ![image-20230324143246099](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241432169.png)

    ![image-20230324143257057](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241432235.png)

  - 从tomcat6开始，包括6，tomcat的目录下就已经没有common，shared，server的目录，而是用lib目录代替。

    ![image-20230324143159234](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241431307.png)

  - 如果想在tomcat6以后的版本使用server，shared类加载器，可以在catalina.properties里面进行配置。

  - WebApp类加载器加载webapps目录下的文件或者jar

  - JSP类加载器加载jsp文件，**在运行jsp时，会转换成java文件，同时编译成class文件**，所以jsp最后运行的时候也是一个class文件，而这个class文件是由JSP类加载器进行加载的

  - common类加载器，Catalina类加载器，Shared类加载器只有一个；而WebApp类加载可以有多个，有多个项目就有多个WebApp类加载；JSP类加载也可以有多个，一个jsp页面就是一个类加载器。



**以下是tomcat中Bootstrap启动的main方法**

![image-20230324145339336](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241453450.png)

![image-20230324145405034](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241454167.png)

最开始commonLoader，catalinaLoader，sharedLoader都是空的，然后在initClassLoader方法里进行初始化，返回的类仍然是继承自lava的ClassLoader。初始化时使用的是一个创建类的工厂：

![image-20230324145847561](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241458737.png)

![image-20230324145924468](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241459584.png)

在ClassLoaderFactory这个类里，调用的仍然是java的URLClassLoader这个加载器。



**WebApp类加载器**

WebApp类加载器是由一个 `WebappClassLoader.java`这个类实现的，他继承了 `WebappClassLoaderBase.java`这个类，而这个类继承了`URLClassLoader`这个类，在`WebappClassLoaderBase`这个类中他重写findClass和loadClass方法，所以它打破了双亲委派机制。



**JSP类加载器**

JSP类加载器是由`JasperLoader`这个类实现的，这个继承了`URLClassLoader`这个类，然后`JasperLoader`这个类重写了findClass和loadClass这两个方法，因此它也打破了双亲委派模型。



**总结：**

Tomcat自定义了 `WebAppClassLoader` 类加载器，打破了双亲委派机制，几如果收到类加载的请求，首先会尝试自己去加载，如果找不到再交给父加载器去加载，目的就是为了 优先加载Web应用自己定义的类，我们知道ClassLoader默认的loadClass方法就是以双亲委派的模型进行加载类的，那么Tomcat打破了这个规则，重写了loadClass方法，我们可以看到 `WebAppClassLoader`的base类中重写了loadClass方法。



## 2.2 为什么Tomcat要打破双亲委派模型

Tomcat是web容器，那么一个web容器可能需要部署多个应用程序；

1、部署在同一个Tomcat上的两个web应用所使用的java类库要相互隔离

​	1）避免不同Web应用的jar出现版本冲突

2、部署在同一个Tomcat上的两个web应用所使用的java类库要相互共享

​	1）可以把共享的jar包放在lib目录下

3、保证Tomcat服务器自身的安全不受部署的Web应用程序影响

​	1）保证Web里面的jar包不会影响Tomcat自身的运行

4、需要支持JSP页面的**热部署和热加载**

​	1）当修改了JSP页面以后，直接重新部署到tomcat服务器上而不需要重启tomcat服务器

​	2）热加载是指启动一个新的jsp类加载器，把这个jsp页面重新加载，重新加载成一个class文件，jsp运行的时候其实最终是编译成一个class文件去运行；Tomcat内部有一个监听器，监听到jsp的变化就会启动一个新的类加载器重新加载这个class文件





## 2.3 什么是热加载，什么是热部署

**热加载** 是指可以在不重启服务的情况下让更改的代码生效，热加载可以显著的提升开发以及调试的效率，它是基于Java的类加载器实现的，但是由于热加载的不安全性，一般不会用于正式的生产环境

**热部署** 是指可以在不重启服务的情况下重新部署整个项目，比如Tomcat热部署就是在程序运行时，如果我们修改了War包中的内容，那么Tomcat就会删除之前的War包解压的文件夹，重新解压新的War包生成新的文件

1、**热加载** 是在运行时重新加载class，后台会启动一个线程不断检测你的class是否发生改变

2、**热部署** 是在运行时重新部署整个项目，耗时相对较高



**如何实现热加载？**

**在程序代码更改且重新编译后**，让运行的进程可以实时获取到新编译后的class文件，然后重新进行加载；

1、实现自己的类加载器

2、从自己的类加载器中加载要热加载的类

3、不断轮训要热加载的类 class文件是否有更新，如果有更新，重新加载



注意：运行进程只可以获取重新编译后的class文件，修改了没有编译的文件仍然获取不到。



## 2.4 java代码到底是如何运行起来的

![image-20230324170052734](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241700889.png)

如图所示，一个`.java`文件，首先通过 `javac` 将其编译成 `.class` 文件，然后使用 `java`命令 将其运行在JVM上。

JVM可以帮助我们屏蔽底层的操作系统，硬件，CPU指令层面的细节，因此可以实现 一次写随时读 的功能。

除了`.java`文件，还可以将 Groovy，Scala，Kotin语言写的代码编译成 `.class` 文件。



![image-20230324171142009](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241711194.png)

说明：

1、一个`Mall.java`文件，通过 `javac` 命令编译成 `Mall.class` ，然后使用 `java Mall`命令使其在`java虚拟机`上运行。

2、一个`Mall.java`文件，通过 `javac` 命令编译成 `Mall.class` ，可以把这个class文件打包成jar包，然后使用 `java -jar Mall.jar`命令进行执行。

3、一个`Mall.java`文件，通过 `javac` 命令编译成 `Mall.class` ，可以把这个class文件打包成war包，然后放进Tomcat中，使用 startup.sh启动tomcat，其实也就是运行tomcat中的Bootstrap这个类的main方法，其实也是启动一个jvm虚拟机。

4、运行一个java程序，都是通过 `java`这个命令启动一个JVM虚拟机，然后在虚拟机里面运行 `Mall.class`字节码文件。