[TOC]

## 3.4 什么情况下对象会进入老年代

![image-20230326180345083](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261803179.png)

![image-20230326181101446](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261811671.png)

**如果必须使用此参数进行调优，可以考虑新生代使用 ParNew 垃圾回收器，老年代使用 CMS 垃圾回收器。**

 

## 3.5 JVM运行时数据区 元空间的特点及作用

1、在JDK1.8开始才出现元空间的概念，之前叫方法区/永久代

2、元空间与java堆类似，是线程共享的内存区域

3、存储被加载的类信息，常量，静态变量，常量池，即时编译后的代码等数据

4、元空间采用的是本地内存，本地内存有多少元空间，他就能扩展到大多大的空间，也可以设置元空间的大小。

​	`—XX:MetaspaceSize=20M -XX:MaxMetaspaceSize=20M`

5、元空间很少有GC垃圾收集，一般该区域回收条件苛刻，能回收的信息很少，所以GC很少回收。

6、元空间内存不足时，将抛出OutOfMemoryError。



## 3.6 JVM本机直接内存的特点及作用

1、直接内存（Directory Memory）不属于JVM运行时数据区，是本机直接物理内存。

2、像在JDK1.4 中新加入了NIO（New Input/Output）类，一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，**它可以直接通过Native函数直接分配堆外内存**，然后通过一个存储在Java堆中的DirectByteBuffer对象作为这块内存的引用进行操作，这样能在一些场景中显著提升性能，因为避免了在Java堆和Native堆中来回复制数据。

3、可能导致OutOfMemory异常出现；（netty的底层使用了NIO，因此可能会报错OutOfMemory异常）



## 3.7 JVM本机直接内存溢出问题

![image-20230505113211447](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051132673.png)

直接内存（Directory Memory）的容量大小可以通过：`-XX:MaxDirectMemorySize`参数来指定，该参数表示设置 New I/O（java.nio程序包）直接缓冲区分配的最大总大小（以字节为单位）；默认情况下，大小设置为0，这意味着JVM自动为NIO直接缓冲区分配选择大小。

![image-20230505113306095](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051133191.png)

直接内存溢出导致的问题：不会生成Heap Dump文件。



## 3.8 说几个与JVM内存相关的核心参数

![image-20230505131836737](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051318876.png)



![image-20230505132154208](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051321269.png)

**查看默认的堆大小即默认的垃圾收集器：**

```
java -XX:+PrintCommandLineFlags -version
```



## 3.9 堆为什么要分成年轻代和老年代

![image-20230505153829622](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051538881.png)

**因为年轻代和老年代不同的特点，需要采用不同的垃圾回收算法**；

年轻代的对象，它的特点是创建之后很快就会被回收，所以需要采用一种垃圾回收算法。

老年代的对象，它的特点是需要长期存活，所以需要另外一种垃圾回收算法；

所以需要分成两个区域来放不同的对象；

**1、绝大多数的对象都是朝生夕灭**

如果一个区域中大多数的对象都是朝生夕灭，那么把他们集中放在一起，每次回收时只关注如何保留少量的存活对象，而不是去标记那些大量将要被回收的对象，就能以较低的代价回收到大量的空间。



**2、熬过了很多次垃圾回收的对象就越难以回收**

如果需要长期存活的对象，那就把它们存放在一起，虚拟机便可使用较低的频率来回收这个区域，这就同时兼顾了垃圾收集的时间开销和内存的空间利用率；



**3、jvm划分新生代，老年代之后，垃圾回收器可以每次回收其中一个或某些部分的区域，同时也有了“Minor GC”，“Major GC”，“Full GC”这样的回收类型的划分**

Minor GC/Young GC：**新生代收集**

Major GC/Old GC：**老年代收集**

Full GC：**整堆收集**，收集整个**Java堆**和**元空间/方法区**的垃圾回收

Mixed GC：**混合收集**，收集整个新生代以及部分老年代的垃圾回收，目前只有G1收集器会有这种行为；



**4、针对不同的区域对象存亡特征采用不同的垃圾收集算法**

标记-复制算法

标记-清除算法

标记-整理算法



## 3.10 JVM堆的年轻代为什么要有两个Survivor区

