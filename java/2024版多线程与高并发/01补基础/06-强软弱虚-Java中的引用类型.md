# 强软弱虚-Java中的四种引用

> **强引用——普通引用**

![image-20240223173415903](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231734932.png)

![image-20240223173448352](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231734396.png)

finalize是产生OOM问题之一，在垃圾回收器进行回收时会进行调用`finalize`。

由于m设置为null，则不指向任何引用。此时`new M()`就会被垃圾回收器回收。



> **软引用**

![image-20240223173804960](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231738001.png)

![image-20240223174004710](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231740735.png)

m到SR是强引用，但是SR到字节数组则是软引用。

源码：

在此代码上配置虚拟机运行配置：最大推内存20M：`-Xmx20M`

```java
import java.lang.ref.SoftReference;

public class T01_SoftReference {
    public static void main(String[] args) {
        SoftReference<byte[]> m = new SoftReference<>(new byte[1024*1024*10]);
        System.out.println(m.get());// m.get()可以拿到软引用的内容
        System.gc();// 此时没有垃圾回收

        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(m.get());// 可以正常输出

        byte[] b = new byte[1024*1024*15];// 又分配了一个新的15M数组
        System.out.println(m.get());
    }
}

输出：
[B@6f539caf
[B@6f539caf
null
```

由于堆最大内存是20M，最开始的字节数组是10M，后来又分配了15M的内存给b，但是最大也就是20M，空间不够，因此之前的10M就被垃圾回收了，这样才能给b分配空间，所以再次输出`m.get()`时输出为空。

**软引用的用途主要在缓存上**。但是缓存的空间不够时，可以随时被垃圾回收。



> **弱引用**

![image-20240223203937735](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402232039874.png)

```java
public class T02_WeakReference {
    public static void main(String[] args) {
        WeakReference<M> m = new WeakReference<M>(new M());

        System.out.println(m.get());
        System.gc();
        System.out.println(m.get());

        ThreadLocal<M> tl = new ThreadLocal<>();
        // ThreadLocal对象：每个线程独立拥有，线程存在，ThreadLocal一直存在
        tl.set(new M());
        tl.remove();
        /*
        // tl.set(T t)如下：
        public void set(T value) {
            Thread t = Thread.currentThread();
            ThreadLocalMap map = getMap(t);
            if (map != null) {
                map.set(this, value);
            } else {
                createMap(t, value);
        }
    }
        
        */
    }
}

输出：
com.lqy.multiprocess.bean.M@79fc0f2f
null
finalize
```

![image-20240223184127992](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231841016.png)

**特点：**

- 垃圾回收器看到它就会把它回收











> **虚引用**：管理堆外内存

![image-20240223184506449](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231845493.png)

![image-20240223190536744](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231905779.png)

完整代码：

```java
public class T03_PhantomReference {
    private static final List<Object> LIST = new LinkedList<>();
    private static final ReferenceQueue<M> QUEUE = new ReferenceQueue<M>();
    public static void main(String[] args) {
        PhantomReference<M> phantomReference = new PhantomReference<>(new M(), QUEUE);
        new Thread(()->{
            while (true){
                LIST.add(new byte[1024*1024]);
                try {
                    Thread.sleep(1000);
                }catch (InterruptedException e){
                    e.printStackTrace();
                    Thread.currentThread().interrupt();
                }
                System.out.println(phantomReference.get());
            }
        }).start();

        new Thread(()->{
            while (true){
                Reference<? extends M> poll = QUEUE.poll();
                if(poll!=null)
                    System.out.println("----虚引用对象被jvm回收了-----" + poll);
            }
        }).start();

        try {
            Thread.sleep(500);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

虚引用：无论使用get方法多少次，都无法get到M对象。

==弱引用只要没有被垃圾回收器回收，那么就可以被get方法get到，但是虚引用纵使在没有被垃圾回收器回收的情况下也是不会被get到的，而且会随时被收回。==

虚引用的作用是：**管理堆外内存**

在NIO里提供了直接内存管理或者叫做堆外内存管理。

启用了nio的时候，jvm是可以直接管理操作系统的内存，则不需要在jvm里面重新copy一遍（这个叫zerocopy）。

==在垃圾回收器里，有一个专门的线程专门监听那些堆外内存的DirectByteBuffer对象没有了，随之把堆外内存给删掉，在C++的层面给删掉。==

那什么时候知道DirectByteBuffer没有了，当它被垃圾回收器回收以后，它的某一个信息会被放到一个队列里

![image-20240223185836553](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402231858715.png)

而垃圾回收器检测这个队列里面有了新的内容就说明，某一个直接内存管理的DirectByteBuffer对象被回收了。与此同时，垃圾回收器会对堆外内存进行处理。

凡是类似DirectByteBuffer的对象，都会挂一个虚引用，作为线程来讲监控这个队列Queue就行，只要这个队列有新内容就清理和这个对象相关的堆外内存。



**总结：**

- 强引用：正常的引用
- 软引用：内存不够使了会被垃圾回收器回收，经常用作缓存
- 弱引用：只要有弱引用指向的对象就会被垃圾回收器回收，应用：WeakHashMap
- 虚引用：管理对外内存，zerocopy零拷贝和他有关系