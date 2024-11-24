[toc]

## 1、由来

![image-20241114230113327](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411142301356.png)

前端埋点记录用户购买海购人参丸的行为数据（浏览、点赞、收藏、评论等）。

![image-20241114230233938](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411142302979.png)

而这些数据会被发送到日志服务器中，落盘到日志文件中，以文件的形式进行存储。

通过Flume监控日志文件中数据的变化，每产生一条日志都能监控的到，然后并把数据传到hadoop集群。

Hadoop的读写速度受于硬盘的影响，大概在100m/s，而日常中Flume的采集速度小于100m/s，所以速度是够用的。

但是双十一，六一八等购物活动，Flume的采集速度是大于200m/s的，此时Hadoop就无法承受此时的速度。

由此产生了kafka。

![image-20241114230926679](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411142309728.png)

## 2、定义

kafka的传统定义：Kafka是一个分布式的基于**发布/订阅模式**的消息队列（Message Queue），主要应用于大数据实时处理领域。

**发布/订阅：**消息的发布者不会将消息直接发送给特定的订阅者，而是将发布的消息分为不同的类型，订阅者只接受感兴趣的消息。



Kafka最新定义：是一个开源的分布式事件流平台（Event Streaming Platform），被数千家公司用于高性能的**数据管道**、**流分析**、**数据集成**和**关键任务应用**。



## 3、消息队列

目前企业中比较常见的消息队列产品主要有Kafka、ActiveMQ、RabbitMQ、RocketMQ等。

在大数据场景主要采用Kafka作为消息队列。在JavaEE开发中主要采用ActiveMQ、RabbitMQ、RocketMQ。

### 3.1、传统消息队列的应用场景

传统消息队列的主要应用场景包括：**缓冲**/**削峰**、**解耦**和**异步通信**。

**缓冲/削峰：**有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致的情况。

![image-20241120234907866](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411202351173.png)

传统的情境中，秒杀系统只能处理每秒1千万的请求，但是如果能引入消息队列，就可以处理双十一每秒10亿人的请求量。

**解耦：**允许你独立的扩展或修改两边的处理过程，只要确保他们遵循同样的接口约束。

![image-20241120235459644](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411202354689.png)

**异步通信：**允许一个用户把一个消息放入队列，但并不立即处理它然后在需要的时候再去处理他们。

![image-20241120235828920](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411202358967.png)

### 3.2、消息队列的两种模式

**1）点对点模式**：一个生产者对应一个发布者

**消费者主动拉取数据，消息收到后清除消息。**

![image-20241121000225689](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411210002719.png)

**2）发布/订阅模式**

- 可以有多个topic主题（浏览、点赞、收藏、评论等）
- 消费者消费数据之后，不删除数据
- 每个消费者相互独立，都可以消费的到数据

![image-20241121000500985](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411210005023.png)

**二者比较：**

- 点对点模式只有一个topic，而发布/订阅模式可以有多个topic
- 点对点模式消费完数据以后立马删除数据，而发布/订阅模式消费完数据以后不删除数据
- 点对点模式只有一个消费者，但是发布/订阅模式有多个消费者

### 3.3、Kafka架构

> **1、为了方便扩展，并提高吞吐量，一个topic分为多个partition**

![image-20241121001308887](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411210013925.png)

其中break0、break1，break2可以认为是3台服务器，正常来说，每台服务器可以存储8T-96T的数据，所以对于100T的数据、一台服务器是存不下的，所以可以分成3台服务器。

（1）**Producer**：消息生产者，就是向Kafka发消息的客户端。

（2）**Consumer：**消息消费者，向Kafka取消息的客户端

（3）**Consumer Group（CG）**：消费者组，由多个consumer组成。消费者组内每个消费者负责不同分区的数据，一个分区的数据只能由一个消费者消费；消费者组之间互不影响。所有的消费者均各属于某个消费者组，即消费者组是逻辑上的一个订阅者。

（4）**Broker：**一台kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic。

（5）**Topic：**可以理解为一个队列，生产者和消费者面向的都是一个topic。

（6）**Partition：**为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每一个partition是一个有序队列。

（7）**Replica**：副本。一个topic的每个分区都有若干个副本，其中包括一个Leader和若干个Follower。

（8）**Leader：**每个分区多个**副本的“主”**，生产者发送数据的对象，以及消费者消费数据的对象都是Leader。

（9）**Follower：**每个分区多个**副本中的”从“**，实时从Leader中同步数据，保持和Leader数据的同步。Leader发生故障时，某个Follower会成为新的Leader。

Zookeeper中记录着集群中Leader和Follower的上下线信息，以及谁是Leader和Follower。

随着时代的发展和业务的增长，Zookeeper已经不能满足需求，正在进行去Zookeeper化。

多个副本中会包含一个**Leader**和多个**Follower**，无论是生产还是消费，处理的对象都是Leader和Follower。



