[TOC]

# 1、JVM

## 1.1 jvm整个运行原理图

![image-20230324172047857](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241720019.png)

首先一个 .java 文件 通过 javac 编译成 .class 字节码文件，然后由**类加载子系统**加载到jvm内存的**元空间**（或者**永久代，JDK8及以后永久代改为元空间**）里。

main方法开始执行，创建一个**虚拟机栈**或者叫**线程栈**或者叫**方法栈**，因为main方法运行也是一个线程。创建一个虚拟机栈然后执行main方法里面的代码，main方法里面的局部变量就放在自己的虚拟机栈里面，引用对象就放在**堆**里面。

**程序计数器**用于记录目前代码执行到了哪里，现在执行到了第几行；如果方法在执行的过程中还需要调用本地方法（native方法，C++语言实现的方法），这时候就需要有一个**本地方法栈**，会创建本地方法栈，执行 native 方法。

在代码执行的时候，有些对象不能再用了，变成垃圾了，那么就用垃圾回收器进行回收。垃圾回收器回收堆里面的垃圾对象。

如果一个类不需要再用了，那么这个类就可以从元空间中卸载掉。



## 1.2 请介绍一下JVM的内存结构的划分

![image-20230324175139453](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241751596.png)

1、左边的区域（除了堆，元空间以外的区域）是线程私有的区域，右边的区域（堆，元空间）是线程共有的区域

​	1）线程私有：线程与线程之间是**隔离的**，每个线程用每个线程自己的，他们**不共享**；在这个区域**不存在线程安全性**问题，每个线程和每个线程之间是隔离的，不存在共享数据的问题，所以他们之间不存在线程安全的问题。

​	2）线程共享：各个线程一起用这个区域，是**共享**的；在这个区域存在线程安全性问题，因为这个区域各个线程共享，所以存在线程安全性问题。

2、调用native方法的时候，在本地方法栈执行。

3、程序计数器：存储字节码行号指示器，表明代码已经执行到哪里

4、虚拟机栈：存储方法，局部变量，运行数据

5、堆：存储创建的所有的对象，和数组。

6、元空间存储：虚拟机加载的字节码数据，静态变量，常量，运行时常量池。





## 1.3 从JVM角度剖析一段代码如何执行

代码如下：

Manager类：

```java
public class Manager {
}
```

Config类：

```java
public class Config {
    // 类变量 GCRoot
    public static Manager manager = new Manager();

    // 实例变量
    private int a;

    public String loadData(){
        return "abc";
    }
}
```

Application类：

```java
public class Application {
    //main线程 --- main的线程栈，也就是虚拟机栈
    public static void main(String[] args) throws IOException {
        load();
        System.in.read(); // 程序不要退出
    }

    private static void load() {
        Config config = new Config();
        config.loadData();
    }
}
```



代码执行示意图如下：

![image-20230324182025705](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303241820804.png)

首先加载Application这个类，这个类要放到元空间里面。然后main方法执行，jvm创建一个虚拟机栈，同时创建一个main方法的栈帧，然后main方法的栈帧被压入到虚拟机栈里面。

在main方法里面执行一个load方法，jvm创建一个load方法的栈帧，并把load方法压入到虚拟机栈里，在load方法里面，创建了一个config的局部变量，这个局部变量就在load方法的栈空间里，这个局部变量指向的对象`new Config`存储在堆里面，我们要使用Config这个类，就需要用类加载器把Config这个类加载到jvm的内存中，然后这个Config的类信息就存在元空间里面。然后我们通过config这个变量指向 new Config这个对象。

然后再执行config.loadData()方法，然后这个方法被jvm创建了一个栈帧，并且被压入到虚拟机栈里。

然后我们在创建Config这个对象的时候，首先是加载这个类，Config这个类会被放到元空间里，它里面有一个静态变量，这个静态变量也会放进元空间里面，静态变量所对应的对象`new Manager`会被放进堆里面，然后静态变量指向堆里面的 `new Manager()`对象。然后Config这个类的实例变量a，会存储在`new Config`对象的属性空间里。

然后在创建Manager这个对象的时候，会先把这个类加载到元空间中，然后开始执行创建对象的操作。

方法执行结束之后，方法出栈。



## 1.4 JVM运行时数据区 程序计数器的特点及作用

![image-20230324231127462](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303242311629.png)

1、程序计数器是一块很小的内存空间，几乎可以忽略

2、是当前线程所执行的**字节码的行号指示器**

3、**Java多线程执行时，每条线程都有一个独立的程序计数器，各条线程之间计数器互不影响**

