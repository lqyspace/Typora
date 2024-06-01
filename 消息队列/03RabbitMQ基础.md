[TOC]

# RabbitMQ

## SpringAMQP

任何语言只要遵循AMQP协议收发消息，都可以与RabbitMQ交互。

SpringAMQP提供了三个功能：

- 自动声明队列，交换机及其绑定关系
- 基于注解的监听器模式，异步接收消息
- 封装了RabbitTemplate工具，用于收发消息



### publisher

---

```java
public void testSimpleQueue() {
        // 队列名称
        String queueName = "simple.queue";
        // 消息
        String message = "hello, spring amqp!";
        // 发送消息
        rabbitTemplate.convertAndSend(queueName, message);
    }
```



### consumer

---

```java
// 利用RabbitListener来声明要监听的队列信息
    // 将来一旦监听的队列中有了消息，就会推送给当前服务，调用当前方法，处理消息。
    // 可以看到方法体中接收的就是消息体的内容
    @RabbitListener(queues = "simple.queue")
    public void listenSimpleQueueMessage(String msg) throws InterruptedException {
        System.out.println("spring 消费者接收到消息：【" + msg + "】");
    }
```



### WorkQueues

---

多个消费者共同处理消息处理，消息处理的速度就能大大提高。

prefetch：1 # 每次只能获取一条消息，处理完成才能获取下一个消息

- 多个消费者绑定到一个队列上，同一条消息只会被一个消费者处理
- 通过设置prefetch来控制消费者预取的消息数量



### 交换机

---

![image-20240329010409448](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290104495.png)

发布者给交换机发消息，交换机负责处理消息，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃；消息队列需要与交换机绑定。

**Exchange（交换机）只负责转发消息，不具备存储消息的能力**，因此必须绑定队列

交换机的类型有四种：

- **Fanout**：广播，将消息交给所有绑定到交换机的队列。我们最早在控制台使用的正是Fanout交换机
- **Direct**：订阅，基于RoutingKey（路由key）发送给订阅了消息的队列
- **Topic**：通配符订阅，与Direct类似，只不过RoutingKey可以使用通配符
- **Headers**：头匹配，基于MQ的消息头匹配，用的较少。



### Fanout交换机-广播

![image-20240329010653371](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290106441.png)

交换机把消息发送给绑定过的所有队列，订阅队列的消费者都能拿到消息；

`rabbitTemplate.convertAndSend(exchangeName, "", message);`

### Direct交换机

基于路由key发送给对应的消息队列，**如果多个队列具有相同的RoutingKey，则与Fanout功能类似**

![image-20240329010933515](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290109559.png)

`rabbitTemplate.convertAndSend(exchangeName, **"red"**, message);`



### Topic交换机

和Direct相比都可以根据**路由key** 路由到不同队列，但是topic可以使用**通配符**。

![image-20240329011127322](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290111364.png)

通配符的规则：

- `#`：匹配一个或多个词
- `*`：仅匹配一个字符

举例：

- `item.#`：能够匹配`item.spu.insert`或者`item.spu`
- `item.*`：只能匹配`item.spu`

### 声明交换机和队列

通过程序自动生成或者通过注解生成

```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue1"),
    exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "blue"}
))
public void listenDirectQueue1(String msg){
    System.out.println("消费者1接收到direct.queue1的消息：【" + msg + "】");
}

@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue2"),
    exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "yellow"}
))
public void listenDirectQueue2(String msg){
    System.out.println("消费者2接收到direct.queue2的消息：【" + msg + "】");
}
```



# 进阶

## 发送者的可靠性

- 确保生产者一定把消息发送到MQ
- 确保MQ不会将消息弄丢
- 确保消费者一定要处理消息

### 生产者重试机制

生产者发送消息时，出现了网络故障，导致与MQ的连接中断；重试尝试连接；阻塞式不常用

### 生产者确认机制

消息发送到MQ之后丢失

- MQ内部处理消息的进程发生了异常
- 生产者发送消息到达MQ后未找到`Exchange`
- 生产者发送消息到达MQ后到达`Exchange`后，未找到合适的`Queue`，因此无法路由

![image-20240329014328365](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290143416.png)

在开启确认机制的情况下，当生产者发送消息给MQ后，MQ会根据消息处理的情况返回不同的**回执**。

ack 表示投递成功，nack表示投递失败；确认机制又分：

- `simple`：同步阻塞等待MQ的回执
- `correlated`：MQ异步回调返回回执



## MQ的可靠性

### 数据持久化

消息保存在内存中，持久化到数据库：

- 交换机持久化
- 队列持久化
- 消息持久化

为了减少IO次数，发送到MQ的消息并不是逐条持久化到数据库的，而是每隔一段时间批量持久化。

如果开启了生产者确认，会等持久化后再返回ACK



### LazyQueue

如果出现消息堆积，会将内存中的消息刷盘到磁盘，磁盘会造成堵塞；

因此直接将消息保存到磁盘中，读消息再从磁盘加载到内存，即懒加载。



