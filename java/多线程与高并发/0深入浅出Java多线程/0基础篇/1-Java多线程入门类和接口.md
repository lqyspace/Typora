[TOC]

# Java多线程入门类和接口

## 1 Thread类和Runnable接口

JDK提供了Thread类和Runnable接口来让我们实现自己的线程类。

- 继承Thread类，并重写run方法
- 实现Runnable接口的run方法

### 1.1 继承Thread类

![image-20230402004746291](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202304020048813.png)

MyThreadDemo测试类：

```java
public class MyThreadDemo {
    public static void main(String[] args) {
        MyThread my = new MyThread();
        MyThread my1 = new MyThread();

//        my.run();// 使用此调用方式并没有成功的启动多线程同时运行，而是my运行完了再my1运行
//        my1.run();

        my.start();
        my1.start();// 两个线程同时运行
    }
}
```

MyThread子类：

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i=0; i<100; i++){
            System.out.println(i);
        }
    }
}
```

**注意：**

1、为什么要重写run()方法？

​	因为run()是用来封装被线程执行的代码

2、run()方法和start()方法的区别？

​	run()：封装线程执行的代码，直接调用，相当于普通方法的调用

​	start()：启动线程，然后由jvm调用此线程的run()方法

3、注意，要调用 `start()`方法后，该线程才算启动！

> 我们在程序里调用start()方法后，虚拟机会为我们创建一个线程，然后等到这个线程第一次得到时间片时再调用run()方法。
>
> 注意，不可多次调用start()方法。在第一次调用start()方法后，再次调用start()方法会抛出异常。



### 1.2 实现Runnable接口（jdk1.8+）

![image-20230402222006540](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202304022220596.png)

代码示例：

线程类：

```java
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for (int i=0; i<100; i++){
//            System.out.println(getName + ":"+i);
            // 不能使用getName方法，因为这个方法并没有继承Thread类
            // 可以通过 Thread.currentThread().getName() 获取名字
            System.out.println(Thread.currentThread().getName() + ":"+i);
        }
    }
}
```

测试类：

```java
public class MyThreadDemo {
    public static void main(String[] args) {
        MyRunnable my = new MyRunnable();

//        Thread t1 = new Thread(my);
//        Thread t2 = new Thread(my);
        Thread t1 = new Thread(my, "hello");
        Thread t2 = new Thread(my, "java");

        t1.start();
        t2.start();
    }
}
```

**总结**：

> 实现多线程的方式有两种

- 继承Thread类
- 实现Runnable接口

> 相比继承Thread类，实现Runnable接口的好处

- 避免了单继承的局限性
  - **在实现一个接口的同时可以继承一个类**
- 适合多个相同程序的代码去处理同一个资源的情况，把线程和程序的代码，数据有效分离，较好的体现了面向对象的设计思想



### 1.3 Runnable接口

接下来我们来看一下Runnable接口：

```java
@FunctionalInterface
public interface Runnbale{
    public abstract void run();
}
```

可以看到`Runnable`是一个函数式接口，这意味着我们可以使用**Java 8的函数式编程**来简化代码。

示例代码：

```java
public class Demo {
    public static class MyThread implements Runnable {
        @Override
        public void run() {
            System.out.println("MyThread");
        }
    }

    public static void main(String[] args) {

        new Thread(new MyThread()).start();

        // Java 8 函数式编程，可以省略MyThread类
        new Thread(() -> {
            System.out.println("Java 8 匿名内部类");
        }).start();
    }
}
```



### 1.4 Thread类构造方法

Thread类是一个Runnable接口的实现类，我们来看看Thread类的源码。

```java
// Thread类源码

// 片段1 - 私有的构造方法
private Thread(ThreadGroup g, Runnable target, String name,
                   long stackSize, AccessControlContext acc,
                   boolean inheritThreadLocals){
    this.inheritedAccessControlContext =
                acc != null ? acc : AccessController.getContext();
}
    
    
// 片段2 - 构造方法调用 片段1
public Thread(Runnable target) {
    this(null, target, "Thread-" + nextThreadNum(), 0);
}

