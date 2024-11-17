[toc]

## 1、EhCache简介

[EhCache官网](https://www.ehcache.org/)

EhCache是一个Java实现的使用简单，高速，线程安全的缓存管理类库，EhCache提供了用缓存、磁盘文件存储以及分布式存储方式等多种灵活的cache管理方案，同时ehcache作为开源代码项目，采用限制比较宽松的Apache License V2.0作为授权方式，被广泛地用于Hibernate,Spring,Cocoon等其他开源项目。Ehcache从Hibernate发展而来，逐渐涵盖了Cache界的全部功能，是目前发展势头最好的一个项目。具有快速、简单、低消耗，依赖性小，扩展性强，支持对象或序列化缓存，支持缓存或元素的失效，提供LRU，LFU和FIFO缓存策略，支持内存缓存和磁盘缓存，分布式缓存机制等。



> **缓存特征**

- 命中率

  命中率=返回正确结果数/请求缓存次数，命中率问题是缓存中的一个非常重要的问题，它是衡量缓存有效性的一个重要指标。命中率越高，表明缓存的使用率越高。

- 最大元素（或最大空间）

  缓存中可以存放的最大元素的数量，一旦缓存中元素的数量超过这个值（或者缓存数据所占空间超过其最大支持空间），那么将会触发缓存的清空策略。根据不同的应用场景设置合理的最大元素值往往可以一定程度上提高缓存的命中率，从而更有效地使用缓存。

- 清空策略

  设计适合自身数据特征的清空策略能有效提升命中率，场景的一些策略有：

  1. FIFO（first in first out）

     先进先出策略，最先进入缓存的数据在缓存空间不够的情况下（超出最大元素限制）会被优先清空掉，以腾出新的空间接收新的数据，策略算法主要比较缓存元素的创建时间。

  2. LFU（least frequently used）

     最少使用策略，无论是否过期，根据元素的使用次数判断，清除使用次数较少的元素以释放空间。策略算法主要是比较元素的命中次数（hitCount）.

  3. LRU（least recently used）

     最近最少使用策略，无论是否过期，根据元素最后一次使用的时间戳，清除最远使用时间戳的元素以释放空间，比较元素最近一次被get使用时间。

     除此之外，还有一些比较简单的内存淘汰策略：1）根据过期时间判断，清理过期时间最长的元素；2）根据过期时间判断，清理最近要过期的元素；3）随机清理；4）根据关键字（或元素内容）长短清理等。

> **缓存介质**

（从硬件介质上来看，无非就是内存和硬盘两种）从技术上划分，可以分为几种，内存，硬盘文件，数据库。

- 内存：将缓存存储与内存中是最快的选择，无需额外的I/O开销，但是内存的缺点就是没有持久化落地磁盘，一旦应用异常break down，重新启动数据很难或者是无法恢复。
- 硬盘：一般来说，很多缓存框架会结合使用内存和硬盘，在内存空间分配满了或者是在异常的情况下，可以被动或者是主动将内存空间数据持久化到硬盘中，达到释放空间或者备份数据的目的。
- 数据库：增加缓存的目的就是为了减少数据库的I/O压力，而现在使用数据库并不是传统的关系型数据库，像那些不支持SQL，只是简单的key、value结构的存储结构的特殊数据库(如berkleydb)，响应速度和吞吐量都远远高于我们常用的关系型数据库。

在目前的应用服务框架中，对于缓存的划分跟常用的是根据缓存与应用的耦合程度，划分为local cache（本地缓存）和remote cache（分布式缓存）：

- Local Cache：指的是应用中的缓存组件，其最大的优点是**应用和缓存在同一进程内部，请求缓存非常快速**，没有过多的网络开销等，在但应用不需要集群支持或者集群情况下各节点无需相互通知的场景下使用本地缓存比较合适。同时他的缺点也是因为**缓存和应用程序耦合**，**多个应用程序无法直接共享缓存**，各应用或者集群各节点都需要维护自己的单独缓存，对内存也是一种浪费。
- Remote Cache：指的是应用分离的缓存组件或服务，其最大的有点就是自身就是一个独立的应用，与本地应用隔离，多个应用可直接共享缓存。

目前没有一种方案适用于所有的业务场景和数据类型，我们需要根据自身的特殊场景和背景，选择最合适的缓存方案。缓存的使用是程序员，架构师的必备技能。好的程序员能够根据数据类型，业务场景来准确判断使用何种类型的缓存，如何使用这种缓存，以最小的成本最快的速度达到最优的目的。

> **主要特性**

- 快速，针对大型高并发系统场景，ehcache的多线程机制有相应的优化改善策略
- 简单很小的ar包，简单配置就可直接使用，单机场景下无需过多的其他服务依赖
- 支持多种的缓存策略，灵活
- 缓存数据有两级：内存和磁盘，与一般的本地内存缓存相比，有了磁盘的存储空间，将可以支持更大量的数据缓存需求。
- 具有缓存和缓存管理器的侦听接口，能更简单方便的进行缓存实例的监控管理
- 支持多缓存管理器实例，以及一个实例的多个缓存区域。

ehcache直接在jvm虚拟机中缓存，速度快，效率高；但是缓存共享麻烦，集群分布式应用不方便。

redis是通过socket访问到缓存服务器，效率比ehcache低，比数据库要快很多，处理集群和分布式缓存方便，有成熟的方案。如果是单个应用，或者是对缓存访问有更高的要求，使用ehcache。如果是大型系统，存在缓存共享，分布式部署，缓存内容很大的，建议用redis。

ehcache也有缓存共享方案，不过是通过RMI或者Jgroup多播方式进行广播缓存通知更新，缓存共享复杂，维护不方便；简单的缓存可以，但是涉及到缓存恢复，大数据缓存，则不合适。



## 2、EhCache 2.x使用

### 2.1、引入依赖

```xml
<!--EhCache-->
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>2.6.11</version>
</dependency>
```



### 2.2、创建ehcache.xml文件

在src/main/resources/创建一个配置文件ehcache.xml

> [!note]
>
> 默认情况下，Ehcache会自动加载classpath根目录下名为ehcache.xml文件，也可以将该文件放到其他地方，只不过在使用时指定文件的位置。
>
> 下面为ehcache配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd" updateCheck="false">
    <diskStore path="java.io.tmpdir" />

    <defaultCache eternal="false" maxElementsInMemory="1000"
        overflowToDisk="false" diskPersistent="false" timeToIdleSeconds="0"
        timeToLiveSeconds="600" memoryStoreEvictionPolicy="LRU" />

    <cache name="FunctionCache" eternal="false"
        maxElementsInMemory="500" overflowToDisk="false" diskPersistent="false"
        timeToIdleSeconds="3600" timeToLiveSeconds="3600"
        memoryStoreEvictionPolicy="LRU">
    </cache>

    <cache name="RoleFunctionCache" eternal="false"
        maxElementsInMemory="500" overflowToDisk="false" diskPersistent="false"
        timeToIdleSeconds="3600" timeToLiveSeconds="3600"
        memoryStoreEvictionPolicy="LRU">
    </cache>

    <cache name="ModelDefCache" eternal="false"
        maxElementsInMemory="500" overflowToDisk="false" diskPersistent="false"
        timeToIdleSeconds="3600" timeToLiveSeconds="3600"
        memoryStoreEvictionPolicy="LRU">
    </cache>

    <cache name="SysNoConfigCache" eternal="false"
        maxElementsInMemory="500" overflowToDisk="false" diskPersistent="false"
        timeToIdleSeconds="3600" timeToLiveSeconds="3600"
        memoryStoreEvictionPolicy="LRU">
    </cache>

    <cache name="MesProdTimeCache" eternal="false"
        maxElementsInMemory="500" overflowToDisk="false" diskPersistent="false"
        timeToIdleSeconds="3600" timeToLiveSeconds="3600"
        memoryStoreEvictionPolicy="LRU">
    </cache>

</ehcache> 
```

> [!important]
>
> 接下来为配置文件的详解：
>
> ```xml
> <?xml version="1.0" encoding="UTF-8" ?>
> <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
>          xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
>          name="ehcache_tmpdir">
> 
>     <!--指定一个文件目录，当EhCache把数据写到硬盘时，将把数据写到这个文件目录下-->
>     <diskStore path="java.io.tmpdir/ehcache" />
> 
>     <!--默认缓存-->
>     <!--设定缓存的默认数据过期策略-->
>     <defaultCache
>         maxElementsInMemory="10000"
>         eternal="false"
>         timeToIdleSeconds="120"
>         timeToLiveSeconds="120"
>         maxElementsOnDisk="10000000"
>         diskExpiryThreadIntervalSeconds="120"
>         memoryStoreEvictionPolicy="LRU">
>     </defaultCache>
> 
>     <cache name="RoleFunctionCache"
>         eternal="false"
>         maxElementsInMemory="1000"
>         overflowToDisk="false"
>         diskPersistent="false"
>         timeToIdleSeconds="3600"
>         timeToLiveSeconds="3600"
>         memoryStoreEvictionPolicy="LRU">
>     </cache>
> 
>     <!--
>         设定具体的命名缓存的数据过期策略
>         cache元素的属性：
>             name: 缓存名称
>             maxElementsInMemory: 内存中最大缓存对象数
>             maxElementsOnDisk: 硬盘中最大缓存对象数，若是0表示无穷大
>             eternal: true表示对象永不过期，此时会忽略timeToIdleSeconds和timeToLiveSeconds属性，默认为false
>             overflowToDisk: true表示当内存缓存的对象数目达到了maxElementsInMemory界限后，会把溢出的对象写到硬盘缓存中，注意：如果缓存的
>         的对象要写入到硬盘中的话，则该对象必须实现了Serializable接口才行；为false则根据memoryStoreEvictionPolicy策略替换Cache中的原有元素。
>         overflowToDisk会根据标签中path值查找对应的属性值，写入磁盘的文件会放在path文件夹下，文件的名称是cache的名称，后缀名是data。
>             diskSpoolBufferSizeMB：磁盘缓存区大小，默认为30MB。每个Cache都应该有自己的一个缓冲区。
>             diskPersistent: 是否持久化磁盘缓存，当这个属性的值为true时，系统在初始化时会在磁盘中查找文件名为cache名称，后缀名为index的文件，这个文件中
>         存放了已经持久化到磁盘中的cache的index，找到后会把cache加载到内存，要想把cache真正持久化到磁盘，写程序时注意执行 net.sf.ehcache.Cache.put(Element element)
>         后调用flush()方法。
>             diskExpiryThreadIntervalSeconds: 磁盘失效或清理线程 运行时间间隔，默认为120s
>             timeToIdleSeconds: 设定允许对象处于空闲状态的最长时间，即缓存数据在失效前的允许闲置时间（单位：秒），仅当eternal=false时使用，默认值是0，
>         表示可闲置时间无穷大，此为可选属性，即访问这个cache中元素的最大时间间隔，若超过这个时间没有访问此Cache中的某个元素，那么此元素将会被从Cache中清除。
>             timeToLiveSeconds: 缓存数据在失效前的允许存活时间（单位：秒），仅当eternal=false时使用，默认值是0，表示可存活时间无穷大。即Cache中某元素从创建
>         到清除的生存时间，也就是说从创建开始计时，当超过这个时间就会把该元素清除。
>             memoryStoreEvictionPolicy: 当达到maxElementsInMemory限制时，EhCache将会根据指定的策略去清理内存，可选策略有：LRU（最近最少使用，默认），LFU，FIFO
>     -->
> 
> </ehcache>
> ```



### 2.3、测试用例

```java
public class EhCacheTest {
    public static void main(String[] args) {
        // 1、创建缓存管理器,默认加载resources目录下的ehcache.xml
        CacheManager cacheManager = CacheManager.create();
        // 获取缓存对象
        Cache cache = cacheManager.getCache("RoleFunctionCache");
        // 3、创建元素
        Element element = new Element("test", 1);
        // 4、将元素添加到缓存
        cache.put(element);
        // 5、获取缓存
        Element element2 = cache.get("test");
        System.out.println("value: " + element2);
        System.out.println(element2.getObjectValue());

        // 6、删除元素
        cache.remove("test");

        Map<String, String> map = new HashMap<String, String>();
        map.put("a", "123");
        Element mapElement = new Element("map", map);
        cache.put(mapElement);
        cache.put(new Element("test", 2));
        System.out.println("cache.size: " + cache.getSize());
        System.out.println("cache: " + cache);

        Element mapElement2 = cache.get("map");
        System.out.println("mapElement2: " + mapElement2);
        Map map2 = (Map) mapElement2.getObjectValue();
        System.out.println("map2: " + map2);

        // 7、关闭
        cacheManager.shutdown();

    }
}
```

输出：

```java
value: [ key = test, value=1, version=1, hitCount=1, CreationTime = 1731826341064, LastAccessTime = 1731826341065 ]
1
cache.size: 2
cache: [ name = RoleFunctionCache status = STATUS_ALIVE eternal = false overflowToDisk = false maxEntriesLocalHeap = 1000 maxEntriesLocalDisk = 0 memoryStoreEvictionPolicy = LRU timeToLiveSeconds = 3600 timeToIdleSeconds = 3600 persistence = none diskExpiryThreadIntervalSeconds = 120 cacheEventListeners: ; orderedCacheEventListeners:  maxBytesLocalHeap = 0 overflowToOffHeap = false maxBytesLocalOffHeap = 0 maxBytesLocalDisk = 0 pinned = false ]
mapElement2: [ key = map, value={a=123}, version=1, hitCount=1, CreationTime = 1731826341065, LastAccessTime = 1731826341065 ]
map2: {a=123}
```



### 2.4、EhCache API

- CacheManager：Cache的容器对象，并管理着（添加或删除）Cache的生命周期。

  ```java
  // 可以自己创建一个Cache对象并添加到CacheManager中
  public void addCache(Cache cache);
  public synchronized void removeCache(String cacheName);
  ```

- Cache：一个Cache可以包含多个Element，并被CacheManager管理。它实现了对缓存的逻辑行为

- Element：需要缓存的元素，它维护着一个键值对，元素也可以设置有效期，0代表无限制。

获取CacheManager的方式：

- 可以通过create()或者newInstance()方法或重载方法来创建获取CacheManager的方式：

  ```java
  public static CacheManager create();
  public static CacheManager create(String configurationFileName);
  public static CacheManager create(InputStream inputStream);
  public static CacheManager create(URL configurationFileURL);
  
  public static CacheManager newInstance();
  ```

EhCache的CacheManager构造函数或工厂方法被调用时，会默认加载classpath下名为ehcache.xml的配置文件。

如果加载失败，会默认加载Ehcache.jar包中的ehcache-failsafe.xml文件，这个文件含有简单的默认配置。

```java
// CacheManager.create == CacheManager.create("./src/main/resources/ehcache.xml")
// 使用Ehcache默认配置新建一个CacheManager实例
CacheManager cacheManager = CacheManager.create();
cacheManager = CacheManager.newInstance();

cacheManager = CacheManager.newInstance("./src/main/resources/ehcache.xml");

InputStream in = new FileInputStream(new File("./src/main/resources/ehcache.xml"));
cacheManager = CacheManager.newInstance(in);

InputStream in = 当前类.class.getClassLoader().getResourceAsStream("ehcache.xml");
CacheManager cacheManager = new CacheManager(in);

String[] cacheNames = cacheManager.getCacheNames(); // [HelloWorldCache]
```

整体上看，ehcahce的使用还是相对比较简单的，提供了完整的各类API接口。需要注意的是，虽然ehcahce支持磁盘的持久化，但是由于存在两级缓存介质，在一级内存中的缓存，如果没有主动的刷入磁盘持久化的话，在应用异常down机等情况下，依然会有缓存数据丢失的风险，为此需要根据需要将缓存刷到磁盘，将缓存条目刷到磁盘的操作可以通过cache.flush()方法来执行，需要注意的是，对于对象的磁盘写入，前提是要将对象进行序列化。



## 3、EhCache 3.x使用（了解）

### 3.1、引入依赖

```xml
<dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>3.9.6</version>
</dependency>
```

### 3.2、项目实例

```java
// 使用CacheManagerBuilder来创建一个预配置（pre-configured）缓存
// 第一个参数是一个别名，用于Cache和CahceManager进行配合。
// 第二个参数是org.ehcache.config.CacheConfiguration主要用来配置Cache
// 我们使用org.ehcache.config.builders.CacheConfigurationBuilder的静态方法newCacheConfigurationBuilder来创建
CacheManager cacheManager = CacheManagerBuilder.newCacheManagerBuilder() 
    .withCache("preConfigured",
        CacheConfigurationBuilder.newCacheConfigurationBuilder(Long.class, String.class, ResourcePoolsBuilder.heap(10))) 
    .build(); // 在你开始使用CacheManager的时候，需要使用init()方法进行初始化
cacheManager.init(); 

// 通过将其别名，键类型，值类型传递给CacheManager来检索缓存cache。例如，要获取步骤二中声明的缓存，我们需要alias='preConfigured'、keyType=Long.class、valueType=String.class。出于类型安全，我们要求传入键和值类型。如果这些类型与我们期望的不同，CacheManager会在应用程序生命周期的早期抛出异常ClassCastException。这样可以防止缓存被随机类型污染。
Cache<Long, String> preConfigured =
    cacheManager.getCache("preConfigured", Long.class, String.class); 
preConfigured.put(2L, "wowowo");
System.out.println("preConfigured = "+preConfigured);

// 通过CacheManager创建出新的Cache
Cache<Long, String> myCache = cacheManager.createCache("myCache", 
    CacheConfigurationBuilder.newCacheConfigurationBuilder(Long.class, String.class, ResourcePoolsBuilder.heap(10)));

// 使用put方法存储数据
myCache.put(1L, "da one!"); 
// 使用get方法获取数据
String value = myCache.get(1L); 
System.out.println("value = "+value);

cacheManager.removeCache("preConfigured"); 

cacheManager.close();
```

[参考官方说明文档](https://www.ehcache.org/documentation/3.10/getting-started.html)



## 4、Spring整合EhCache

### 4.1、添加依赖

```xml
<!--EhCache-->
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>2.10.3</version>
</dependency>
```

### 4.2、添加配置文件

> 在resources文件夹中添加ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         name="ehcache_tmpdir">

    <!--指定一个文件目录，当EhCache把数据写到硬盘时，将把数据写到这个文件目录下-->
    <diskStore path="java.io.tmpdir/ehcache" />

    <!--默认缓存-->
    <!--设定缓存的默认数据过期策略-->
    <defaultCache
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="120"
        timeToLiveSeconds="120"
        maxElementsOnDisk="10000000"
        diskExpiryThreadIntervalSeconds="120"
        memoryStoreEvictionPolicy="LRU">
    </defaultCache>

    <cache name="RoleFunctionCache"
        eternal="false"
        maxElementsInMemory="1000"
        overflowToDisk="false"
        diskPersistent="false"
        timeToIdleSeconds="3600"
        timeToLiveSeconds="3600"
        memoryStoreEvictionPolicy="LRU">
    </cache>

    <!--
        设定具体的命名缓存的数据过期策略
        cache元素的属性：
            name: 缓存名称
            maxElementsInMemory: 内存中最大缓存对象数
            maxElementsOnDisk: 硬盘中最大缓存对象数，若是0表示无穷大
            eternal: true表示对象永不过期，此时会忽略timeToIdleSeconds和timeToLiveSeconds属性，默认为false
            overflowToDisk: true表示当内存缓存的对象数目达到了maxElementsInMemory界限后，会把溢出的对象写到硬盘缓存中，注意：如果缓存的
        的对象要写入到硬盘中的话，则该对象必须实现了Serializable接口才行；为false则根据memoryStoreEvictionPolicy策略替换Cache中的原有元素。
        overflowToDisk会根据标签中path值查找对应的属性值，写入磁盘的文件会放在path文件夹下，文件的名称是cache的名称，后缀名是data。
            diskSpoolBufferSizeMB：磁盘缓存区大小，默认为30MB。每个Cache都应该有自己的一个缓冲区。
            diskPersistent: 是否持久化磁盘缓存，当这个属性的值为true时，系统在初始化时会在磁盘中查找文件名为cache名称，后缀名为index的文件，这个文件中
        存放了已经持久化到磁盘中的cache的index，找到后会把cache加载到内存，要想把cache真正持久化到磁盘，写程序时注意执行 net.sf.ehcache.Cache.put(Element element)
        后调用flush()方法。
            diskExpiryThreadIntervalSeconds: 磁盘失效或清理线程 运行时间间隔，默认为120s
            timeToIdleSeconds: 设定允许对象处于空闲状态的最长时间，即缓存数据在失效前的允许闲置时间（单位：秒），仅当eternal=false时使用，默认值是0，
        表示可闲置时间无穷大，此为可选属性，即访问这个cache中元素的最大时间间隔，若超过这个时间没有访问此Cache中的某个元素，那么此元素将会被从Cache中清除。
            timeToLiveSeconds: 缓存数据在失效前的允许存活时间（单位：秒），仅当eternal=false时使用，默认值是0，表示可存活时间无穷大。即Cache中某元素从创建
        到清除的生存时间，也就是说从创建开始计时，当超过这个时间就会把该元素清除。
            memoryStoreEvictionPolicy: 当达到maxElementsInMemory限制时，EhCache将会根据指定的策略去清理内存，可选策略有：LRU（最近最少使用，默认），LFU，FIFO
    -->

</ehcache>
```

> 添加spring配置文件applicationContext.xml、applicationContext-ehcache.xml

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.lqy.*"/>
</beans>
```