4、该区域是“线程私有”的内存，每个线程独立存储

5、该区域不存在OutOfMemoryError

6、无GC回收；



## 1.5 JVM运行时数据区，虚拟机栈的特点及作用

1、线程**私有**

2、方法执行会**创建栈帧**，**存储局部变量表**等信息

3、方法执行入虚拟机栈，方法执行完出虚拟机栈；（先进后出）

4、**栈深度大于虚拟机所允许的 会报：StackOverflowError；（一般出现在递归调用时）**

5、栈需扩展而无法申请空间：OutOfMemoryError（比较少见）；**hotspot虚拟机没有这个错误**，因为hotspot虚拟机中的栈空间不是动态扩展，而是一开始就定义好了；如果空间不够，在创建这个线程栈的时候就已经创建失败了，就报这个错误，但这个错误在hotspot虚拟机是很少见的；

6、栈里面运行方法，存放方法的局部变量名，变量所指向的值（常量值，对象值等）都存放在堆上的；**常量值是存在元空间里，元空间在划分的时候是和堆在一起的**。所以常量值是在堆里面，加载一个class类是加载到元空间里，而元空间的类的常量值是放在堆里面。

7,、栈一般不设置大小，栈所占用的空间其实很小，可以通过`-Xss1M`进行设置，如果不设置栈空间默认为1M。也可以调成其他的数值：-Xss128k。栈空间的大小是可以改小的，因为递归调用的深度不是特别多，所以是完全够用的；另外把栈空间改小一点，可以启动更多的线程，因为启动一个线程就启动一个线程栈，线程栈就是虚拟机栈，如果线程栈的空间小的话，在同样的内存区域中，就可以创建更多的线程，那启动的线程数就更多。

8、线程栈随线程而生，随线程而灭

9、该区域不会有GC回收



![image-20230324232334178](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303242323335.png)

说明：

- 一个线程执行，有一个虚拟机栈，比如通过一个main方法去执行，main就创建一个虚拟机栈，一个方法创建一个栈帧，栈帧里面又有四块：局部变量表、操作数栈、动态链表、返回地址
  - 局部变量：基本数据类型就直接在里面存储；引用类型的局部变量存在栈帧里，而引用的对象存在堆里；
  - 操作数栈：就是在方法中进行一些运算，那么此时就需要操作数栈；刚开始时是空的，随着代码运行压栈出栈
  - 动态链接：在一个方法里面还会调用别的方法，那么就需要找出这个方法的内存地址，通过动态链接找到这个内存地址；这个方法可能在元空间里面。
  - 返回地址：return 返回结果





## 1.6 JVM运行时数据区 本地方法栈的特点及作用

![image-20230326005445322](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260054545.png)

说明：

- 与虚拟机栈类似
- 本地方法栈为Native方法服务
- Hotspot虚拟机将虚拟机栈和本地方法栈区域的划分其实是合在一起的，就相当于在一个区域中；分配的内存区域都在一起
- 有StackOverFlowError和OutOfMemoryError错误（OutOfMemoryError比较少见）；如果在创建线程的时候，导致虚拟机栈空间分配不够，无法分配空间，无法申请空间来创建这个虚拟机栈，也会产生OutOfMemoryError错误
- 随线程而生，随线程而灭
- 所以这个区域不存在GC回收

程序计数器、虚拟机栈、本地方法栈3个区域随线程而生，随线程而灭，所以这三个区域都是没有垃圾回收的。

![image-20230326010946128](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260109300.png)



## 1.7 JVM运行时数据区 Java堆的特点及作用

- 线程共享
- **虚拟机启动**时创建堆内存
- 虚拟机所管理的内存中最大的一块区域
- 存放所有实例对象或数组
- GC收集器的主要管理区域
- 可分为新生代（刚刚创建的对象），老年代（对象存活了很长的时间）
- 新生代更细分为Eden，From Survivor，To Survivor，Eden：Survivor=8：1:1
- **可通过-Xmx，-Xms调节堆的大小**
  - 比如加入参数：-Xmx128m -Xms128m
- 如果堆无法再扩展，就报一个 **java.lang.OutOfMemoryError: Java heap space**
- 如果从分配内存的角度看，**所有线程共享的Java堆中可以划分出多个线程私有的分配缓冲区**（Thread Local Allocation Buffer，TLAB），以提升对象分配时的效率。
  - 多个线程同时往堆里面放数据，可能会产生竞争关系，可能会产生冲突，就会用锁的方式，但是用锁的话，效率会有损耗，所以为了解决这个问题，就给每个线程分配了缓冲区
  - 这个缓冲区不是很大，但是够线程取放一些数据，当这个缓冲区放满了以后，然后再往公共区域去放数据；当私有的区域放满了以后，就会放在公共的区域，这个时候就需要上锁来保证安全问题





