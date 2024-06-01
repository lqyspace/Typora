# 你真的了解Redis的持久化机制吗

## Redis持久化

Redis的持久化机制有两种，一种是快照（RDB），另一种是AOF日志。

RDB是一次全量备份，AOF日志是连续的增量备份。快照是内存数据的二进制序列化形式，在存储上非常紧凑。而AOF日志记录的是内存数据修改的指令记录文本。

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172206334.webp)

## RDB（Redis Database）

RDB快照持久化是Redis默认开启的，它会根据配置策略将内存数据保存在一个名为dmp.rdb的二进制文件中。

在redis.conf配置文件中，可以配置RDB的持久化策略，系统默认是有三个保存策略(三个同时生效)，如下：

- save 900 1       900秒内有1个键发生改变
- save 300 10      300秒内有10个键发生改变
- save 60 10000    60秒内有10000个键发生改变

我们也可以根据我们自己生产环境的具体情况进行配置。如果我们要关闭RDB快照，直接将配置文件中上述三个save注释掉即可。



### 写时复制机制(COW)

Redis提供了save命令进行快照生成，但是save命令要阻塞主进程(执行客户端命令的线程)，当需要生成快照的内存特别大时(几百个G)，需要的时间也会很长，甚至十几秒，所以这个时候如果阻塞主进程，会导致整个服务不可用，就得不偿失了。

针对这种情况，Redis借助操作系统提供的写时复制技术，提供了bgsave命令，basave命令可以在主进程的基础上，fork一个子进程，子进程会共享主进程的代码段和数据段，相当于是在后台生成快照。

在bgsave子进程写入RDB数据时，如果主进程对这些数据也都是读操作，那么，主进程和 bgsave 子进程相互不影响。但是，如果主进程要修改一块数据，这块数据就会被复制一份，生成该数据的副本。然后，bgsave 子进程会把这个副本数据写入RDB，而在这个过程中，主进程仍然可以直接修改原来的数据。



**触发机制——三种方式：**

save（同步）

bgsave（异步）

自动

**save命令：**客户端向redis发送save命令来创建一个快照文件。

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172215158.png)

执行save命令的时候，如果存在老的快照文件，新的将会替换老的文件。

**bgsave命令：**客户端向redis发送一个bgsave命令，redis调用fork创建一个子进程，然后子进程负责将快照写入磁盘，而父进程则继续处理命令请求。

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172218094.png)

**save命令和bgsave命令对比：**

| 命令           | save             | bgsave             |
| -------------- | ---------------- | ------------------ |
| IO类型         | 同步             | 异步               |
| 是否阻塞主进程 | 是               | 否                 |
| 复杂度         | $O(n)$           | $O(n)$             |
| 优点           | 不会消耗额外内存 | 不阻塞客户端命令   |
| 缺点           | 阻塞客户端命令   | 需要fork，消耗内存 |

**自动生成：**通过配置，满足任何一个条件就会创建快照文件。

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172221967.png)

bgsave会进行后台操作，不阻塞流程。 保存后会生成一个dump.rdb文件。



快照持久化选项：

```
多久执行一次自动快照操作，60s之内有1000次操作写入时执行
save 60 1000
创建快照失败后是否仍然继续执行写命令
stop-writes-on-bgsave-error no
是否对快照文件进行压缩
rdbcompression yes
命名硬盘上的快照文件
dbfilename dump.rdb
```

最佳配置： 

```
dbfilename dump-${port}.rdb
dir /bigdiskpath
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
```



## AOF（append only file）

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172236644.webp)

AOF持久化可以通过redis.conf文件中的appendonly参数控制是否开启。

- appendonly no 默认情况下是no， 将no改为yes即可开启AOF持久化方式，Redis会将修改的每一条指令先记录到系统缓存，默认每隔一秒钟就把缓存刷新到appendonly.aof磁盘文件中，当 Redis 重新启动时， 程序就可以通过重新执行 AOF 文件中的命令来达到重建数据集的目的。

打开appendonly.aof就能看到我们之前写入的键值对。

```java
*3
$3
SET
$4
key1
$4
val1
*3
$3
SET
$4
3124
$3
124
*3
$3
SET
$5
key14
$4
key2
```

这是一种resp协议格式的数据，星号后面的数字代表命令有多少个参数，$号后面的数字代表这个参数有几个字符。



### AOF持久化策略

- appendfsync always：每次有新命令追加到 AOF 文件时就执行一次 fsync ，非常慢，也非常安全，不建议用，本身Redis就是借助的内存快的优势，要是每次操作都访问磁盘这个优势就没了。
- appendfsync everysec：每秒 fsync 一次，足够快，并且在故障时只会丢失 1 秒钟的数据，AOF默认会走在这个配置。
- appendfsync no：从不 fsync ，将数据交给操作系统来处理。更快，也更不安全的选择，发生宕机时，丢失的数据最多。

![图片](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172258276.png)

### AOF重写

假设我们执行了两个命令 **set key1 1** 与 **set key1 2**，在开启AOF持久化的情况下，这两个命令都会被记录到AOF文件中，但是我们在宕机后重启恢复数据的时候是不是就只需要执行**set key1 2**就可以了，之前的**set key1 1**在恢复数据的时候忽略掉也没有任何影响，并且因为AOF文件随着时间的推移，体积会越来越大，重写AOF就显得尤为重要。

**手动触发后台重写，redis客户端执行命令：bgrewriteaof**

Redis提供了两个配置参数控制AOF自动重写频率(redis.conf)。

