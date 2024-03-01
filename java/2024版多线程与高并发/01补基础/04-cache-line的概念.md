# cache line的概念 缓存行对齐 伪共享

![image-20240222225858373](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402222258557.png)

缓存行，一行数据64 字节字节。缓存行就是块，每次都一块数据。也就是CPU每次会读取一行的数据。

如上图，x和y的数据共有6个备份，当不同的计算单元修改了该行的数据时，就需要通知其他的线程该行的数据做了修改

**缓存一致性协议与volatile没有关系**



**多级缓存**：计算单元在读取数据时会从最近的缓存行中读取，如果L1没有就从L2读取，如果L2没有就从L3读取，以此类推，如果还是没有就从主内存中读取。

实例：

```java
public class T01CacheLinePadding {
    private static class T{
        public volatile long x = 0L;// volatile 数据可见性
    }
    public static T[] arr = new T[2];
    static {
        arr[0] = new T();
        arr[1] = new T();
    }
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(()->{
            for (long i = 0; i<1000_0000L; i++)
                arr[0].x = i;
        });
        Thread t2 = new Thread(()->{
            for (long i=0; i<1000_0000L; i++)
                arr[1].x = i;
        });

        final long start = System.nanoTime();
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println((System.nanoTime() - start)/100_0000);
    }
}
// 执行以上代码平均用时200sm
```

上述代码中分别对x，y修改了10000000次，x和y是在一个缓存行里的。每一次修改x或者y都需要通知对方一下，这是因为x，y是一个缓存行中的数据，在cpu层级的数据一致性是以cache line为单位的，要是一颗cpu里面的数据有修改，我就通知另外一颗cpu这行里面有数据进行了修改，你重新去主内存读一遍。

cpu是可以做到在**缓存行之间的数据一致性的**！这就是所谓的**缓存一致性协议**。

![image-20240224214533671](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402242145804.png)

实例：缓存行对齐

```java
public class T02CacheLinePadding {
    private static class Padding{
        public volatile long p1, p2, p3, p4, p5, p6, p7;
    }
    private static class T extends Padding{// 这个T占了64个字节
        public volatile long x = 0L;
    }
    public static T[] arr = new T[2];

    static {
        arr[0] = new T();
        arr[1] = new T();
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            for (long i=0; i<1000_0000L; i++)
                arr[0].x = i;
        });
        Thread t2 = new Thread(()->{
            for (long i=0; i<1000_0000L; i++)
                arr[1].x = i;
        });

        final long start = System.nanoTime();
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println((System.nanoTime() - start)/100_0000);
    }
}
```

由于1个缓存行是64个字节，上述代码中的一个对象T就是64字节，因此arr的两个元素在不同的缓存行里，对这两个元素的修改都不会通知另外一个计算单元，因此节省了不少的时间，上述代码平均用时80sm。这就是**缓存行对齐**。

> 缓存行：
>
> 缓存行越大，局部性空间效率越高，但读取时间慢
>
> 缓存行越小，局部性空间效率越低，但读取时间快
>
> 取一个折中值，目前多用64字节

---

**MESI Cache一致性协议**

MESI是指CPU核之间的数据一致性协议

![image-20240223000023890](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402230000028.png)

cpu每个cache line标记有四种状态：分别是Modified（修改）、Exclusive（独占）、Shared（共享）、Invalid（失效），也就是这四种状态的缩写MESI。

如果是x86的cpu也就是Intenal的cpu，用的就是MESI协议保存缓存数据的一致性，但是其他的cpu用的可不一定是缓存的一致性。

还有其他的缓存一致性协议：`MSI`、`MESI`、`MOSI`、`Synapse`、`Firefly`、`Dragon`