## 消费者的可靠性

### 消费者确认机制

当消费者处理消息结束后，应该向RabbitMQ发送一个回执，告知RabbitMQ自己消息处理状态。回执有三种可选值：

- ack：成功处理消息，RabbitMQ从队列中删除该消息
- nack：消息处理失败，RabbitMQ需要再次投递消息
- reject：消息处理失败并拒绝该消息，RabbitMQ从队列中删除该消息(少用)

#### 消息回执处理

- `**none**`：不处理。即消息投递给消费者后立刻ack，消息会立刻从MQ删除。非常不安全，不建议使用
- `**manual**`：手动模式。需要自己在业务代码中调用api，发送`ack`或`reject`，存在业务入侵，但更灵活
- `**auto**`：自动模式。SpringAMQP利用AOP对我们的消息处理逻辑做了环绕增强，当业务正常执行时则自动返回ack. 当业务出现异常时，根据异常判断返回不同结果：
  - 如果是**业务异常**，会自动返回`nack`；
  - 如果是**消息处理或校验异常**，自动返回`reject`;

### 失败重试机制

消费者出现异常会导致消息不断 重入队，如果一直报错一直重入队，无限循环；设置阈值重试最大次数到了就reject丢弃；

### 失败处理策略

- `RejectAndDontRequeueRecoverer`：重试耗尽后，直接`reject`，丢弃消息。默认就是这种方式
- `ImmediateRequeueMessageRecoverer`：重试耗尽后，返回`nack`，消息重新入队
- `RepublishMessageRecoverer`：重试耗尽后，将失败消息投递到指定的交换机

比较优雅的一种处理方案是`RepublishMessageRecoverer`，失败后将消息投递到一个指定的，专门存放异常消息的队列，后续由人工集中处理。



### 业务幂等性-消息重复

指同一个业务，执行一次或多次对业务状态的影响是一致的，如查询数据；

但更新操作不一定是幂等的，重复执行可能会造成不一样的后果；

想办法保证消息处理的幂等性。这里给出两种方案：

- 唯一消息ID
- 业务状态判断

为了避免相同消息的重复处理，必须要采取一定的措施。RabbitMQ 服务端是没有这种控制的，因为它不知道你是不是就要把一条消息发送两次，所以只能在消费端控制。

回到前面生产者确认模式中讲到了一个 **全局唯一 ID** ，我们可以通过他来保证消息的幂等性，如下：

1. 消费者获取到消息后先根据这个全局 **唯一 ID** 去查询 redis/db 是否存在该消息；
2. 如果不存在，则正常消费，消费完毕后写入 redis/db；
3. 如果存在，则证明消息被消费过，直接丢弃，不做处理。



#### 唯一消息ID

1. 每一条消息都生成一个唯一的id，与消息一起投递给消费者。
2. 消费者接收到消息后处理自己的业务，业务处理成功后将消息ID**保存到数据库**
3. 如果下次又收到相同消息，去数据库查询判断是否存在，存在则为重复消息放弃处理。



### 消息确认机制

![img](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290214463.webp)

## 延迟消息

比如定时发布消息；超时未支付订单取消操作；在一段时间以后才执行的任务，我们称之为**延迟任务**。

实现延迟消息也有两种方案：

- 死信交换机+TTL
- 延迟消息插件



### 死信交换机+TTL

当一个队列中的消息满足下列情况之一时，可以成为**死信（dead letter）**：

消息处理失败重试达到次数、过期消息、队列满了

- 消费者使用`basic.reject`或 `basic.nack`声明消费失败，并且消息的`requeue`参数设置为false
- 消息是一个过期消息，超时无人消费
- 要投递的队列消息满了，无法投递

死信会被投递到死信交换机中绑定的队列；死信交换机会沿用之前的**路由key**

![image-20240329021802461](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403290218508.png)

生产者发送消息到ttl队列，五分钟到期了没人消费，会被放到死信交换机路由到真正需要消息的队列，实现了延迟消费，相当于设置了一个过渡ttl队列。但是时间不一定准确，万一队列消息很多，可能最终需要超过5分钟的时间才能被处理



## 如何保证消息可靠性

### 可靠性

- MQ持久化：将消息放到磁盘中保存
- 生产者确认机制：发送成功返回ack，没成功重试发送
- 消费者确认机制：由消息队列维护消息的状态，消费者成功消费消息会返回ack给队列，队列就会删除已被消费的消息；如果消费失败，会重试，重试达到次数会根据失败处理策略执行丢弃消息、重入队、加入死信交换机。

### 保证消息顺序

保证消费者只消费一个队列的消息

### 数据丢失

数据持久化、生产者确认机制

### 数据重复消费

消费者确认机制，去重处理

每条消息有唯一ID，在消费端消费消息之前会判断消息数据库中是否已经存在已消费ID；



### 如何保证高可用？

集群模式：多个节点组成集群，负载均衡

镜像队列：将消息复制到其他节点备份