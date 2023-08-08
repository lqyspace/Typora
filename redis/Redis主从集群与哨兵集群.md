# Redis主从集群与哨兵集群

## 一、Redis 哨兵集群原理

Redis 哨兵集群是一种高可用性的解决方案，用于监控 Redis 实例的状态并在实例出现故障时自动进行故障转移。

Redis 哨兵集群由多个哨兵实例组成，每个哨兵实例都运行在独立的服务器上。每个哨兵实例都会周期性地向 Redis 实例发送 PING 命令，以检查实例是否处于活动状态。如果哨兵实例无法与 Redis 实例通信，则哨兵实例会认为该实例已经出现故障，并开始执行故障转移操作。

故障转移操作包括将故障实例的故障状态通知其他哨兵实例，并选举出一个新的主节点来接管故障实例的职责。一旦新的主节点被选举出来，所有哨兵实例都会将新的主节点添加到自己的配置中，并停止向故障实例发送 PING 命令。

Redis 哨兵集群可以提供高可用性和数据冗余，因为每个 Redis 实例都会被多个哨兵实例监控。如果一个哨兵实例出现故障，其他哨兵实例仍然可以监控 Redis 实例的状态，并执行故障转移操作。此外，由于每个 Redis 实例都有多个备份，因此即使发生故障，数据也不会丢失。



## 二、搭建Redis主从集群

### 2.1 集群结构

我们搭建的主从集群结构如图：

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308021746693.png)

共包含三个节点，一个主节点，两个从节点。

这里我们会在同一台虚拟机中开启3个redis实例，模拟主从集群，信息如下：

|       IP        | PORT |  角色  |
| :-------------: | :--: | :----: |
| 192.168.159.132 | 7001 | master |
| 192.168.159.132 | 7002 | slave  |
| 192.168.159.132 | 7003 | slave  |



### 2.2 准备实例和配置

要在同一台虚拟机开启3个实例，必须准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。

（1）创建目录

我们创建三个文件夹，分子分别为7001,7002,7003：

```shell
# 进入/tmp目录
cd /tmp
# 创建目录
mkdir 7001 7002 7003
```

![image-20230802190514228](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308021905305.png)

（2）修改原始配置

修改redis-5.0.4/redis.conf配置文件，将其中的持久化模式改为默认的RDB模式，AOF保持关闭状态：

```shell
# 开启RDB
# save ""
save 3600 1
save 300 100
save 60 10000
# 关闭AOF
appendonly no
```

（3）拷贝配置文件到每个实例目录

然后将redis-5.0.4/redis.conf文件拷贝到三个目录中

```shell
# 方式一：逐个拷贝
cp redis-5.0.4/redis.conf 7001
cp redis-5.0.4/redis.conf 7002
cp redis-5.0.4/redis.conf 7003

# 方式二：管道组合命令，一键拷贝
echo 7001 7002 7003 | xargs -t -n 1 cp redis-5.0.4/redis.conf
```

（4）修改每个实例的端口号，工作目录

修改每个文件夹的配置文件，将端口号分别改成7001,7002,7003，将rdb文件保存位置修改为自己所在目录（在tmp目录下执行命令）

```shell
sed -i -e 's/6379/7001/g' -e 's/dir .\//dir \/home\/xiaoyun\/software\/tmp\/7001\//g' 7001/redis.conf
sed -i -e 's/6379/7002/g' -e 's/dir .\//dir \/home\/xiaoyun\/software\/tmp\/7002\//g' 7002/redis.conf
sed -i -e 's/6379/7003/g' -e 's/dir .\//dir \/home\/xiaoyun\/software\/tmp\/7003\//g' 7003/redis.conf
```

（5）修改每个实例的声明ip

虚拟机本身有多个ip，为了避免将来混乱，我们需要在redis.conf文件中制定每一个实例的绑定ip信息，格式如下：

```shell
# redis实例的声明 IP
replica-announce-ip 192.168.159.132
```

