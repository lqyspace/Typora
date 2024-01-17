# Redis数据结构

## Redis的数据类型以及使用场景分别是什么？

Redis提供了丰富的数据类型，常见的数据类型有五种，分别是：String（字符串）、Hash（哈希）、List（链表）、Set（集合）、Zset（有序集合）。

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308030003518.png)

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308030003354.png)

随着Redis版本的更新，后面又支持四种数据类型：BitMap（2.2版新增）、HyperLogLog（2.8版新增）、GEO（3.2版新增）、Stream（5.0版新增）。

Redis物种数据类型的应用场景：

- String类型的应用场景：缓存对象、常规计数、分布式锁、共享session信息等
- List类型的应用场景：消息队列（但是有两个问题：1、生产者需要自行实现全局唯一ID；2、不能以消费组形式消费数据）等。
- Hash类型：缓存对象，购物车等。
- Set类型：聚合计算（并集，交集，差集）场景，比如点赞，共同关注，抽奖活动等。
- Zset类型：排序场景，比如排行榜，电话和姓名排序等

Redis后续版本又支持四种数据类型，他们的应用场景如下：

- BitMap（2.2新增）：二值状态统计的场景，比如签到，判断用户登录状况，连续签到用户总数等
- HyperLogLog（2.8版新增）：海量数据基数统计的场景，比如百万级网页UV计数等
- GEO（3.2版新增）：存储地理位置信息的场景，比如滴滴叫车；
- Stream（5.0版新增）：消息队列，相比于基于List类型实现的消息队列，有这两个特有的特性：自动生成全局唯一消息ID，支持以消息组形式消费数据。

> TIP
>
> 想深入了解这 9 种数据类型，可以看这篇：[2万字 + 20 张图 ｜ 细说 Redis 常见数据类型和应用场景](https://xiaolincoding.com/redis/data_struct/command.html)



## 五种常见的Redis数据类型是怎么实现的？

下图是一张redis数据类型和底层数据结构的对应关系图，左边是Redis3.0版本的，也就是《Redis设计与实现》这本书讲解的版本，现在看有点过时了，右边是现在Redis7.0版本的。

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308030016418.png)

> **String类型内部实现**

String类型的底层的数据结构实现主要是SDS（简单动态字符串）。SDS和我们认识的C字符串不太一样，之所以没有使用C语言的字符串表示，因为SDS相比于C的原生字符串：

- **SDS不仅可以保存文本数据，还可以保存二进制数据**，因为 SDS 使用 len 属性的值而不是空字符来判断字符串是否结束，并且 SDS 的所有 API 都会以处理二进制的方式来处理 SDS 存放在 buf[] 数组里的数据。所以 SDS 不光能存放文本数据，而且能保存图片、音频、视频、压缩文件这样的二进制数据。
- **SDS 获取字符串长度的时间复杂度是 O(1)**。因为 C 语言的字符串并不记录自身长度，所以获取长度的复杂度为 O(n)；而 SDS 结构里用 len 属性记录了字符串长度，所以复杂度为 O(1)。
- **Redis 的 SDS API 是安全的，拼接字符串不会造成缓冲区溢出**。因为 SDS 在拼接字符串之前会检查 SDS 空间是否满足要求，如果空间不够会自动扩容，所以不会导致缓冲区溢出的问题。

> **List类型内部实现**

List 类型的底层数据结构是由**双向链表或压缩列表**实现的：

- 如果列表的元素个数小于 512 个（默认值，可由 list-max-ziplist-entries 配置），列表每个元素的值都小于 64 字节（默认值，可由 list-max-ziplist-value 配置），Redis 会使用**压缩列表**作为 List 类型的底层数据结构；
- 如果列表的元素不满足上面的条件，Redis 会使用**双向链表**作为 List 类型的底层数据结构；

但是**在 Redis 3.2 版本之后，List 数据类型底层数据结构就只由 quicklist 实现了，替代了双向链表和压缩列表**。

> **Hash类型内部实现**

Hash 类型的底层数据结构是由**压缩列表或哈希表**实现的：

- 如果哈希类型元素个数小于 512 个（默认值，可由 hash-max-ziplist-entries 配置），所有值小于 64 字节（默认值，可由 hash-max-ziplist-value 配置）的话，Redis 会使用**压缩列表**作为 Hash 类型的底层数据结构；
- 如果哈希类型元素不满足上面条件，Redis 会使用**哈希表**作为 Hash 类型的底层数据结构。

**在 Redis 7.0 中，压缩列表数据结构已经废弃了，交由 listpack 数据结构来实现了**。



> TIP
>
> 想深入了解这 9 种数据结构，可以看这篇：[2万字 + 40 张图 ｜ 细说 Redis 数据结构](https://xiaolincoding.com/redis/data_struct/data_struct.html)