![image-20230505160254596](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051602697.png)

**1、如果没有Survivor区会怎么样**

此时每触发一次Minor GC，就会把Eden区的存活对象复制到老年代，这样当老年代满了以后就会触发Major GC/Full GC（通常伴随着Minor GC），Full GC比较慢也比较耗时，要尽量避免Full GC，所以必须要有Survivor区；



**2、如果只有一个Survivor区会怎么样**

刚刚创建的对象在Eden区中，一旦Eden满了，触发一次Minor GC，Eden中存活的对象就会移动到Survivor中，下一次Eden满了的时候，此时进行Minor GC，Eden和Survivor中垃圾都会被回收，但是将Eden垃圾回收以后Eden会有一部分存活的对象，Survivor区也会有一部分存活的对象，Survivor区中的对象是离散的分布，会有一些空闲碎片，但是如果此时Eden区中存活的对象是一个比较大的对象，那就难以将这个对象复制到Survivor区中，Survivor中没有连续的空间存储这个大对象，那就会又触发一次Minor GC，又会清理一些对象，以便能够放的下那个大对象。此时就不得不使用 “标记-整理”的方法把Survivor区中离散的对象整理为连续空间的对象，这样做也是比较损耗性能的。

如果此时还有另外一个Survivor1区的话，那么就可以把Eden和Survivor0区中存活的对象整齐的复制到另一个Survivor1区中，然后直接清空Eden和Survivor0区就行。



## 3.11 Eden区和Survivor区的空间大小比值为什么是默认8:1:1

一个Eden区，新生代出生的地方；

两个Survivor区，一个用来保存上次新生代GC存活下来的对象，还有一个空着，在新生代GC时把Eden+survivor中存活的对象复制到这个空的survivor中；

统计和经验表明，90%的对象朝生夕灭，存活的时间极短，每次GC会有90%的对象被回收，剩下的10%要预留一个survivor空间去保存。



## 3.12 请介绍一下JVM中的垃圾回收算法

![image-20230505163632684](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051636755.png)



> **标记-清除算法**

标记-清除算法是最基础的收集算法，后续的很多垃圾回收算法是基于该算法发展而来的，它分为“标记”和“清除”两个阶段。

**1、标记**

**标记出所有需要回收的对象**，在标记完成以后，统一回收掉所有被标记的对象，也可以反过来，**标记出所有存活的对象**，在标记完成后，统一回收所有未被标记的对象，标记过程就是对象是否属于垃圾的判定过程，基于 **可达性分析算法** 判断对象是否可以回收。

**2、清除**

标记后，对所有被标记的对象进行回收。

![image-20230505165038467](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051650560.png)

优点：基于最基础的可达性分析算法，实现简单，后续的收集算法都是基于这种思想实现。

缺点：1、执行效率不稳定，如果Java堆中包含大量的对象，而且其中大部分是需要被回收的，这是必须进行大量的标记和清除的动作，导致标记和清除两个过程的执行效率都随着对象数量增长而降低。

​			2、**内存空间的碎片化问题**，标记，清除之后会产生大量的不连续的内存碎片，空间碎片太多可能会导致当以后在程序中需要分配较大对象时无法找到足够的连续内存而不得不提前触发另一次垃圾回收。



> **复制算法**

复制算法是 **标记-复制算法** 的简称，将可用的内存按照容量分为大小相等的两块，每次只使用其中一块，当这一块的内存用完了以后，就将还存活的对象复制到另一块内存上，然后再把已使用过的内存空间一次清理掉。

![image-20230505191515724](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051915815.png)

优点：实现简单，效率高，解决 **标记-清除**算法导致的**内存碎片**的问题。

缺点：

- 代价太大，将可用内存缩小一半，空间浪费太多了
- 另外对象存活率较高时就要进行较多的复制操作，效率会降低。
- 该算法不适合老年代，因为老年代中大部分的对象都需要存活，每次复制就需要复制大量的对象，效率降低。

