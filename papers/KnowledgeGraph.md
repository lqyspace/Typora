# 知识图谱的存储与查询

## 基于关系型数据库的知识图谱存储

### 图上的查询语言：SPARQL

![image-20220802070604827](C:/Users/HP/AppData/Roaming/Typora/typora-user-images/image-20220802070604827.png)



[SPARQL 1.1 QUERY Language](https://www.w3.org/TR/sparql11-query/)





### 最简单的存储：Triple Store

![image-20220802071129282](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020711325.png)







### Property Tables：属性表存储

- 基本思想是以实体为中心，把同一个实体类型的属性组织为一个表，即属性表进行存储。
  - 优点：Join减少了，本质上接近于关系型数据库，可重用RDBMS功能
  - 缺点：很多空值，对Subject聚类比较复杂，不易处理多值属性。

![image-20220802071517343](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020715369.png)



### Binary Tables：二元表

- 二元表也称垂直划分表，也是关系型数据库实现的三元组存储方式
- 基本思想是对三元组按属性划分，为每个属性在关系型数据库中建立一个包含（subject，object）两列的表
- 由于一个知识图谱中属性数量有限，所以表的总体数量是可控的。
  - 优点：没有空值，也不需要聚类，对于subject-object-join操作性能好
  - 缺点：Insert性能高，并且subject-object join 性能差。

![image-20220802071919732](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020719763.png)



### Exhaustive index：全索引结构

- 性能最好的存储方式，典型的实现包括RDF-3X，Hexastore等。
- 仅维护一张包含（Subject，Predicate，Object）的三列表，但增加了多个方面的优化手段。
- 第一个优化手段是建立Mapping Table，即将所有的字符串首先映射到唯一的数字ID，这将大大的压缩存储空间。

![image-20220802072438245](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020724271.png)





## 基于原生图数据库的知识图谱存储

### 图数据库：Relations are first-class citizens

![image-20220802072854339](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020728379.png)



### 图查询语言：Cypher

![image-20220802073020210](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020730266.png)

### Cypher图查询举例

![image-20220802073133333](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020731368.png)



![image-20220802073158997](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020731031.png)



### 常见的图数据库列表

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020732328.png)



![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020733041.png)



## 原生图数据库实现原理浅析

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020734273.png)





![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020736204.png)

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020736611.png)



## 原生图数据库的物理存储实现

![image-20220802073748369](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020737398.png)



![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020737029.png)

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020738029.png)

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020738127.png)

![在这里插入图片描述](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020738388.png)

**RDF是什么：**

由于万维网上的信息没有统一的表示方式，这给数据管理带来了困难。如果网络中的资源在创建之初就使用标准的元数据来描述，就可以省去许多的麻烦。其中**RDF（资源描述框架）**可以用来描述和注解万维网中的资源并向计算机系统提供理解和交换数据的手段。**RDF是一种资源描述语言，它受到元数据标准、框架系统、面向对象语言等多方面的影响。RDF其本质是一个数据模型，提供了一个统一的标准，用于描述实体/资源**。简单来说，就是表示事物的一种方法和手段。**RDF由节点和边组成，节点表示实体/资源和属性，边则表示了实体和实体之间的关系以及实体和属性的关系**。于是，基于RDF数据模型，知识图谱由一条条知识组成，每条知识表示为一个SPO三元组。**三元组是 RDF 的核心概念，指的是两个事物和它们之间的关系**，在语法上呈现为“主语 + 谓语 + 宾语”。
数据库技术与人工智能技术相结合，出现了智能数据库、知识库和主动数据库系统。其中，知识图谱是结构化的语义知识库，用于迅速描述物理世界中的概念及其相互关系，通过将数据粒度从document级别降到data级别，聚合大量知识，从而实现知识的快速响应和推理。大部分知识图谱使用RDF描述世界上的各种资源，并以三元组的形式保存到知识库中。知识图谱的构建包括信息抽取、信息融合和信息加工三个步骤。信息抽取是从结构化数据库（关系数据库）、半结构化数据库（XML数据库）中从各种类型的数据源中提取出实体、属性以及实体之间的关系，在此基础上形成本体化的知识表达。信息融合即知识整理，用以消除矛盾和歧义。信息加工让系统能够实现推理和快速的有联系的查询。知识图谱相较于传统数据库具有高效率、可扩展、开放和半结构化的特点：

- 高效率：关系数据库查询时是通过表，对已存储的海量数据进行优化查询； 而知识图谱的查找模式为从三元组中查找需要的内容。对于多跳查找时，知识图谱的联系和推理性优于关系数据库的Join操作，所以查询效率会大大提升。
- 半结构化：关系数据库要求严格结构化，一个表通常为多表关系，保证数据的结构。而知识图谱使用三元组，模式灵活，支持半结构化。
- 扩展性：关系数据库扩展时，需在表上添加属性，还得更新相关索引。



## 总结

> 两种主要的存储方式：基于图数据库的查询与存储、基于关系型数据库的存储与查询

![image-20220802083007926](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020830973.png)



![image-20220802083117638](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020831676.png)

![image-20220802083217206](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020832247.png)



![image-20220802083236966](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020832006.png)



![image-20220802083255435](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020832475.png)



> 两种比较流行的查询语言：Gremlin和Cypher

1、Gremlin是过程式（procedural）语言；用户需指明具体的导航步骤，也就是在图上怎么走；它是业界标准查询语言，除了Neo4j外，几乎所有图数据库均支持。

2、Cypher是Neo4j专用语言，它是声明式（declarative）语言；用户只需声明“查什么”, 无需关心“怎么查”；

![image-20220802082612389](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208020826429.png)