- auto‐aof‐rewrite‐min‐size 64mb //aof文件至少要达到64M才会自动重写，文件太小恢复速度本来就 很快
- auto‐aof‐rewrite‐percentage 100 //aof文件自上一次重写后文件大小增长了100%则再次触发重写

**触发重写时 Redis 会通过fork主进程**，生成一个子进程去做，这么做的原因是因为重写一般意味着要有大量的I/O操作，非常耗时，如果用主进程去重写会导致主进程长时间的阻塞，影响Redis执行客户端命令，甚至导致服务长时间不可用，**但是子进程去重写就意味着主进程仍然可以接收、处理客户端指令，就会导致重写后的AOF文件和服务器当前的数据库状态不一致。**

例如：子进程fork主进程时，内存中只有key1这个键，但是当开始重写后，客户端又多set了key2、key3,这就导致了重写的AOF文件和数据库不一致。

所以Redis在AOF重写时加了一个AOF重写缓冲区，**当有新命令执行的时候，Redis会将命令发送到AOF缓冲区和AOF重写缓冲区。**

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307172248697.webp)

执行完重写任务后，子进程会向主进程发送一个信号，主进程收到信号后会执行下面两个操作(==以下两个操作会阻塞主进程==)：

- 将 AOF 重写缓冲区中的所有内容写入到新的 AOF 文件中，保证新 AOF 文件保存的数据库状态和服务器当前状态一致。
- 对新的 AOF 文件进行改名，原子的覆盖现有 AOF 文件，完成新旧文件的替换

虽然只有上面两个操作会阻塞Redis主进程，但是很显然它已经将重写对性能的影响降到了最低。但是如果你想再对其进行优化，这里有两个解决方案。

1. 服务低峰期定时重写
2. 使用ssd，提升持久化效率

**注意：AOF在重写时，为了避免执行命令时造成的客户端缓冲区溢出，重写程序在处理列表、哈希表、集合、有序集合这四种可能会有多个元素的键时，会先检查键所包含的元素数量，如果元素的数量超过了redis.h/REDIS_AOF_REWRITe_ITeMS_PER_CMD常量的值(默认64个元素),那么重写程序将会使用多条命令来记录键的值，而不是单单只用一条命令。**

### 混合持久化(sence redis4.0)

Redis宕机恢复时，为了提高数据的完整程度，我们通常使用 AOF 日志进行恢复，但是使用 AOF 日志性能相对 RDB来说要慢很多，这样在 Redis 实例很大的情况下，启动需要花费很长的时间。 Redis4.0 为了解决这个问题，推出了**混合持久化**，说白了，**混合持久化就是结合了RDB与AOF两种持久化方式的优势。**

启混合模式后，AOF在重写时会将AOF文件中的数据以RDB文件的二进制格式写入当前AOF文件中，之后的新的写操作继续以AOF文件的格式进行追加。当redis宕机重启的时候，加载 AOF 文件进行恢复数据：先加载 RDB 的部分再加载剩余的 AOF内容，因此重启效率大幅得到提升。

通过修改redis.conf文件中的以下配置开启混合持久化(必须先开启AOF持久化)：

- aof‐use‐rdb‐preamble yes



### 总结

- RDB文件会保存Redis中所有的键值对数据
- save生成RDB文件会阻塞主进程
- bgsave命令后台生成RDB，不会阻塞主进程
- RDB文件体积小，保存的是二进制数据
- RDB通过配置策略执行，可能会丢失部分数据
- **RDB文件在Redis宕机后恢复快**

------

- AOF文件保存的是客户端执行的命令
- **AOF文件体积大，恢复慢，但是默认配置下数据比RDB要全很多**
- **Redis宕机重启后默认是用AOF方式进行恢复**
- AOF文件中的命令是以Resp协议格式保存的
- ==命令会先保存到缓冲区，再定期同步到AOF文件==
- AOF会根据配置策略自动的对AOF文件进行重写，以降低文件体积
- AOF重写时会fork一个子进程去执行，同时会有一个重写缓冲区，用来保存重写时主进程修改的键
- AOF重写时，最后会生成一个新的AOF文件，覆盖原有的文件
- 将重写缓冲区的内容写入到AOF与替换新旧AOF文件时会阻塞主进程

------

- 混合持久化是RDB与AOF的优势结合所产生的
- **混合持久化本质还是用的AOF文件**
- **混合持久化使用的前提是开启AOF持久化**
- **混合持久化在重写AOF文件时会将数据直接写成RDB的二进制格式，之后新的命令还是以AOF文件Resp协议格式进行保存**





## 附

### fsync

为了提高文件的写入效率，linux系统在做持久化时，会调用write函数，先将要写入文件的数据保存在一个内存缓冲区里就会直接返回，等到缓冲区被填满，或者超过了指定的时限(一般是30秒)后，才会将缓冲区的数据写入到磁盘。

这种做法虽然提高了写入效率，但是如果发生宕机，就有可能会导致缓冲区中还未写入磁盘的数据丢失。

**为此，linux提供了fsync函数，它会强制并阻塞等待系统将数据写入到磁盘，从而保证数据的安全性**



### 子进程

为什么Redis会fork一个子进程而不用子线程，是因为子进程可以携带主进程的数据副本，可以在不使用锁的情况下，保证数据安全。

**注：子进程在重写期间，主进程还是会接收并处理客户端命令，会导致子进程与主进程数据不一致。**


参考链接：https://juejin.cn/post/7114877074053005320
[https://mp.weixin.qq.com/s/gMvoCyoKGWWw1eGeEPRLyQ](https://mp.weixin.qq.com/s/gMvoCyoKGWWw1eGeEPRLyQ)