一般虚拟机都会采用该算法来回收新生代，但是JVM对复制算法进行了改进，**JVM并没有按照1:1的比例来划分新生代的内存空间**，因为通过大量的统计和研究表明，90%以上的对象都是朝生夕灭的，所以JVM把新生代分为一块较大的Eden区和两个较小的Survivor区。每次使用只使用其中的Eden区和其中一块Survivor区，发生垃圾回收时，将Eden区和Survivor区中仍然存活的对象复制到另一块Survivor空间上，然后直接清理掉Eden和已用过的那块Survivor空间，HotSpoot虚拟机默认Eden和Survivor的大小比例是：8:1:1 ，即每次新生代中可用内存空间为整个新生代容量的90%（Eden的80%加上一个Survivor的10%），只有另外一个Survivor空间即10%的新生代会被浪费，当然，90%的对象可被回收仅仅是大部分的情况，我们无法百分百的保证每次回收只有不多于10%的对象存活，因此JVM还有一个安全担保机制的安全设计，当Survivor空间不足以容纳一次Minor GC之后存活的对象时，就需要依赖其他内存区域（实际上就是老年代）进行空间分配担保（Handle Promotion，也就是冒险Minor GC一下）。



> **标记-整理算法**

标记-整理算法是根据老年代的特点而产生的。

1、标记

标记过程与上面的标记-清理算法一致，也是基于可达性分析算法进行标记。

2、整理

和标记-清理不同的是，该算法不是针对可回收对象进行清理，而是根据存活对象进行整理，让存活对象都向一端**移动**，然后直接清理掉边界以外的内存。

![image-20230505195917948](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305051959022.png)

而标记-清除算法不移动存活对象，导致有大量不连续空间，即内存碎片，而老年代这种每次回收都有大量存活对象的区域，移动存活对象并更新所有引用这些对象的引用，这是一种比较耗时的操作，而且这种对象移动操作必须全程暂停用户程序才能进行，像这样的停顿我们也称为“Stop the World” 即 STW。

但是即便是移动存活的对象也是比较耗时的操作，但是如果不这么做，那么在充满内部碎片的空间中分配对象，又影响对象的分配和访问的效率，所以JVM权衡两者之后，还是采用了移动存活对象的方式，也就是对内存进行了整理。

另外像cms垃圾回收器，平时大多数都采用 **标记-清除** 算法，暂时容忍内部碎片的存在，直到内存空间的碎片化程度已经大到影响对象分配时，再采用一次 **标记-整理**算法收集一次，以获得规整的内存空间，所以像基于 **标记-清除** 算法的cms垃圾回收器面临空间碎片化过多时就会进行一次整理。

优点：

- 不会像复制算法那样划分两个区域，提高了空间利用率
- 不会产生不连续的内存碎片

缺点：

- 效率问题，除了像标记-清除算法的标记过程以外，还多了一步整理过程，效率变低。



> **分代收集算法**

![image-20230505211810445](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305052118542.png)

新生代中，每次进行垃圾回收都会发现大量的对象死亡，只要少量的对象存活，**因此采用复制算法，只需要付出少量存活对象复制成本就可以完成收集**。

老年代中，因为对象的存活率很高。采用 **标记-清除**，**标记-整理**算法来进行回收。





## 3.13 不同的垃圾回收器

![image-20230505214433837](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305052144933.png)

如上图所示，一共有7种作用于不同分代的垃圾回收器，如果两个收集器之间存在连线，则说明它们可以搭配使用，垃圾回收器所处区域表示它是属于新生代收集器还是老年代收集器；

新生代收集器：Serial、ParNew，Parallel Scavenge

老年代收集器：CMS、Serial Old、Parallel Old

整堆收集器：G1

垃圾收集器的最前沿的成果：ZGC 和 Shenandoah



## 3.14 Serial 收集器

新生代收集器，最早的收集器，**单线程**的，**收集时需暂停用户线程的工作**，所以有卡顿现象，效率不高，致使java团队一直改进垃圾收集器的算法和实现，但Serial收集器简单，不会有线程切换的开销，是Client模式下默认的垃圾收集器，-client、-server；

参数：-XX:+UseSerialGC

`java -XX:+PrintFlagsFinal -version` 打印jvm默认的参数值。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305052233504.png)





## 3.15 ParNew收集器

它是**新生代收集器**，就是Serial收集器的**多线程版本**，大部分基本一样，单CPU下，ParNew还需要切换线程，可能还不如Serial；