// 片段3 - 使用私有构造方法里面初始化AccessControlContest类型的私有属性
this.inheritedAccessControlContext =
    acc != null ? acc : AccessController.getContext();

// 片段4 - 两个对用于支持ThreadLocal的私有属性
ThreadLocal.ThreadLocalMap threadLocals = null;
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
```

我们挨个来解释一下 `Thread(ThreadGroup g, Runnable target, String name,long stackSize, AccessControlContext acc,boolean inheritThreadLocals)`方法里的参数

- g：线程组，指定这个线程是哪个线程组的
- target：指定要完成的具体任务
- name：指定线程的名称，多个线程的名称是可以重复的。如果不指定线程名称，剪片段2.
- acc：见片段3，用于初始化私有变量 `inheritedAccessControlContext`。

> 这个变量有点神奇，它是一个私有变量，但是在Thread类里面只有片段1对它进行了初始化，在exit方法把它设为null。其他没有任何地方使用它。一般我们是不会使用它的，那什么时候会使用这个变量呢？可以参考Stack Overflow的问题：[Restrict permissions to threads which execute third party software](https://stackoverflow.com/questions/13516766/restrict-permissions-to-threads-which-execute-third-party-software)；

- inheritThreadLocals：可继承的 ThreadLocal，见片段4，Thread类里面有两个私有属性来支持ThreadLocal。我们会在后面介绍ThreadLocal的概念。

实际情况下，我们大多数是直接调用下面两个构造方法：

```java
Thread(Runnable target);
Thread(RUnnable target, String name);
```



### 1.5 Thread类的几个常用方法

- currentThread()：静态方法，返回对当前正在执行的线程对象的引用
- start()：开始执行线程的方法，jvm会调用线程内的run()方法；
- yield()：yield在英语里面是放弃的意思，同样的，yield()指的是当前线程愿意让出对当前处理器的占用，但是需要注意的是，就算当前线程调用了yield()方法，程序在调度的时候，也还有可能继续运行这个线程的；
- sleep()：静态方法，使当前线程睡眠一段时间；
- join()：其他线程必须等待调用此方法的线程执行完毕，其他的线程才会有机会继续执行，内部调用的是Object类的wait方法实现的。



### 1.6 Thread类与Runnable接口的比较

实现自定义的线程类，可以使继承Thread类或者实现Runnable接口这两种方式：

- 由于Java单继承，多实现的特性，Runnable接口使用起来比Thread更灵活
- Runnable接口更符合面向对象，将线程单独进行对象的封装
- Runnable接口出现，降低了线程对象和线程任务的耦合性
- 如果使用线程时不需要使用Thread类的诸多方法，显然使用Runnable接口更为轻量

所以，我们通常优先使用 “实现Runnable接口” 这种方式的自定义线程类。



## 2 Callable、Feature与FeatureTask

通常来说，我们使用 `Thread`和 `Runnable`来创建一个新的线程。但是它们有一个弊端，就是 `run`方法是没有返回值的。而有时候我们希望开启一个线程去执行一个任务，并且这个任务执行完成后有一个返回值。

JDK提供了 `Callable`接口与 `Future`接口来解决这个问题。

### 2.1 Callable接口

`Callable`与 `Runnable`类似，同样是只有一个抽象方法的函数式接口。不同的是，`Callable`提供的方法是有返回值的，而且支持泛型。

```java
@FunctionalInterface
public interface Cannable<V>{
    V call() throws Exception;
}
```

那一般是怎么用 `Callable`的呢？`Callable`一般是配合线程池工具 `ExecutorService`来使用的。我们会在后续的章节进行介绍线程池的使用。这里只介绍 `ExecutorService`可以使用 `submit`方法来让一个 `Callable`接口去执行。它会返回一个 `Future`，我们后续的程序可以通过这个 `Future`的 `get`方法得到结果。

```java
public class CallableDemo implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 模拟计算需要1s
        Thread.sleep(1000);
        return 2;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newCachedThreadPool();
        CallableDemo callableTask = new CallableDemo();
        Future<Integer> res = executor.submit(callableTask);
        // 注意调用get方法会阻塞当前线程，直到得到结果
        // 所以实际编码过程中建议使用可以设置超时时间的重载get方法
        System.out.println(res.get());
    }
}
```

 输出结果：

```java
2
```



### 2.2 Future接口

`Future`接口只有几个比较简单的方法：

```java
public interface Future<V>{
    public abstract boolean cancel(boolean paramBoolean);// 试图取消一个线程
    public abstract boolean isCancelled();
    public abstract boolean isDone();
    public abstract V get() throws InterruptedException, ExecutionException;
    public abstarct V get(long paramLong, TimeUnit paramTimeUnit) throws InterruptedException, ExecutionException, TimeoutException;
}
```

cancal方法是试图取消一个线程的执行。

注意是试图取消，并不一定能够执行成功。因为任务可能已经完成，已经取消，或者一些其他的因素不能取消，存在取消失败的可能。boolean类型的返回值是是否取消成功的意思。参数paramBoolean表示是否采用中断的方式取消线程。

所以有时候，为了让任务有能够取消的功能，就是用Callable代替Runnable。如果为了可取消性而是用Future但又不提供可用的结果，则可以声明 Future<?> 形式类型，并返回null作为底层任务的结果。



### 2.3 FutureTask类

上面介绍了 Future 接口。这个接口有一个实现类叫 FutureTask。FutureTask是实现了RunnableFuture接口的，而RunnableFuture接口同时继承了Runnable接口和Future接口：

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    /**
     * Sets this Future to the result of its computation
     * unless it has been cancelled.
     */
    void run();
}
```

