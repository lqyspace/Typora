[TOC]

# 1 从底层揭秘程序的栈和堆

![image-20230322130457958](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303221305057.png)



**`java`采用基于栈的结构**：

一个线程一个栈。

当程序开始执行的时候，他就开启一个主线程，这个主线程会维护一个栈。main方法先进入栈，会形成一个栈帧。当main方法执行的时候遇到了m方法，会在main方法中停住，然后去执行m方法，m方法进栈，也会形成一个栈帧。

当m方法执行完毕，就会弹栈消失，这里的弹栈指的是栈顶指针往下移动，这样就能保证一不会再执行之前的方法了。

堆空间是程序执行的时候，用来动态分配内容的空间，然后栈空间引用堆空间的地址，从而建立关联。

总结：

- 每一个线程都会维护一个线程栈，线程栈里面放的是一个个方法的调用，调用的是一个个栈帧的堆叠。
- **栈帧空间是自动释放的**，当一个方法调用完毕，栈顶指针自动指向下一个，不需要手动释放。因此栈空间是自动释放的。
- 堆空间的内容是手动new出来的，因此需要进行手动回收，回收是指**将原本不可用的，被占的空间标记为可用**。

![image-20230322140812169](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303221408485.png)





> **程序中最难调试的bug**

- 野指针
  - 同一个对象，两个指针，一个释放了，另一个不知道还拿来用
  - 同一个指针，不同位置，一个释放了，另一个线程源的指针不知道，还在使用
  - 不再指向任何对象的指针
  - NullPointerException



- 并发问题
  - 多线程并发访问同一个内存区域



> **语言发展历史**

- c / c++
  - 手工管理 malloc free / new delete
  - 忘记释放 memory leak - out of memory
  - 释放多次：可能导致其他线程写进的数据被清空，产生极其难以调试的bug，一个线程空间莫名其妙被另外一个释放了
  - 开发效率低；手工管理内存
- java python go
  - 对内存管理比较友好
  - 引入了GC（Garbage Collector）概念：应用线程只管分配，垃圾回收器负责回收；GC会占用CPU资源，因此运行效率比C/C++低
  - 开发效率高，降低程序员的门槛
- java vs c++
  - java
    - GC处理垃圾
    - 开发效率高，执行效率低
  - c++
    - 手工处理垃圾
      - 忘记回收
        - 内存泄露
      - 回收多次
        - 非法访问
      - 开发效率低，执行效率高



# 2 如何辨别什么是垃圾

在python中采用的是循环引用：

![image-20230322182229511](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303221822804.png)

当一个节点被引用的次数为0，则表示它是一个无用的节点，就是一个垃圾

当然，这个也会存在问题：假如存在这三个垃圾，循环指向，导致每个垃圾的指向不可能为0，因此不能被回收。

![image-20230322235155086](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303222351379.png)





然而java中采用的是 `Root Searching`：根可达

![image-20230322235332267](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303222353590.png)

就是从main方法开始，以树搜索的方式搜索调用的所有方法和变量，那些找不到的就是垃圾。



> 常见的垃圾回收器算法

![image-20230323001010087](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303230010214.png)



- Mark-Sweep（标记清除）

  ![image-20230323001157706](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303230011876.png)

  - 会将内存中的每个内存区域进行标记，不过这个有个缺点，标记会导致内存中的区域碎片化，当需要分配一个更大的内存区域时，可能会导致内存溢出



- Copying

  ![image-20230323001542646](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303230015815.png)

  - 会对内存区域进行划分，一半的内存用于存储，另一半的内存待使用。当进行垃圾回收时，，会使用Root  Searching算法，将可以找到的对象放进待使用的内存中，并进行排列。剩下其他的对象全部杀掉。这种方法简单效率高，但是浪费内存



- Mark-Compact（标记压缩）

  ![image-20230323002111693](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303230021900.png)

  - 垃圾回收器在回收的过程中，边回收边整理内存，并排列地整整齐齐的，前面是存活的进程，剩下的空间全是可用的，避免了硬盘的 碎片化，但是效率低。它是这三种中效率最低的。



# 3 垃圾回收器GC

> **垃圾回收器综合运用了以上三种方法**

GC综合使用这三种方法，针对不同的情况进行综合运用



> **垃圾回收器的演化**

![image-20230323002813200](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303230028523.png)



## 1 Serial

- GC演化
  - 随着内存大小而不断演进
  - 最开始的垃圾回收器有两个：Serial、Serial Old
    - 将一个统一的内存用统一的算法来算的话，总是会有毛病，因此为了综合运用，将一个内存划分两个区域：新生代、老年代
      - 新生代：刚诞生的对象优先存进新生代；GC会频繁的扫描新生代，扫描的过程中说不定就能扫描掉很多的垃圾
      - 老年代：经历过多次垃圾回收的对象，当经历垃圾回收的次数大于一个阈值，就会被存进老年代；而老年代中的对象，GC并没有经常去回收它，当内存不够的时候才会去回收它。
      - 因此对于不同的内存区域可以采用不同的GC算法。在新生代里使用Copying算法，在老年代使用Mark-Compact算法
    - 工业界统计，在新生代的每次垃圾回收都回收掉90%的对象，因此copying算法就没有必要分成两半了，因为剩下的对象就是10%，复制到待使用区也就是占用10%的空间，因此没必要分成两半这样浪费空间。因此就分成了默认的比例8:1:1,。
    - 当产生一个新对象时，把这个对象放进伊甸区，经过一次垃圾回收，还剩10%，把这10%的对象扔到survivor1，然后伊甸区的整体空间全部回收。当下次伊甸区又有新的对象进入时，垃圾回收器会将伊甸区和survivor1一起扫描，又扫描掉90%，把伊甸区活着的和survivor1中活着的都放进survivor2，再下一次扫描的时候又会扫描掉90%，把survivor2中的10%和伊甸区的10%拷贝到survivor1中，如此往复。当某个对象的年龄够了，就会被分配到老年区。
      - 如果survivor装不下的话，会把较老的对象存放进老年代
      - 当老年代满了，会产生FullGC（FGC），整体回收

![image-20230323005005228](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303230050501.png)



- GC的演化

  - 几兆 - 几十兆

    - Serial：单线程STW垃圾回收，年轻代和老年代都是单线程

      ![image-20230323153331572](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231533861.png)

      stop-the-world简称：STW，使用copying collector

      当多个方法运行一段时间以后，STW根据Root Searching算法，将垃圾全部清除，然后又过了一短时间，STW又会进行回收，仍然使用Root Searching算法

    - Serial Old：单线程，老年代

      ![image-20230323153902620](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303231539835.png)

      仍然使用STW，使用标记清除，mark-sweep-compact。

      原理同年轻代的 Serial 一样



## 2 parallel

GC的演化

- 随着内存大小的不断增长而演进
- 几兆 - 几十兆
  - Serial **单线程**STW垃圾回收，年轻代， 老年代
- 几十兆 - 上百兆1G
  - parallel Scavenge 和 parallel Old（ps、po），并行多线程
  - JDK8的默认垃圾回收器：ps 和 po
  - 使用命令可以查看：java -XX:+PrintCommandLineFlags -version
  - 原理：多个GC对多个线程上的方法进行垃圾回收