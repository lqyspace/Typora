# Java线程通信

合理地使用Java多线程可以更好的利用服务器资源。一般来讲，线程内部有自己私有的线程上下文，互不干扰。但是当我们需要多个线程之间互相协作的时候，就需要我们掌握Java多线程的通信方式。本文将介绍Java线程之间的几种通信方式。

## 1 锁与同步

在Java中，锁的概念是基于对象的，所以我们经常将其称为对象锁。线程和锁的关系，一个锁同一时间只能被一个线程持有。如果其他的线程需要得到这个锁，就需要等待上一个锁被释放。

在我们的线程之间，有一个同步的概念。什么是同步呢，假如我们现在有2位正在抄暑假作业答案的同学：线程A和线程B。当他们正在抄的时候，老师突然来修改了一些答案，可能A和B最后写出的暑假作业就不一样。我们为了A,B能写出2本相同的暑假作业，我们就需要让老师先修改答案，然后A，B同学再抄。或者A，B同学先抄完，老师再修改答案。这就是线程A，线程B的线程同步。

可以解释为：线程同步是线程之间按照**一定的顺序**执行。

为了达到线程同步，我们可以使用锁来实现它。

我们先来看看一个无锁的程序：

```java
public class NoneLock {

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 100; i++) {
                System.out.println("Thread A " + i);
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 100; i++) {
                System.out.println("Thread B " + i);
            }
        }
    }

    public static void main(String[] args) {
        new Thread(new ThreadA()).start();
        new Thread(new ThreadB()).start();
    }
}
```

执行这个程序，你会在控制台看到，线程A和线程B各自独立工作，输出自己的打印值。如下是我的电脑上某一次运行的结果。每一次运行结果都会不一样。

```java
....
Thread A 48
Thread A 49
Thread B 0
Thread A 50
Thread B 1
Thread A 51
Thread A 52
....
```

那我现在有一个需求，我想等A先执行完之后，再由B去执行，怎么办呢？最简单的方式就是使用一个“对象锁”：

```java
public class ObjectLock {
    private static Object lock = new Object();

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 100; i++) {
                    System.out.println("Thread A " + i);
                }
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 100; i++) {
                    System.out.println("Thread B " + i);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(10);
        new Thread(new ThreadB()).start();
    }
}
```

这里声明了一个名字为`lock`的对象锁。我们在`ThreadA`和`ThreadB`内需要同步的代码块里，都是用`synchronized`关键字加上了同一个对象锁`lock`。

上文我们说到了，根据线程和锁的关系，同一时间只有一个线程持有一个锁，那么线程B就会等线程A执行完成后释放`lock`，线程B才能获得锁`lock`。

> 这里在主线程里使用sleep方法睡眠了10毫秒，是为了防止线程B先得到锁。因为如果同时start，线程A和线程B都是出于就绪状态，操作系统可能会先让B运行。这样就会先输出B的内容，然后B执行完成之后自动释放锁，线程A再执行。



## 2 等待/通知机制

上面一种基于“锁”的方式，线程需要不断地去尝试获得锁，如果失败了，再继续尝试。这可能会耗费服务器资源。

而等待/通知机制是另一种方式。

Java多线程的等待/通知机制是基于`Object`类的`wait()`方法和`notify()`, `notifyAll()`方法来实现的。

> notify()方法会随机叫醒一个正在等待的线程，而notifyAll()会叫醒所有正在等待的线程。

前面我们讲到，一个锁同一时刻只能被一个线程持有。而假如线程A现在持有了一个锁`lock`并开始执行，它可以使用`lock.wait()`让自己进入等待状态。这个时候，`lock`这个锁是被释放了的。

这时，线程B获得了`lock`这个锁并开始执行，它可以在某一时刻，使用`lock.notify()`，通知之前持有`lock`锁并进入等待状态的线程A，说“线程A你不用等了，可以往下执行了”。

> 需要注意的是，这个时候线程B并没有释放锁`lock`，除非线程B这个时候使用`lock.wait()`释放锁，或者线程B执行结束自行释放锁，线程A才能得到`lock`锁。

我们用代码实现一下：

```java
public class WaitAndNotify {
    private static Object lock = new Object();

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println("ThreadA: " + i);
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println("ThreadB: " + i);
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(1000);
        new Thread(new ThreadB()).start();
    }
}

// 输出：
ThreadA: 0
ThreadB: 0
ThreadA: 1
ThreadB: 1
ThreadA: 2
ThreadB: 2
ThreadA: 3
ThreadB: 3
ThreadA: 4
ThreadB: 4
```

在这个Demo里，线程A和线程B首先打印出自己需要的东西，然后使用`notify()`方法叫醒另一个正在等待的线程，然后自己使用`wait()`方法陷入等待并释放`lock`锁。

> 需要注意的是等待/通知机制使用的是使用同一个对象锁，如果你两个线程使用的是不同的对象锁，那它们之间是不能用等待/通知机制通信的。