![image-20230326011843579](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260118786.png)



关于java heap space的问题：

![image-20230326013112145](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260131278.png)

由于一直创建对象，对象存放在堆里，而且使用了orderlist对新创建的对象进行引用，则表示这个对象一直要使用，所以每次创建的order都不是垃圾，所以不能收回，但是一直创建order对象的话，就会报Java heap space的错。

只要把orderlist的引用去掉，即删掉add方法，那么orderlist这个局部变量 就只指向堆内存里的ArrayList对象 而并没有对order的对象进行引用。所以GC可以对order对象进行回收。



## 1.8 JVM中对象如何在堆内存中分配

![image-20230326094251776](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260942928.png)

- 指针碰撞（Bump The Pointer）：指对象在内存中分配规整，排列整齐的情况下，我们可以通过指针不停的往右边移动，从而知道当前的对象放到哪里去了，表示前面的内存已经分配满了不能再分配了，要从右边的内存开始分配

![image-20230326094339559](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260943678.png)

- 空闲列表：指对象在内存中分配的不规整，然后使用一个空闲列表来记录内存中空闲的内存，当对象需要使用内存时，会从空闲列表中查询空闲的内存从而将对象存放进去。

![image-20230326095146422](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260951548.png)

选择哪种分配方式由Java堆是否规整决定的，而Java堆是否规整又由所采用的垃圾回收器是否带有**空间压缩整理（Compact）**的能力决定的；

因此当使用**Serial、ParNew**等带压缩整理过程的收集器时，系统采用的分配算法就是指针碰撞，既简单又高效；

而当采用**CMS**这种**基于清除（Sweep）算法**的收集器时，理论上就只能采用较为复杂的空闲列表；

其实CMS里面也有一个清除整理的功能，所以只是理论上会采用空闲列表的分配。



- 本地线程分配缓冲（Thread Local Allocation Buffer，TLAB）：对象创建在虚拟机中频繁发生，即使仅仅修改一个指针所指向的位置，在并发的情况下也并不是线程安全的，可能出现正在给A对象分配内存，指针还没来得及修改，对象B又同时使用原来的指针来分配内存的情况：

那么解决方法有两种：

（1）同步锁定，JVM的采用**CAS**配上**失败重试**的方法保证更新操作的原子性

（2）线程隔离，把内存分配的动作按照线程划分在不同的空间之中进行，即每个线程在Java堆中预先分配一小块内存，称为本地线程分配缓冲（Thread Local Allocation Buffer，TLAB），哪个要分配内存，就在哪个线程的本地缓冲区中分配，只有本地缓冲区用完了，分配新的缓冲区的时候才需要同步锁定，虚拟机是否使用TLAB，可以通过 -XX：+/-UseTLAB参数来设定；使用 -XX:TLABSize=512k 设置TLAB的大小；使用这个参数可以打印虚拟机的默认参数值：-XX:+PrintFlagsFinal  或者  -XX:+PrintCommandLineFlags

给每个创建的线程预先分配一个小区域，这个小区域叫做本地线程分配缓冲，这些小区域是相互线程隔离的，每个线程只在自己的小区域里创建对象，分配内存，只有当这些小区域都用完了以后，才会去分配新的缓冲区给这个线程使用。当分配新的缓冲区的时候，考虑其他的线程可能也在分配新的缓冲区，因此需要对新的缓冲区使用同步锁定避免冲突。这个过程相当于**空间换时间**。



## 1.9 JVM堆内存中的对象布局

![image-20230326103654765](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261036912.png)

![image-20230326103809731](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261038936.png)

![image-20230326104436368](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261044440.png)

对象的实例数据，比如定义一个User对象，User的成员变量age，age=18，这个就是对象的实例数据

![image-20230326104639100](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261046172.png)

对齐填充不是必须需要的，主要用于占位，如果一个对象（包括它的对象头，实例数据）不是8字节的整数倍的话，就会使用一个对齐填充的方式使这个对象变成8字节的整数倍。



# 2 堆溢出

## 2.1 什么情况JVM堆内存溢出

![image-20230326144123218](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261441375.png)

![image-20230326013112145](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303260131278.png)

由于一直创建对象，对象存放在堆里，而且使用了orderlist对新创建的对象进行引用，则表示这个对象一直要使用，所以每次创建的order都不是垃圾，所以不能收回，但是一直创建order对象的话，就会报Java heap space的错。

