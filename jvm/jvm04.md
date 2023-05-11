[TOC]

## 4.1 你们线上环境的JVM都设置多大

线上：4核8G机器；

JVM：栈，堆，元空间

1、栈，默认是1m，也可以自己设置：-Xss512k，一个线程是1m，一个项目Tomcat可能有300个线程，300m；

2、堆：大概把机器的一半内存给堆，4G（新生代，老年代）；

CMS：1/3（默认新生代占1/3，老年代占2/3）,   2/3

G1: 6:4（新生代：老年代），一般大内存的情况用G1垃圾收集器，堆内存的大小最好在6G以上，这样效果才最好。

3、元空间，一般512M肯定够了；

![image-20230510233609846](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305102336988.png)

其实这种情况下使用CMS就行，因为堆内存是4G。



## 4.2 线上Java服务器内存飙升怎么回事

- 使用 `jmap -histo pid` 可以查看各个类所占内存大小的信息
- 使用 `jmap -heap pid` 可以查看堆 Eden，Survivor区，以及老年代各区所占内存大小的信息
- 使用 `jmap -dump:format=b, file=heappid.hprof pid` 将堆报错信息存在heappid的文件里，然后再使用`MemoryAnalyzer`来分析这个堆的报错的各个信息



## 4.3 线上Java项目的cpu飙升到100%怎么排查

第一步，使用 top   查看当前占用cpu最多的进程pid；

第二步，使用top -p pid 可以只看这个进程；

第三步，使用 top -H -p pid 可以查看该进程对应的线程的信息；

第四步，由于需要使用 `jstack pid` 来查看进程信息，而 `jstack` 是16进制的，因此可以先使用 `printf '%x' tid` 把某一线程转成16进制，便于查看。输入 `printf '%x' tid` 以后输出 一串字符，要记住这串字符。然后使用 `jstack pid` 查看输出的所有的线程信息，在所有的线程信息里面搜索上面提到的字符串的位置，即该位置就是 tid 这个线程的输出信息。



## 4.4 JVM堆溢出后，其他的线程是否可以继续工作？

**可以**

主要分为两种情况：

1、如果造成堆内存溢出的原因来自某一个方法或者某一个局部变量，当发生堆内存溢出之后堆内存就会被释放，因此是不影响其他的线程继续运行的。

2、如果造成堆内存溢出的变量是单例模式或者全局变量，当发生堆内存溢出后，引用不会被释放，因此会一直占用着堆内存，如果其他线程占用堆内存很少，那可能会继续运行，如果占用的堆内存很大的话，基本上就不能继续运行。



## 4.5 线上Java项目OOM了，怎么回事？

![image-20230511145302154](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111453348.png)



## 4.6 某一天晚上，线上Java服务器宕机了，怎么回事？

比如从后台数据库中查询到的数据太多，导致内存占满，容易导致宕机。

再比如SQL注入问题。





## 4.7 亿级流量的订单系统JVM优化

![image-20230511150813415](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111508566.png)



 ![image-20230511151419016](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111514454.png)

![image-20230511151343282](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111513592.png)

![image-20230511152440818](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111524952.png)

![image-20230511152729045](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111527122.png)

![image-20230511153021186](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111530320.png)

![image-20230511153057444](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111530514.png)

![image-20230511153454163](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111534348.png)

![image-20230511153549944](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111535072.png)

![image-20230511153805025](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111538151.png)

![image-20230511153828692](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111538763.png)

![image-20230511153937687](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111539799.png)

![image-20230511154129697](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111541882.png)

![image-20230511155123096](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202305111551242.png)





