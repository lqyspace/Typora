# CAS

![image-20240221162035846](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402211620920.png)

**比较并交换**：先读取一个值E，然后计算结果值V，当把V值写回时，此时比较E和当前新的值N，如果相等则将E更新为V；如果不相同的话，说明有其他的线程修改了原来的E值，此时将这个新的值读过来，经过计算会得到一个新值a，把这个新值a写回的时候，仍然比较原来的值是否发生改变，如果没有发生改变则更新为新值a，如果发生了改变，则在把这个新值b读过来，继续cas，直到成功为止。

**ABA：**如果中间其他的线程修改数次最后值和原值相同，则是ABA问题。

![image-20240221170724737](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402211707766.png)

**如何避免ABA问题：**

可以通过加版本号，或者加时间戳解决，或者保证单向递增或递减就不会出现此类问题。

atomic包下的`AtomicStampedReference`类：其compareAndSet方法首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志，如果全部相等，则以原子的方式将该引用的该标志的值设置为给定的更新值。

**CAS的底层实现是通过：cmpxchg=cas修改指令**

```cpp
lock cmpxchg 指令 # c语言
```

cas最底层是通过cmpxchg指令支撑的，单核cpu使用cmpxchg，多核cpu使用lock cmpxchg；

cmpxchg是非原子性操作，而lock是锁总线的，意思是cpu在执行后面的指令的时候只让自己使用而不让别人使用。加lock的目的是让cpu在执行后面的指令的时候不被其他的cpu打断，保证原子性。

lock支持所有的汇编语言。

**所以，cas是使用lock来保证执行指令的原子性。**



硬件：lock指令在执行后面指令的时候锁定一个北桥信号（不采用锁总线的方式）



**CAS导致的其他的问题：**

1、只能保证一个共享变量的原子操作

```java
@HotSpotIntrinsicCandidate
public final int getAndAddInt(Object o, long offset, int delta) {
    int v;
    do {
        v = getIntVolatile(o, offset);
    } while (!weakCompareAndSetInt(o, offset, v, v + delta));
    return v;
}
```

当对一个共享变量执行操作时，我们可以使用循环`CAS`的方式来保证原子操作，但是对多个共享变量操作时，循环`CAS`就无法保证操作的原子性，这个时候就可以用锁，或者有一个取巧的办法，就是把多个共享变量合并成一个共享变量来操作。比如有两个共享变量i＝2,j=a，合并一下ij=2a，然后用CAS来操作 ij。从Java1.5开始JDK提供了`AtomicReference`类来保证引用对象之间的原子性，可以把多个变量放在一个对象里来进行CAS操作。

2、循环时间长开销大

自旋CAS如果长时间不成功，会给CPU带来非常大的执行开销。



**对象在内存中的存储布局：**

引入依赖：

```xml
<dependency>
    <groupId>org.openjdk.jol</groupId>
    <artifactId>jol-core</artifactId>
    <version>0.17</version>
</dependency>
```

然后输入代码：

```java
public class T01 {
    public static void main(String[] args) {
        Object o = new Object();// 刚声明的对象有四部分组成：markword、class pointer、Instance data、padding
        System.out.println(ClassLayout.parseInstance(o).toPrintable());
    }
}
```

输出：

```java
# WARNING: Unable to get Instrumentation. Dynamic Attach failed. You may add this JAR as -javaagent manually, or supply -Djdk.attach.allowAttachSelf
java.lang.Object object internals:
OFF  SZ   TYPE DESCRIPTION               VALUE
  0   8        (object header: mark)     0x0000000000000001 (non-biasable; age: 0)
  8   4        (object header: class)    0x00000d58
 12   4        (object alignment gap)    
Instance size: 16 bytes
Space losses: 0 bytes internal + 4 bytes external = 4 bytes total
```

class之后应该紧跟着成员变量，实例数据，但是此处没有，所以就没有展示出来。

![image-20240221172603034](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402211726198.png)

对象分成两种：**普通对象**、**数组**

普通数组：

- 对象头（markword）：关于secret的所有信息都记录在这里，8个字节
- 类型指针（klass pointer）： 指向所属类，4个字节
- 实例数据（Instance data）：成员变量所在地
- 对齐（padding）：当整体的字节不能被8整除时，会进行对齐补充能被8整除。这因为在读内存中的值的时候会按照总线的宽度来读，能被8整除的话，会提高读效率

![image-20240221173646537](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402211736566.png)

我们可以看到java虚拟机使用了这两个命令：`-XX:+UseCompressedClassPointers`、`-XX:+UseCompressedOops`；我们知道jvm是64位的，也就是指针长度是64位的，也就是8个字节，但是由于使用了`-XX:+UseCompressedClassPointers`，所以指向`class`的指针默认是4个字节，而`-XX:+UseCompressedOops`普通对象指针也是4个字节。

![image-20240221173939110](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402211739176.png)

> Object o = new Object()在内存中占多少个字节？
>
> 对象头8个字节，class指针4个字节，实例数据为0，还有4个字节是用于对齐，所以说Object默认大小16个字节（这是在开启了压缩指针的情况下）；再加上4字节的实例名（普通对象指针 Object o），一共20字节。20字节不能被8整除，所以是24字节。
>
> 如果没有开启压缩指针，8字节的markword，8字节的class pointer，一共16字节可以被整除。如果使用了普通指针压缩4字节，没有实例数据，所以一共20字节，因为要被8整除，所以一共24字节（补4个空字节）。

**关于锁的信息在哪儿？**

```java
public class T01 {
    public static void main(String[] args) {
        Object o = new Object();
        System.out.println(ClassLayout.parseInstance(o).toPrintable());

        synchronized (o) {
            System.out.println(ClassLayout.parseInstance(o).toPrintable());
        }
    }
}
```

输出：

```java
# WARNING: Unable to get Instrumentation. Dynamic Attach failed. You may add this JAR as -javaagent manually, or supply -Djdk.attach.allowAttachSelf
java.lang.Object object internals:
OFF  SZ   TYPE DESCRIPTION               VALUE
  0   8        (object header: mark)     0x0000000000000001 (non-biasable; age: 0)
  8   4        (object header: class)    0x00000d58
 12   4        (object alignment gap)    
Instance size: 16 bytes
Space losses: 0 bytes internal + 4 bytes external = 4 bytes total

java.lang.Object object internals:
OFF  SZ   TYPE DESCRIPTION               VALUE
  0   8        (object header: mark)     0x000000c544eff210 (thin lock: 0x000000c544eff210)
  8   4        (object header: class)    0x00000d58
 12   4        (object alignment gap)    
Instance size: 16 bytes
Space losses: 0 bytes internal + 4 bytes external = 4 bytes total
```

由此可知：关于锁的信息记录在对象`o`的markword里面。