那 FutureTask类有什么用呢？为什么要有一个 FutureTask类？前面说到了Future只是一个接口，而它里面的 cancel，get，isDone等方法实现起来是非常复杂的。所以JDK就提供了一个FutureTask类供我们使用。

实例代码：

```java
// 自定义Callable，与上面的一样
class Task implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 模拟计算需要1s
        Thread.sleep(1000);
        return 2;
    }
    
    public static void main(String args[]) throws Exception {
        // 使用
        ExecutorService executor = Executors.newCachedThreadPool();
        FutureTask<Integer> futureTask = new FutureTask(new Task());
        executor.submit(futureTask);
        System.out.println(futureTask.get());
    }
}
```

使用上与第一个demo是有一点小区别的。首先，调用submit方法是没有返回值的。这里实际上是调用的submit(Runnable task)方法的。而上面的Demo，调用的是 submit(Callable<T> task)方法。

然后，这里直接使用 FutureTask直接get取值，而上面的demo是通过submit方法返回的Future去取值。

在很多高并发的环境下，有可能Callable 和 FutureTask会创建多次。FutureTask能够在高并发的环境下确保任务只执行一次。这块有兴趣的同学可以自己去研究一下。



### 2.4 FutureTask的几个状态

```java
/**
  *
  * state可能的状态转变路径如下：
  * NEW -> COMPLETING -> NORMAL
  * NEW -> COMPLETING -> EXCEPTIONAL
  * NEW -> CANCELLED
  * NEW -> INTERRUPTING -> INTERRUPTED
  */
private volatile int state;
private static final int NEW          = 0;
private static final int COMPLETING   = 1;
private static final int NORMAL       = 2;
private static final int EXCEPTIONAL  = 3;
private static final int CANCELLED    = 4;
private static final int INTERRUPTING = 5;
private static final int INTERRUPTED  = 6;
```

> state表示任务的运行状态，初始状态为NEW。运行状态只会在set、setException、cancel方法中终止。COMPLETING、INTERRUPTING是任务完成后的瞬时状态。

以上就是Java多线程几个基本的类和接口的介绍。可以打开JDK看看源码，体会这几个类的设计思路和用途吧！
