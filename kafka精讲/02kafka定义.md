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