每个目录都要改，我们一键完成修改（在tmp目录下执行命令）

```shell
# 逐一执行
sed -i '1a replica-announce-ip 192.168.159.132' 7001/redis.conf
sed -i '1a replica-announce-ip 192.168.159.132' 7002/redis.conf
sed -i '1a replica-announce-ip 192.168.159.132' 7003/redis.conf

# 或者一键修改
printf '%s\n' 7001 7002 7003 | xargs -I{} -t sed -i '1a replica-announce-ip 192.168.159.132' {}/redis.conf
```



### 2.3 启动

为了方便查看日志，我们打开3个ssh窗口，分别启动3个redis实例，启动命令：

```shell
# 第1个
redis-server 7001/redis.conf
# 第2个
redis-server 7002/redis.conf
# 第3个
redis-server 7003/redis.conf
```

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022149740.png)

如果要一键停止，可以运行下面的指令：

```shell
printf '%s\n' 7001 7002 7003 | xargs -I{} -t redis-cli -p {} shutdown
```



### 2.4开启主从关系

现在三个实例还没有任何关系，要配置主从可以使用replicaof 或者slaveof（5.0以前）命令。

有临时和永久两种模式：

- 修改配置文件（永久生效）
  - 在redis.conf中添加一行配置：`slaveof <masterip> <masterport>`
- 使用redis-cli客户端连接到redis服务，执行slaveof命令（重启失效）

```shell
slaveof <masterip> <masterport>
```

==注意：==在5.0以后新增命令replicaof，与slaveof效果一致。

这里演示使用方式二

通过redis-cli命令连接7002，执行下面命令：

```shell
# 连接 7002
redis-cli -p 7002
# 执行slaveof
slaveof 192.168.159.132 7001
```

通过redis-cli客户端连接7003，执行下面的命令：

```shell
# 连接 7003
redis-cli -p 7003
# 执行slaveof
slaveof 192.168.159.132 7001
```

然后连接7001节点，查看集群状态：

```shell
# 连接 7001
redis-cli -p 7001
# 查看状态
info replication
```

结果：

![image-20230802221535790](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022215880.png)



Redis主从配置异常解决：Error condition on socket for SYNC: Connection refused

搭建的redis主从集群时，从服务器上的redis日志报错：

```shell
32677:S 08 Feb 16:14:38.947 * Connecting to MASTER 172.168.10.70:6379
32677:S 08 Feb 16:14:38.948 * MASTER <-> SLAVE sync started
32677:S 08 Feb 16:14:38.948 # Error condition on socket for SYNC: Connection refused
32677:S 08 Feb 16:14:39.950 * Connecting to MASTER 172.168.10.70:6379
32677:S 08 Feb 16:14:39.950 * MASTER <-> SLAVE sync started
32677:S 08 Feb 16:14:39.950 # Error condition on socket for SYNC: Connection refused
32677:S 08 Feb 16:14:40.952 * Connecting to MASTER 172.168.10.70:6379
32677:S 08 Feb 16:14:40.952 * MASTER <-> SLAVE sync started
32677:S 08 Feb 16:14:40.953 # Error condition on socket for SYNC: Connection refused
```

解决方案：

redis主服务器上的 redis.conf 中修改bind字段，将

```shell
bind 127.0.0.1
```

修改为：

```shell
bind 0.0.0.0
```

又或者直接注释掉bind字段

```shell
# bind 127.0.0.1
```

原因：

如果redis主服务器绑定了127.0.0.1，那么跨服务器IP的访问就会失败，从服务器用IP和端口访问主的时候，主服务器发现本机6379端口绑在了127.0.0.1上，也就是只能本机才能访问，外部请求会被过滤，这是linux的网络安全策略管理的。如果bind的IP地址是172.168.10.70，那么本机通过localhost和127.0.0.1、或者直接输入命令redis-cli登录本机redis也就会失败了。只能加上本机ip才能访问到。
所以，在研发、测试环境可以考虑bind 0.0.0.0，线上生产环境建议绑定IP地址。



