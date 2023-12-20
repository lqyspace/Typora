#  MySql各种存储引擎的对比总结

存储引擎是 `mysql`数据库的核心，对于 `mysql`来说，存储引擎是以插件的形式运行的。

虽然 `mysql`支持的存储引擎种类繁多，但是常用的就那几种。



## 一，引言

在 `mysql5`以后，支持的存储引擎有十几个，但是常用的就那么几个，而且默认支持的也是 `InnoDB`，记下来从不同的维度进行对比。

使用命令 `show engines \g;`来查看当前的数据库支持哪些存储引擎。

![image-20230609171522524](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306091715574.png)

发现默认支持的方式有9种。

存储索引的选择：

| 功能         | MyISAM  | Memory | InnoDB  | Archive |
| :----------- | :------ | :----- | :------ | :------ |
| 存储限制     | 265TB   | RAM    | 65TB    | Node    |
| 支持事务     | No      | No     | ==Yes== | No      |
| 支持全文索引 | ==Yes== | No     | No      | No      |
| 支持数索引   | Yes     | Yes    | Yes     | No      |
| 支持哈希索引 | No      | Yes    | No      | No      |
| 支持数据缓存 | No      | N/A    | ==Yes== | No      |
| 支持外键     | No      | No     | ==Yes== | No      |