只要把orderlist的引用去掉，即删掉add方法，那么orderlist这个局部变量 就只指向堆内存里的ArrayList对象 而并没有对order的对象进行引用。所以GC可以对order对象进行回收。

可以使用VisualVM查看堆溢出的情况：

![image-20230326151159705](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261511787.png)

![image-20230326151214482](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261512554.png)



**分析堆溢出的的原因：**

以下是常用参数：

![image-20230326151521957](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261515049.png)

![image-20230326151548597](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261515655.png)

常用最后两个来分析堆溢出的原因：

可使用eclipse 官网下的 MAT工具进行分析：

![image-20230326152429736](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261524779.png)

该文件直接解压缩就可以用。

首先打开这个软件：

![image-20230326153449043](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261534093.png)

打开这个软件之后，再导入hprof文件，之后就会生成如下图的报告：

![image-20230326153546749](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261535973.png)

我们看到如下信息：

![image-20230326153622492](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261536676.png)

该图显示：有一个局部变量占据了99.97%的内存，并且该对象是一个Object[] 数组造成的。

查看See stacktrace可以查看具体报错信息。

查看下面的Keywords：

可以发现造成错误的原因是一个 java.lang.Object[] 类型的变量造成的；

并且报错信息位于 HeapOOM的main方法里，同时报错源码在main方法的11行。

所以我们可以根据以上信息进行排错。

![image-20230326154027459](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261540616.png)

我们可以看到源码11行，在创建Order对象的时候报错，也就是说内存不够了，导致Order创建失败，内存溢出。

同时也可以点这两个按钮来查看内存消耗的排序：

![image-20230326154553019](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261545205.png)

main方法执行，创建了一个Thread线程，然后这个线程创建了一个ArrayList对象，这个ArrayList对象的底层实现是Object[]，然后就是由于com.myjvm.memory.Order创建的对象很多，是因为Order类里面的bytes也很大，因为在Order对象里面的bytes是1M。

![image-20230326155120771](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261551850.png)

然后我们也可以搜索：

![image-20230326155211524](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261552614.png)



另外，我们也可以使用下面这个按钮：

![image-20230326155357363](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261553441.png)

我们可以根据百分比一层一层的找出原因：

![image-20230326155545333](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261555400.png)

我们可以知道，原来是创建了很多的Order对象，没有被回收器回收，内存不够用，导致内存溢出。



## 2.2 JVM如何判断对象可以被回收

![image-20230326160734646](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261607853.png)

![image-20230326160904601](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261609677.png)

![image-20230326161018026](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261610096.png)

![image-20230326161309233](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261613334.png)

比如如下代码中：

![image-20230326161414301](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261614396.png)

**new Order()**就可以作为一个**GC Root**。



2、**方法区/元空间的类静态属性引用的对象可以作为 GC Root；**

3、**方法区/元空间中的常量引用的对象可以作为GC Root；**

![image-20230326161829426](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261618510.png)

4、**在本地方法栈中JNI（即通常所说的Native方法）引用的对象；**

5、**java虚拟机内部的引用，如基本数据类型对应的Class对象，一些常驻的异常对象（比如NullPointException、OutOfMemoryError）等，还有系统类加载器；**

6、**所有被同步锁（synchronized关键字）持有的对象；**

7、**反映虚拟机内部情况的JMXBean、JVMTI中注册的回调，本地代码缓存等；**

8、**其他可能临时性加入的对象；**



## 2.3 谈谈java中不同的引用类型

![image-20230326162647498](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261626738.png)

1、**强引用：**只要这个引用没有释放、一直存在，即使是内存不足，内存溢出了，这个对象也是不能回收的。

2、**软引用**：内存充足时不回收，内存不足时就回收，即便是有这个引用存在，也会被回收；**它适合用在缓存里面，缓存充足就不回收，缓存不足就清掉**；

3、**弱引用**：不关内存是否充足，只要GC一运行，就会回收该引用对象；

![image-20230326163239447](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261632545.png)

4、**虚引用**：这个其实暂时可以忽略也行，因为很少使用，它形同虚设，就像没有引用一样，其作用就是该引用对象被GC回收的时候触发一个系统通知，或者触发进一步的处理；





# 3 JVM分代模型

## 3.1 JVM堆内存分代模型

![image-20230326163904412](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261639493.png)

![image-20230326163921665](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261639831.png)



**1、请介绍一下JVM中新生代的垃圾回收过程？**

![image-20230326164536338](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261645499.png)

