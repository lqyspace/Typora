# volatile底层

特点：

- 可见性——一个线程的修改对其他的线程可见
- 禁止指令重排序——DCL单例是不是要加volatile的问题

> 乱序执行

是指cpu在执行两条指令时，如果两条指令存在先后顺序的关系，但是并没有什么依赖关系，且第一条执行的执行时间很长，第2条指令不用等待第1条指令执行完就可以立马执行第2条指令。

![image-20240223001404836](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402230014999.png)



> **volatile的可见性和禁止指令重排序是怎么实现的**

实例：

```java
import java.util.concurrent.TimeUnit;

public class T01_HelloVolatileTest {
    volatile boolean running = true;
    void m(){
        System.out.println("m start");
        while (running){

        }
        System.out.println("m end");
    }

    public static void main(String[] args) {
        T01_HelloVolatileTest t01HelloVolatileTest = new T01_HelloVolatileTest();
        new Thread(t01HelloVolatileTest::m, "t1").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        t01HelloVolatileTest.running = false;
    }
}
```

`volatile`可以保证线程之间的可见性。

《深入理解Java虚拟机）中有如下描述：

“观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个lock前缀指令。”

`lock`前缀指令实际上相当于一个**内存屏障**（也称作内存栅栏），内存屏障提供3个功能：

- 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行内存屏障这句话时，在它前面的操作已经全部完成；
- 它会强制将对缓存的修改操作立即写入主存
- 如果是写操作，它会导致其他CPU中对应的缓存行无效

![image-20240224222843354](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242228386.png)

![image-20240224222854730](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242228768.png)



> 额外知识：
>
> ![image-20240223161128232](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231611422.png)
>
> ![image-20240223161236879](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231612928.png)
>
> ![image-20240223161441730](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231614897.png)
>
> 超线程：一个ALU对应多个PC+Registers，所谓的四核八线程，也就是一颗CPU核中，只有一个ALU计算单元，但是可以有多个PC和Registers的组合，也就是一颗cpu可以运算两个线程，让ALU切换到不同的PC和registers的组合就行，比如下图：
>
> ![image-20240224211012221](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242110320.png)
>
> ![image-20240223161949601](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231619776.png)
>
> 工程实践探索出来，单核两线程最好。



> cpu读数据
>
> 按块读取
>
> 程序局部性原理，可以提高效率
>
> 充分发挥总线CPU阵脚等一次性读取更多数据的能力



> **系统底层如何保证数据一致性**

1、MESI如果能解决，就是用MESI

2、如果不能解决，就锁总线



> **系统底层如何保证有序性**

1、sfence mfence lfence 等系统原语

![image-20240224215730052](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242157152.png)

![image-20240224215954713](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242159878.png)

2、锁总线



> **volatile的五层实现**

1：java源码——加了volatile关键字

2：ByteCode字节码——加了ACC_VOLATILE

3：JVM的内存屏障（在JVM的层级内存屏障）

4、Hotspot实现——指令前加了lock

5、CPU级别

![image-20240224205844502](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242058621.png)

屏障两边的指令不可以重排，保障有序。

JVM的逻辑屏障（hotspot，J9等）

![image-20240223171724341](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231717474.png)

![image-20240223172157007](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231721089.png)

在volatile写操作之前加一个屏障，在volatile写操作之后加一个屏障；在volatile读操作之前加一个屏障，在volatile读操作之后加一个屏障。

![image-20240224214842717](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242148882.png)

这个不用背！！！

4：hotspot实现（其实就是锁总线）

![image-20240223172639462](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231726499.png)
