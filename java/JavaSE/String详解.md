[TOC]



# 1 String 是如何实现的？

且看JDK1.8版本的String类源码，String内部实际的存储结构为 `char[]`：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence{ 
    /** The value is used for character storage. */
    private final char value[];
 }
```

- **final修饰类名**：String作为不可重写类，它保证了线程安全
- **Serializable实现接口**：String支持序列化
- **Comparable<String>实现接口**：String支持与同类型对象的比较与排序
- **CharSequence实现接口**：String支持字符标准接口，具备以下行为：length/charAt/subSequence/toString，在jdk8之后，CharSequence接口默认实现了chars()/codePoint()方法：返回String对象的输入流



另外JDK9与JDK8的类声明比较有明显差异，下面是JDK9的类源码描述：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    @Stable
    private final byte[] value;
    private final byte coder;
    @Native static final byte LATIN1 = 0;
    @Native static final byte UTF16  = 1;
    static final boolean COMPACT_STRINGS;
    static {
        COMPACT_STRINGS = true;
    }
 }
```

- **在JDK8中：String底层最终使用字符数组 char[] 来存储字符值；但在 JDK9 之后，JDK维护者将其改为了 byte[] 数组作为底层存储**（究其原因是 JDK 开发人员调研了成千上万的应用程序的 heap dump 信息，然后得出结论：大部分的 String 都是以 Latin-1 字符编码来表示的，只需要一个字节存储就够了，两个字节完全是浪费）。
- **在JDK9之后，String类多了一个成员变量coder，它代表编码格式**，目前String支持两种编码格式：LATIN1和UTF16.LATIN1需要用一个字节来存储。而UTF16需要使用2个字节或者4个字节来存储。



而实际上，JDK对String类的存储优化由来已久：

![image-20230321134609298](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303211346375.png)







# 2 String有多个重要的构造方法

> **`String(char[] value)`  分配一个新的 `String` ，以便它表示当前包含在字符数组参数中的字符序列。**

```java
public String(char value[]) {
    this(value, 0, value.length, null);
}
```



> **`String(String original)`  初始化新创建的`String`对象，使其表示与参数相同的字符序列;  换句话说，新创建的字符串是参数字符串的副本。**

```java
public String(String original) {
    this.value = original.value;
    this.coder = original.coder;
    this.hash = original.hash;
}
```



> **`String(StringBuffer buffer)`  分配一个新的字符串，其中包含当前包含在字符串缓冲区参数中的字符序列。 **

```java
public String(StringBuffer buffer) {
    this(buffer.toString());
}
```



> **`String(StringBuilder builder)`  分配一个新的字符串，其中包含当前包含在字符串构建器参数中的字符序列。 **

```java
public String(StringBuilder builder) {
    this(builder, null);
}
```



# 3 String类常用方法列表

String类（JDK8）提供了很多实用的方法，碍于篇幅，这里以列表的形式概括：

![image-20230321145803440](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303211458507.png)

![image-20230321145824462](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303211458517.png)



# 4 String问题延伸

## 4.1 为什么String类型要用final修饰

- String作为不可重写类，它保证了线程安全
- 当你在传参时，不用考虑谁会修改它的值，如果是可变类的话，则有可能需要重新拷贝一个新值出来进行传参，这样在性能上有一定的损失



## 4.2 == 和 equals 的区别是什么

- == 对于基本数据类型来说，是用于比较 ”值“ 是否相等

- equals 对于引用类型来说，是用于比较引用地址是否相等



## 4.3 String 和 StringBuilder、StringBuffer有什么区别

- String类型是不可变的，所以在字符串的拼接的时候如果使用String的话会降低性能
- StringBuffer是线程安全的，可变的字符序列。字符串缓冲区就像是一个String，但是却可以修改。在任何时间点，它包含一些特定的字符序列，但可以通过某些方法调用来更改序列的长度和内容。
  - 它提供了append和insert方法可用于字符串的拼接
  - 它使用了synchronized来保证线程的安全
- StringBuilder提供了可变的字符序列，当时不能保证同步。在一般情况下，应优先使用StringBuilder而不是StringBuffer，因为它的性能将更快
  - 它提供了append和insert操作
  - 在使用单个线程字符缓冲区的地方，可以使用StringBuilder代替StringBuffer



## 4.4 String 的 intern() 方法有什么含义





## 4.5 String类型在JVM（Java虚拟机）中是如何存储的？编译器对String做了哪些优化