- 创建的新对象首先被放在Eden区，当Eden区被放满了以后，就触发了Minor GC回收器。然后Minor GC就回收掉了大部分的垃圾对象，还有少数的不可回收的对象被放到了From Survivor区，此时Eden区已经没有没有可用的对象。
- 然后又开始创建对象，新生的对象首先又被放在了Eden区，当Eden被放满了以后，又触发了Minor GC垃圾回收器，此时垃圾回收器会回收掉Eden区中大部分的垃圾，然后不可回收的对象被移到了To Survivor区，垃圾回收器也会对From Survivor进行回收，回收掉大部分的垃圾，还有一部分不可回收的垃圾被移到了To Survivor区，此时Eden，From Survivor都是空的，没有对象。
- 然后又开始创建对象，新生的对象首先被放到了Eden区，当Eden被放满了以后又会触发Minor GC进行垃圾回收，此时回收器会将Eden区和To Survivor区可回收的大部分垃圾给清理掉，然后不可回收的对象被移到了From Survivor区，此时 Eden区 和 To Survivor区 是空的，没有可用的对象
- 循环以上的过程，当Eden区又被放满了，触发了Minor GC垃圾回收器，会回收掉Eden区和From Survivor区的大部分可回收的垃圾，不可回收的对象被移到了To Survivor区。此时，Eden 和 From Survivor 变成空的。
- 如果一个对象，被垃圾回收了15次还没有被回收就会被移到老年代。放在老年代的对象表示这是一个需要长期存活的对象。
  - 官方默认：并行（吞吐量）收集器的默认值时15；CMS收集器的默认值为6；
  - 默认使用的并行垃圾回收器

![image-20230326170815310](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261708416.png)

![image-20230326170842650](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261708787.png)



## 3.2 JVM对象动态年龄判断是怎么回事？

**除了对象被垃圾回收15次进入老年代之外，还有另外一个动态判断年龄的条件，并不一定到达15岁就放到老年代，有可能没到15岁就被放入到老年代**：

![image-20230326172043704](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261720846.png)

![image-20230326172145086](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261721193.png)

**这种情况下，年龄累加的总和并没有到50%，因此没有对象被移到老年代。**



![image-20230326172310937](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261723023.png)

**在经过了一次垃圾回收以后，从1到4岁的总和为51%大于50%，但是此时并没有大于4岁的对象，因此没有对象被移入到老年代。**



![image-20230326172514840](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261725998.png)

**又经历过一次垃圾回收，从小到大进行累加，发现加到3岁时，年龄的总和已经大于50%，因此比3大的都会被移到老年代。**





## 3.3 什么是老年代空间分配担保机制

![image-20230326173611244](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261736351.png)

![image-20230326173642534](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261736732.png)

**过程说明：**

**新生代Minor GC后剩余存活的对象太多，无法放入Survivor区，此时就必须将这些存活的对象直接转移到老年代去，如果此时老年代空间也不够怎么办？**

- 1、执行一次Minor GC之前，JVM会先检查一下老年代的可用空间，是否大于新生代所有对象的总大小，因为在极端的情况下，可能新生代Minor GC以后，新生代的所有对象都需要存活，而Survivor区也放不下，那么就会造成新生代所有对象全部要进入老年代；
- 2、如果**老年代的可用内存大于新生代所有对象总大小**，此时就可以放心大胆的对新生代发起一次Minor GC，因为Minor GC之后即使所有的对象都存活，Survivor区放不下了吗，也可以转移到老年代去。
- 3、如果执行Minor GC之前，检测发现**老年代的可用空间已经小于新生代的全部对象总大小**，那么就会进行下一次判断，判断老年代的可用空间大小，是否大于**之前每一次Minor GC后进入老年代的对象的平均大小**，如果判断发现老年代的内存大小，大于之**前每一次Minor GC之后进入老年代的对象的平均大小**，那么就是说可以冒险尝试一下Minor GC，但是此时真的可能有风险，那就是Minor GC过后，剩余的存活对象的大小，大于Survivor空间的大小，也大于老年代可用空间的大小，老年代都放不下这些存活对象了，此时就会触发一次“**Full GC**”；
- 所以老年代的空间分配担保机制的目的：**是为了避免频繁地进行Full GC；**因为“Full GC”是比较耗时的，影响系统吞吐量的。
- 如果Full GC之后，老年代还是没有足够的空间存放Minor GC过后的剩余存活的对象，那么此时就会导致 “OOM” 内存溢出。
- 以上过程是针对JDK1.8的版本。**如果是JDK1.6，它会有一个参数 -XX:+/-HandlePromotionFailure 用于开启是否要进行空间担保机制。**