### 2.5 测试

执行下面的命令进行测试：

```shell
# 利用redis-cli连接7001，执行 set num 123
# 利用redis-cli连接7002，执行 get num，再执行 set num 666
# 利用redis-cli连接7003，执行 get num，再执行 set num 888
```





## 三、搭建Redis哨兵集群

### 3.1 集群结构

这里我们搭建一个三节点形成的Sentinel集群，来监管之前的Redis主从集群。如图：

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022223787.png)

三个sentinel实例信息如下：

| 节点 |       IP        | PORT  |
| :--: | :-------------: | :---: |
|  s1  | 192.168.159.132 | 27001 |
|  s2  | 192.168.159.132 | 27002 |
|  s3  | 192.168.159.132 | 27003 |



### 3.2 准备实例和配置

要在同一台虚拟机开启3个实例，必须准备三份不同的配置文件和目录，配置文件所在目录也就是工作目录。

我们创建三个文件夹，名字分别叫s1、s2、s3：

```shell
# 进入/tmp目录
cd /tmp
# 创建目录
mkdir s1 s2 s3
```

如图：

![image-20230802222617268](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022226342.png)

然后我们在s1目录下创建一个sentinel.conf文件，添加下面的内容：

```shell
port 27001
sentinel announce-ip 192.168.159.132
sentinel monitor mymaster 192.168.159.132 7001 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
dir "/home/xiaoyun/software/tmp/s1"
```

解读：

- port 27001：是当前sentinel实例的端口
- sentinel monitor mymaster 192.168.150.101 7001 2：指定主节点信息
  - mymaster：主节点名称，自定义，任意写
  - 192.168.159.132 7001：主节点的ip和端口
  - 2：选举master时的quorum值

然后将s1/sentinel.conf文件拷贝到s2，s3两个目录中（在tmp目录下执行命令）

```shell
# 方式一：逐个拷贝
cp s1/sentinel.conf s2
cp s1/sentinel.conf s3
# 方式二：管道组合命令，一键拷贝
echo s2 s3 | xargs -t -n 1 cp s1/sentinel.conf
```

修改s2，s3两个文件夹内的配置文件，将端口分别改成27002, 27003：

```shell
sed -i -e 's/27001/27002/g' -e 's/s1/s2/g' s2/sentinel.conf
sed -i -e 's/27001/27003/g' -e 's/s1/s3/g' s3/sentinel.conf
```



### 3.3 启动

为了方便查看日志，我们打开3个ssh窗口，分别启动3个实例，启动命令如下：

```shell
# 第1个
redis-sentinel s1/sentinel.conf
# 第2个
redis-sentinel s2/sentinel.conf
# 第3个
redis-sentinel s3/sentinel.conf
```

启动后：

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022237256.png)



### 3.4 测试

尝试让master节点7001宕机，查看sentinel日志：

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022307117.png)

查看7003的日志：

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022308525.png)

查看7002的日志：

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202308022308418.png)



## 四、redis哨兵集群的优点

### 1 Redis哨兵集群的优点

- 哨兵集群基于主从复制模式，具有主从配置的优点，可以实现故障转移和系统的高可用性
- 哨兵模式是主从复制的升级，可以实现手动到自动的健壮性
- 架构简单，部署方便
- 高性价比，缓存使用时无需备用节点，实现高可用性也可以牺牲一个备用节点
- 高性能



### 2 Redis哨兵集群的缺点

- 在线扩容比较麻烦，集群容量一旦达到上限，在线扩容就十分麻烦
- 实现哨兵模式的配置比较麻烦，里面有很多选择
- 不保证数据的可靠性，在缓存使用时，进程重启后，数据丢失
- 主节点故障，集群无法进行工作，可用性比较低
- 哨兵集群在主从切换时存在访问瞬断的情况，等待时间比较长，至少十来秒不可用



参考的博客：

[Redis主从集群与哨兵集群](https://blog.csdn.net/lovoo/article/details/131076399)