Serial和ParNew收集器可以配合CMS收集器，前者收集新生代，后者收集老年代，“-XX:+UseConcMarkSweepGC”：指定使用CMS以后，会默认使用ParNew作为新生代垃圾收集器。

“-XX:+UseParNewGC”：强制指定使用ParNew

"-XX:ParallelGCThreads=2"：指定垃圾收集器的线程数量，ParNew默认开启的收集线程数与CPU的数量相同。

![image-20230505225506643](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305052255756.png)





## 3.16 Parallel Scavenge收集器

![image-20230505214433837](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305052256530.png)

简称Parallel，它是新生代收集器，基于**复制算法**，**并行的多线程收集器**（与ParNew收集器类似），侧重于**达到一个可控的吞吐量**，虚拟机运行100分钟，垃圾收集1分钟，则吞吐量为99%，有的时候我们也把垃圾收集器叫吞吐量垃圾收集器或者吞吐量优先的垃圾收集器； **而且这个垃圾收集器是jvm默认的垃圾收集器。**

它提供一个参数设置吞吐量：

`-XX:MaxGCPuaseMillis`该参数设置大于0的毫秒数，每次GC的时间将尽量保持不超过设定的值，但是这个值也不是越小越好，GC暂停时间越短，那么GC的次数就变得越频繁。

`-XX:+UseAdaptiveSizePolicy`自适应新生代大小策略，默认这个参数是开启的，当这个参数被开启之后，就不需要人工指定新生代的大小（`-Xmn`）、Eden与Survivor区的比例（`-XX:SurvivorRatio`）、晋升老年代对象的大小（`-XX:PretenureSizeThreshold`）等细节参数，虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数已提供最适合的停顿时间获得最大的吞吐量，这种调节方式称为垃圾收集的自适应的调节策略（GC Ergonomics）；如果我们不知道怎么对jvm进行调优，我们可以使用Parallel Scavenge收集器配合自适应调节策略，把内存管理的调有任务交给虚拟机去完成或许是一个不错的选择，只需要把基本的内存数据设置好（如-Xmx设置最大堆），然后使用 `-XX:MaxGCPauseMillis` 参数（最大停顿时间），那具体参数的调节细节可以交给虚拟机完成，自适应调节策略是Parallel Scavenge收集器区别于ParNew收集器的一个重要特性。

参数： `-XX:+UseParallelGC` 指定使用Parallel Scavenge 垃圾收集器。

`java -XX:+PrintCommandLineFlags -version` 打印jvm默认初始堆和最大堆大小以及垃圾收集器

`java -XX:+PrintFlagsFinal -version` 打印jvm默认的参数值；

`java -XX:+PrintFlagsFinal -version` 打印jvm所有的默认的参数值；

[虚拟机参数官方文档](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html#BGBCIEFC)

`-XX:+PrintGCDetails`

`-XX:+PrintGCDateStamps`

`-Xloggc:d:/dev/gc.log` 打印日志（包含以上共三行代码）

Parallel Scavenge 垃圾收集器中的Ergonomics负责自动的调节gc暂停时间和吞吐量之间的平衡，自动优化虚拟机性能；

下面是打印的日志：

![image-20230506192303742](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305061923819.png)

![image-20230506193050418](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305061930576.png)



## 3.17 Serial Old收集器

它是Serial收集器的老年代版本，同Serial一样，单线程，可在Client模式下运行，也可在Server模式下使用，采用 **标记-整理** 算法，Serial Old收集器也可以作为 CMS 收集器发生失败时的后备预案，在并发收集发生Concurrent Mode Failure时使用；

![image-20230506200650093](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305062006187.png)



![image-20230506201239774](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305062012857.png)



## 3.18 Parallel Old 收集器

是 **Parallel Scavenge** 的老年版本，多线程，标记整理算法，它是jdk1.6开始提供的；

在注重吞吐量和CPU的情况下，Parallel Scavenge新生代 + Parallel Old老年代是一个很好的搭配。

参数： `-XX:+UseParallelOldGC` 指定使用 Parallel Old 收集器；

![image-20230506204030784](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305062040906.png)









































