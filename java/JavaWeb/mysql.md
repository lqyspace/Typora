# 数据库

## 概念

1. 数据库英文简称：DataBase，DB

2. 数据库：存储和管理数据的仓库

3. 特点：

   1. 持久化存储数据，其实数据库就是一个文件管理系统
   2.  方便存储和管理数据
   3. 使用统一的方式操作数据库——SQL

4. 常见的数据库软件：

   1. Oracle：收费、大型、常用
   2. MySql：开源免费，小型、常用
   3. Microsoft SQL Server：收费，中型
   4. PostgreSQL
   5. MongoDB
   6. DB2：IBM公司的数据库产品，收费，常应用在银行系统中
   7. Cassandra
   8. Microsoft Access
   9. Redis
   10. SQLite：嵌入式小型数据库，应用在手机端

5. mysql的命令行启动方式

   ```mysql
   // 手动
   cmd——>services.msc
   // 命令行
   net stop mysql
   net start mysql
   
   // 登录
   mysql -uroot -padmin
   mysql -h127.0.0.1 -uroot -padmin
   mysql --host=ip --user=root --password=admin
   ```





## SQL

1. Structured Query Language 结构化查询语言

2. Sql作用：

   1. 是一种所有关系型数据库的查询规范，不同的数据库都支持
   2. 通用的数据库操作语言，可以用在不同的数据库中
   3. 不同的数据库SQL语句有一些区别

3. SQL分类：

   1. Data Definition Language（DDL数据定义语言），如：建库，建表
   2. Data Manipulation Language（DML数据操作语言），如：对表中的数据的增删改
   3. Data Query Language（DQL数据查询语言），如：对数据的查询
   4. Data Control Language（DCL 数据控制语言），如：用户权限的设置

4. MySQL的语法：

   1. 每条语句以分号结尾
   2. SQL中不区分大小写，关键字中认为大写和小写是一样的
   3. 注释：`/* */`多行注释，`//`单行注释

5. DDL操作数据库

   1. 创建数据库

      ```mysql
      // 创建数据库
      create database 数据库名;
      
      // 判断数据库是否已经存在，如果不存在则创建数据库
      create database if not exists 数据库名;
      
      // 创建数据库并指定字符集
      create database if not exists 数据库名 character set 字符集;
      
      // 按tab键可以自动补全关键字
      ```

   2. 查询数据库

      ```mysql
      // 查询数据库
      show databases;
      // 查看某个数据库的定义信息，即创建语句
      show create database db3;
      ```

   3. 修改数据库

      ```mysql
      // 修改数据库默认的字符集
      alter database 数据库名 default character set 字符集;
      
       // 修改数据库字符集
       alter database 数据库名 character set 字符集;
      ```

   4. 删除数据库

      ```mysql
      // 删除数据库
      drop database 数据库名;
      drop database if exists 数据库名;
      ```

   5. 使用数据库

      ```mysql
      // 查看正在使用的数据库
      select database();
      
      // 使用/切换数据库
      use 数据库名;
      ```

6. DDL操作表结构

   1. 创建表

      ```mysql
      // 创建表格式
      create table if not exists 表名(
      	字段名1 字段类型1,
      	字段名2 字段类型2
      );
      ```

   2. MySql数据类型

      1. 常见的数据库类型：

         ![image-20230630175556178](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306301755199.png)

      2. 详细的数据类型如下：

         ![image-20230630175730382](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202306301757462.png)

      3. 查看表

         ```mysql
         // 查看某个数据库中的所有表
         show tables;
         
         // 查看表结构
         desc 表名;
         
         // 查看创建表的SQL语句
         show create table 表名;
         ```

      4. 快速创建一个表结构相同的表

         ```mysql
         // 快速创建一个表结构相同的表
         create table 新表名 like 旧表名;
         ```

      5. 删除表

         ```MySQL
         // 删除表
         drop table 表名;
         
         // 判断表是否存在，如果存在则删除表
         drop table if exists 表名;
         ```

         与直接删除表的区别：如果表不存在，则不删除表，否则删除表。

      6. 修改表结构

         ```mysql
         // 添加表列add
         alter table 表名 add 列名 类型;
         
         // 修改列类型modify
         alter table 表名 modify 列名 新的类型;
         
         // 修改列名change
         alter table 表名 change 旧列名 新列名 类型;
         
         // 删除列drop
         alter table 表名 drop 列名;
         
         // 修改表名
         rename table 表名 to 新表名;
         
         // 修改字符集
         alter table 表名 character set 字符集;
         ```

7. DML操作表中的数据

   1. 插入

      ```MySQL
      // 插入全部字段
      insert into 表名 (字段名1， 字段名2，字段名3...) values (值1，值2，值3...);
      insert into 表名 values (值1，值2，值3...);
      
      // 插入部分数据
      insert into 表名 (字段名1， 字段名2...) values (值1，值2...);
      
      // 没有添加数据的字段会使用NULL
      ```

   2. 更新

      ```mysql
      // 不带条件
      update 表名 set 字段名=值, 字段名=值...;  -- 修改所有的行
      
      // 带条件
      update 表名 set 字段名=值, 字段名=值... where 字段名=值; -- 更新多个字段
      ```

   3. 删除

      ```mysql
      // 删除表记录
      delete from 表名 where 条件表达式;
      delete from 表名;
      delete from 表名 where 字段名=值;
      
      // 使用truncate删除表中所有的记录；先删除表，再创建一模一样的表
      truncate [table] 表名;
      
      truncate：删除表中的内容，不删除表结构，释放空间；不能删除一个带有外键的表，如果要删除首先要取消外键，然后再删除。
      delete：删除内容，不删除表结构，但不释放空间
      ```

      truncate 与 delete 的区别：

      1）条件删除：delete支持条件删除，可以带where，而truncate只能删除整个表

      2）事务回滚：delete是数据库操作语言（DML），操作时原数据会被放到rollback segment中，可以被回滚，所以可以进行回滚和提交操作；而truncate是数据库定义语言（DDL），操作时自动进行commit，不能进行回滚。

      3）清理速度：在数据库量比较小的情况，delete和truncate的清理速度差不多大。但是数据量很大的时候就不一样了，由于truncate是不支持回滚的，所以使用的系统和事务日志资源少。delete语句每删除一行，并在事务日志中为所删除的每行记录一项，固然会慢，但是相对来说比较安全。

      4）高水位重置：随着不断的进行表记录的DML操作，会不断提高表的高水位线（HWM），delete操作之后虽然表的数据删除了，但是并没有降低表的高水位（数据会从删除前的最后一行续写），随着DML操作数据库容量也只上升，不会下降。所以如果使用delete删除数据，就算删除很多，在查询时还是和delete操作前速度一样；而truncate操作会重置高水位线（数据重新从1开始），数据库容量也会被重置，之后再进行DML操作也会有所提升。

      **小结：**

      ​	truncate table 则一次性地从表中删除所有的数据页并不把单独的删除操作记录记入日志中保存，删除行是不能恢复的。并且在删除的过程中不会激活与表有关的删除触发器。执行速度快。

      ​	delete table语句可以通过where对要删除的记录进行选择。而使用truncate table将删除表中的所有记录。因此delete语句更加灵活。

      ​	drop和truncate都是DDL，不能够激活触发器，因为该操作不记录各行删除；drop删除表，内容连带结构一起删除，表不复存在。

      ​	delete命令不能自动提交事务，操作会触发trigger；而truncate，drop命令，执行后会自动提交事务，操作不触发trigger。

      ​	速度：一般来说，drop>truncate>delete

8. DQL查询数据表

   语法：

   ```mysql
   select 字段列表 from 表名列表 where 条件列表 group by 分组字段 having 分组之后的条件 order by 排序 limit 分页限定;
   ```

   1. 简单查询

      ```mysql
      select * from 表名;
      
      select 字段名1, 字段名2... from 表名;
      
      // 指定列的别名进行查询
      select 字段名1 as 别名1, 字段名2 as 别名2... from 表名;
      
      // 对列和表同时使用别名
      select 字段名1 as 别名1, 字段名2 as 别名2... from 表名 as 别名;
      
      // 查询指定列并且结果不出现重复的数据
      select distinct 字段名 from 表名;// 去掉重复的记录
      ```

      注：起别名时，`as`可以省略

   2. 查询结果参与运算

      ```mysql
      // 某列数据和固定值运算
      select 列名1+固定值 from 表名;
      eg: select math+5 from student;
      
      // 某列数据和其他列数据参与运算
      select 列名1+列名2 from 表名;
      eg: select *, (math + english) as 总成绩 from student;
      // as 可以省略
      select *, (math + english) 总成绩 from student;
      
      //ifnull:判断第一个参数是否为空，如果为空用第二个参数替换
      select name, math, english, math + ifnull(english, 0) as 总分 from student;
      
      // 注意：参与运算的必须是数值型
      ```

   3. 条件查询

      ```mysql
      select * from 表名 where 条件;
      
      // 比较运算符
      >,<,<=,>=,=,<>   <>在SQL中表示不等于，在mysql中可以使用!=，没有==
      between ... and  在一个范围内，比如 between 100 and 200
      in     			 集合表示多个值，使用逗号分割
      like "张%"		模糊查询
      is NULL 		查询某一列为空，注：不能写=NULL
      // NULL不能使用=去判断，应该使用 is NULL，is not NULL
      
      eg：
      select 字段名 from 表名 where 字段 in (数据1，数据2...);
      select 字段名 from 表名 where 字段 not in (数据1，数据2...);
      select * from student where english between 75 and 90;
      
      select * from 表名 where 字段名 like "通配符字符串";
      // 通配符：% 匹配任意多个字符串；_ 下划线匹配一个字符
      // 比如：
      ```

   4. 逻辑运算

      ```mysql
      and 或 &&	与，SQL中建议使用前者，后者并不通用
      or 或 ||		或
      not 或 !		非
      ```

      

   

## MySql约束与设计

1. 排序

   ```mysql
   // 通过 order by 子句将查询的结果进行排序展示出来，不会影响数据库中数据的顺序
   select 字段名 from 表名 where 字段=值 order by 字段名 [asc|desc];
   asc：升序，默认值
   desc: 降序
   
   // 单排序
   select * from student order by age asc;
   
   // 组合排序
   select 字段名 from 表名 where 字段=值 order by 字段名1 [asc|desc], 字段名2 [asc|desc];
   eg:
   select * from student order by age desc, math asc;
   // 查询所有的数据，在年龄降序的基础上，如果年龄相同再以数学成绩的升序进行排序
   ```

2. 聚合函数

   ```mysql
   // 之前我们进行的都是横向的查询，它们都是根据条件一行一行的进行判断，而使用聚合函数是纵向查询，它是对一列的值进行计算，然后返回一个结果值。聚合函数会忽略空置NULL。
   max(列名)		求这一列的最大值
   min(列名)		求这一列的最小值
   avg(列名)		求这一列的平均值
   count(列名)	统计这一列有多少条记录
   sum(列名)		对这一列进行求和
   
   // 语法
   select 聚合函数(列名) from 表名;
   eg:
   select count(id) as 总人数 from student;// 统计学生总人数
   select count(*) as 总人数 from student;// 统计学生总人数
   // 聚合函数对于NULL的记录不会统计，建议如果所统计的列中存在NULL，可以使用如下方法：
   select count(ifnull(列名, 0)) from student;// 如果记录为NULL，则给它一个默认值，这样统计的数据就不会有遗漏
   
   // 查询年龄大于20的总数
   select count(*) from student where age>20;
   // 查询数学成绩总分数
   select sum(math) 总分 from student;
   // 查询数学平均分
   select avg(math) 平均分 from student;
   // 查询数学成绩最高分
   select max(math) 最高分 from student;
   // 查询数学成绩最低分
   select min(math) 最低分 from student;
   ```

3. 分组

   ```mysql
   // 分组查询是指使用 group by 语句对查询信息进行分组，相同数据作为同一组
   select 字段1，字段2... from 表名 group by 分组字段 [having 条件];
   // group by 将分组字段结果中相同内容作为同一组，如按照性别分组则分成2组
   // group by 将分组字段结果中相同内容作为一组，并且返回每组的第一条数据，所以单独分组没有什么用处。分组的目的是为了统计，一般分组会跟聚合函数一起使用。
   
   // 按性别进行分组，求男生女生的数学平均分
   select sex, avg(math) from student group by sex;// 聚合函数和group by一起用没什么问题
   // 第一个sex并不是一定要强制出现,所以也可以写成这样
   select avg(math) from student group by sex;
   
   // 按性别进行分类，并统计每组有多少人
   select sex, count(*) from student group by sex;
   // 先过滤掉年龄小于25岁的人。，然后按照性别进行分组，最后统计每组的人数
   select count(*), sex from student where age>25 group by sex;
   // 查询年龄大于25岁，按性别进行分组，统计每组的人数，并且只显示性别人数大于2
   select sex, count(*) from student where age>25 group by sex having count(*)>2;
   // 统计产品各分组的总和
   select product, sum(price) from orders group by product having sum(price)>30;
   ```

    当遇到数据库重复数据，就要将数据进行分组，取其中的一条来进行展示，此时就要用到 `group by`，但当MySQL的版本高于5.7的时候，在执行 `group by`时，select字段不属于 `group by`的字段的话，就会报如下的错误：

   > 1055 - Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'zz.student.name' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

   永久的解决方法：

   在windows下，用记事本打开my.ini文件，在[mysqld]中添加如下语句：

   ```mysql
   sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
   ```

   之后重启mysql才会生效。

   详细解决方案可以参考：[https://blog.csdn.net/liqinglonguo/article/details/131128659](https://blog.csdn.net/liqinglonguo/article/details/131128659)

4. having 与 where 的区别

   where：

   1. 将不符合where条件的数据在分组之前进行过滤，即先过滤后分组
   2. where后面不可以使用聚合函数

   having：

   1. having子句的作用是筛选满足条件的组，即在分组之后过滤数据，即先分组后过滤
   2. having后面可以使用聚合函数

5. limit的作用是限制，限制查询记录的条数

   1. 语法格式

      ```mysql
      select *|字段列表 [as 别名] from 表名 [where子句] [group by子句] [having子句][order by 子句][limit 子句];
      ```

   2. 参数说明

      1. offset：起始行数，从0开始计数，如果省略，默认就是0
      2. length：返回计数的行数

6. 数据库备份与还原

   1. 备份的应用场景

      在服务器进行数据传输，数据存储和数据交换时，就有可能产生数据故障。比如发生意外停机或存储介质损坏，这时如果没有采取必要的备份与恢复手段，就会导致数据的丢失，造成的损失是无法估量的。

   2. 备份与还原的语句

      1. 备份格式：DOS下，未登录模式，这是一个可执行文件exe，在bin文件夹

         ```mysql
         mysqldump -u用户名 -p密码 数据库 > 文件路径
         
         # 备份day21数据库中的数据到 d:\day21.sql文件中
         mysqldump -uroot -proot day21 > d:\day21.sql
         ```

         数据库中的表和数据都会导出成sql语句

      2. 还原格式：mysql中的命令，需要登录后才可以使用

         ```mysql
         use 数据库;
         source 导入文件的路径;
         
         # 1. 删除day21数据库中的所有表
         # 2. 登录MySQL
         # 3. 选中数据库
         # 4. 使用source命令还原数据
         # 5. 查看还原结果
         
         use day21;
         source d:/day21.sql;
         ```

         图形界面的备份与导出因为自己已经用过，所以就不备注了。

7. 数据库中表的约束

   1. 约束的作用

      对表中的数据进行限制，保证数据的正确性，有效性和完整性。一个表如果添加了约束，不正确的数据将无法插入到表中。约束在创建表的时候添加比较合适。

   2. 约束的种类

      主键约束：primary key，不重复，非空

      唯一：unique

      非空：not null

      外键：foreign key

      检查约束：check 注：MySQL不支持

      ```mysql
      # 创建主键的方式
      # 1. 在创建表的时候给字段添加主键
      字段名 字段类型 primary key
      # 2. 在已有表中添加主键
      alter table 表名 add primary key(字段名);
      # 注意：一个表中只能有一个主键，但它可以由多个任意列组成，以标识唯一的行，我们称之为联合主键
      
      # 删除主键
      # 删除st5表的主键
      alter table st5 drop primary key;# 一次性删除所有列的主键
      # 错误的删除方式
      alter table st5 modify id int;
      
      # 添加主键
      alter table st5 add primary key(id);
      alter table st5 modify id int primary key;
      
      # 主键自增
      auto_increment 表示自动增长，字段类型必须是整数型，只能用于主键上
      # 插入数据
      insert into st6 (name, age) values('小乔', 18);
      insert into st6 values(null, '周瑜', 35);
      
      # 修改自增长的默认起始值
      # 默认auto_increment的开始值是1，如果希望修改起始值，可以使用以下语句
      create table 表名(
      	字段 字段类型 primary key aotu_increment
      ) auto_increment=起始值;
      # 创建好以后修改起始值
      alter table 表名 auto_increment=起始值;
      # 删除自动增长
      alter table 表名 modify id int;# 这种方式可以删除自动增长，但是主键是删不掉的。
      # 添加自动增长
      alter table 表名 modify id int auto_increment;
      
      # 注：自动增长跟上一条记录有关系，从上一条记录的值+1
      
      # delete 和 truncate 对自增长的影响
      # delete：删除所有记录以后自增长没有影响
      # truncate: 删除以后，自增长从1重新开始
      ```

      ```mysql
      # 唯一约束
      # 创建的时候添加唯一约束
      字段名 字段类型 unique
      # 唯一约束不能添加重复值，但是多个null并不涉及唯一性
      # 删除唯一约束
      alter table st4 modify name varchar(20); # 这个方法并不能删除唯一约束，应使用下面这个
      alter table st4 drop index name;
      # 创建表以后添加唯一约束
      alter table st4 modify name varchar(20) unique;
      
      
      
      # 非空约束
      # 创建表时添加约束
      字段名 字段类型 not null
      # 删除非空约束
      alter table 表名 modify 字段名 字段类型;
      eg:
      alter table st4 modify name varchar(20);
      # 添加非空约束
      alter table st4 modify name varchar(20) not null;
      
      
      
      # 默认值
      字段名 字段类型 default 默认值
      -- 创建一个学生表 st9，包含字段(id,name,address)， 地址默认值是广州
      create table st9 (
          id int,
          name varchar(20),
          address varchar(20) default '广州'
      )
      -- 添加一条记录,使用默认地址
      insert into st9 values (1, '李四', default);
      select * from st9;
      insert into st9 (id,name) values (2, '李白');
      ```

      ```mysql
      # 外键约束
      什么是外键约束：在从表中与主表中主键对应的那一列，我们称之为外键
      主表：一方，用来约束别人的表
      从表：多方，被别人约束的表，即含有外键的表
      使用场景：1. 对从表的外键进行插入和更新操作时，避免外键并不存在于主表主键中；2. 删除了主表中的主键，会对从表中引用了该主键的外键产生影响。以上两种情况都会引起外键约束
      
      
      # 在创建表时，添加外键
      create table 表名(
      	...
          # 外键列
          constraint 外键名称 foreign key(外键名称) references 主表名(主表列名称)#这个地方可以是主键，也可以是被唯一约束的其他的列，所以写成主列表名称 
      );
      # 已有表增加外键
      alter table 从表 add constraint 外键名称 foreign key(外键字段名) references 主表(主键字段名);
      
      # 删除外键
      alter table 从表 drop foreign key 外键名称;
      # 外键可以为null，但是不可以是不存在的值
      
      思考：如何把部门表中的主键2变为5
      1. 现将员工表中的外键值为2的改为null，然后再将部门表中的主键改为5，最后把员工表中的null改为5。
      
      注：外键可以有多个
      ```

      ```mysql
      # 级联操作
      在修改和删除主表的主键时，同时更新和删除副表的外键值，称为级联操作
      on update cascade 级联更新。更新主表中的主键，从表中的外键也自动同步更新
      on delete cascade 级联删除
      
      # 创建表时
      create table employee(
      	id int primary key auto_increment,
      	name VARCHAR(20),
      	age int,
      	dep_id int,
      	constraint emp_dep_fk foreign key(dep_id) references department(id) on update cascade on delete cascade
      );
      # 表已经存在时添加 外键和级联操作
      alter table employee add constraint emp_dep_fk foreign key(dep_id) references deppartment(id) on update cascade on delete cascade;
      -- 记住，如果在创建表的时候已经加入了外键，执行此语句会报错，只能先删除外键，然后再执行该语句
      ```




## 多表之间的关系

> **关系介绍**

1. 一对一
2. 一对多 或 多对一
3. 多对多



> **多表关系实现**

1. 一对多（多对一）

   实现方式：在多的一方建立外键，指向一的一方的主键

2. 多对多

   多对多关系实现需要借助第三张表，中间至少包含两个字段，这两个字段作为第三张表外键，分别指向两张表的主键

3. 一对一

   可以在任意一方添加唯一（unique）外键，指向另一方的主键

案例：

```mysql
-- 创建线路分类表
create table tab_category(
	cid int primary key auto_increment,
	cname varchar(100) not null unique
);
insert into tab_category (cname) values ('周边游'), ('出境游'), ('国内游'), ('港澳游');

drop table tab_route;
-- 创建线路表
create table tab_route(
	rid int primary key auto_increment,
	rname varchar(100) not null unique,
	price double,
	rdate date,
	cid int,
	foreign key(cid) references tab_category(cid)-- 这个是简写方式，系统会默认给外键分配一个外键名，这个外键名在删除外键的时候会用到
);

-- show create table tab_route;

INSERT INTO tab_route VALUES
(NULL, '【厦门+鼓浪屿+南普陀寺+曾厝垵 高铁 3 天 惠贵团】尝味友鸭面线 住 1 晚鼓浪屿', 1499,'2018-01-27', 1),
(NULL, '【浪漫桂林 阳朔西街高铁 3 天纯玩 高级团】城徽象鼻山 兴坪漓江 西山公园', 699, '2018-02-22', 3),
(NULL, '【爆款￥1699 秒杀】泰国 曼谷 芭堤雅 金沙岛 杜拉拉水上市场 双飞六天【含送签费 泰风情 广州往返 特价团】', 1699, '2018-01-27', 2),
(NULL, '香港迪士尼乐园自由行 2 天【永东跨境巴士广东至迪士尼去程交通+迪士尼一日门票+香港如心海景酒店
暨会议中心标准房 1 晚住宿】', 799, '2018-04-10', 4);



-- 创建用户表
create table tab_user(
	uid int primary key auto_increment,
	username varchar(100) unique not null comment '用户名长度',
	password varchar(30) not null,
	name varchar(100) comment '真实姓名长度',
	birthday date,
	sex char(1) default '男',
	telephone varchar(11),
	email varchar(100)
);
INSERT INTO tab_user VALUES
(NULL, 'cz110', 123456, '老王', '1977-07-07', '男', '13888888888', '66666@qq.com'),
(NULL, 'cz119', 654321, '小王', '1999-09-09', '男', '13999999999', '99999@qq.com');
select * from tab_user;

-- 创建收藏表
create table tab_favorite(
	rid int,
	date datetime,
	uid int,
	-- 创建复合主键
	primary key(rid, uid),
	foreign key(rid) references tab_route(rid),
	foreign key(uid) references tab_user(uid)
);

INSERT INTO tab_favorite VALUES
(1, '2018-01-01', 1), -- 老王选择厦门
(2, '2018-02-11', 1), -- 老王选择桂林
(3, '2018-03-21', 1), -- 老王选择泰国
(2, '2018-04-21', 2), -- 小王选择桂林
(3, '2018-05-08', 2), -- 小王选择泰国
(5, '2018-06-02', 2); -- 小王选择迪士尼
```

![image-20230707180747234](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307071807378.png)





## 数据库设计的范式

> 概念

设计数据库时，需要遵循的范式。要遵循后面范式的要求，就一定遵循前面的范式的要求。

设计数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据冗余越小。

目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。

一般来说，数据库满足第三范式就可以了。

> **第一范式**（1NF）：每一列都是不可分割的原子数据项

数据库表的每一列都是不可分割的原子数据项，不能是集合，数组等非原子数据项。即表中的某个列有多个值时，必须拆分为不同的列。简而言之，第一范式每一列不可再拆分，称为原子性。

比如下面这个表就不满足第一范式：

![image-20230707182234262](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307071822346.png)

下面这个表满足原子性：

![image-20230707183351789](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307071833877.png)

这个表存在的问题：

- 存在非常严重的数据冗余（重复）：姓名，系名，系主任
- 数据添加存在问题：添加新开设的系和系主任时数据不合法，因为没有招生
- 数据删除存在问题：张无忌同学毕业了，删除数据，会将系的数据一起删除。



> **第二范式（2NF）**：在1NF的基础上，非码属性必须完全依赖于码（在1NF基础上消除非主属性对主码的部分函数依赖）

在满足第一范式的前提下，表中的每一个字段都完全依赖于主键。

所谓完全依赖是指不能存在仅依赖主键一部分的列。**简而言之，第二范式就是在第一范式的基础上所有列完全依赖于主键列。**当存在一个复合主键包含多个主键列的时候，才会发生不符合第二范式的情况。比如有一个主键有两个列，不能存在这样的属性，它只依赖于其中一个列，这是不合理，它得依赖于两个列，但这就不符合第二范式。

第二范式的特点：

- 一张表只描述一件事情
- 表中每一列都完全依赖于主键



几个概念：

1. 函数依赖：A——>B，如果通过A属性（属性组）的值，可以确定唯一B属性的值，则称B依赖于A

   例如：学号 ——> 姓名、（学号、课程名称）——> 分数、

2. 完全函数依赖：A——>B，如果A是一个属性组，则B属性值的确定需要依赖于A属性组中所有的属性值。

   例如：（学号，课程名称）——> 分数

3. 部分函数依赖：A——>B，如果A是一个属性组，则B属性值的确定只需依赖于A属性组中部分值即可。

   例如：（学号，课程）——> 姓名

4. 传递函数依赖：A——>B，B——>C，如果通过A属性（属性组）的值，可以确定唯一B属性的值，再通过B属性（属性组）的值可以确定唯一C属性的值，则称C传递依赖于A

   例如：学号——>系名，系名——>系主任

5. 码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性（属性组）为该表的码

   例如：该表的码为：（学号、课程名称）

   主属性：码属性组中的所有属性

   非主属性：除码属性组的属性

注意：如何理解 “唯一确定”的含义：

- 学号——>姓名：即指我通过学号能够唯一确定一个姓名，不会由此产生第二个不一样姓名
- （学号， 课程）——> 分数：即指我通过（学号，课程名称）可以唯一确定一个分数，不会产生第二个不一样的分数
- （学号，课程）——> 姓名：通过（学号，课程）并不能唯一确定一个姓名，由（学号，课程）可以确定多个姓名，因此并不能唯一确定。并且姓名部分依赖于学号



第二范式（2NF）：在1NF的基础上，非码属性必须完全依赖于码（在1NF的基础上消除非主属性对主码的部分函数依赖）

理解：在这个表中，（学号，课程名称）是候选码，其他的属性是非主属性，（学号，课程名称）可以唯一确定分数，

![image-20230707183351789](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307071833877.png)

然而，姓名，系名，系主任对于主码（学号，课程名称）是部分依赖，只依赖**学号**即可。

因此需要对表进行拆分：

首先可以将学号，课程名称，分数拆分成一个表：

![image-20230707205721041](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072057107.png)

剩下的四个非主属性中：学号（学好这个属性肯定是要留着的），课程名称，系名，系主任，其中学号就是主码，因此就形成了下面的表：

![image-20230707210002485](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072100557.png)

此时就可以解决第一个问题：消除数据冗余：

![image-20230707210133721](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072101817.png)

但是剩下的两个问题仍然没有解决：1、数据添加存在问题；2、数据删除存在问题

接下来看第三范式。



> **第三范式（3NF）**：在2NF的基础上，任何非主属性不依赖于其他非主属性（在2NF基础上消除传递依赖）

![image-20230707210133721](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072101817.png)

将这个表进行第三范式进行拆分，我们首先对其分析一下：

学号可以唯一确定系名，系名可以唯一确定系主任，因此这个表是存在传递依赖的。我们知道学生与系的关系是多对一的关系，因此在建立外键关系的时候，在多的一方建立外键，一的一方为主表主键，学生表是多的一方，因此在学生表建立外键，系表为主表。因此对上表进行拆分可以得到如下表结构：

![image-20230707211101075](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072111146.png)

![image-20230707211111673](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072111727.png)

将重复的数据删除掉：

![image-20230707211219548](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072112608.png)

因此解决了：1、数据添加存在的问题：开设新的系，数据合法。

​						2、数据删除存在的问题：删除学生表的一个学生，并不会影响系表的数据。

因此，一般只要满足前三个范式，表的设计基本上就已经很合理了。

总结一下：

![image-20230707213200179](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072132251.png)





## 多表查询

> **查询语法**

```mysql
select
	列名列表
from
	表名列表
where ...
```



> **笛卡尔积现象**

笛卡尔积，又叫cross join，**是SQL中两表连接的一种方式**。 假如A表中的数据为m行，B表中的数据有n行，那么A和B做笛卡尔积，结果为m*n行。 通常我们都要在实际SQL中避免直接使用笛卡尔积，因为它会使“数据爆炸”，尤其是数据量很大的时候。



如何清除笛卡尔积现象？

我们发现不是所有的数据都是有用的，因此需要使用条件多滤掉没用的数据。

比如：

```mysql
select * from dept,emp where dept.id = emp.dept_id;
```



> **内连接**

用左边表的记录去匹配右边表的记录，如果符合条件则显示出来。如：从表.外键=主表.主键



**1. 隐式内连接**

看不到join关键字，条件使用where指定。

```mysql
select 字段名 from 左表，右表 where 条件;

-- 查询所有的员工信息和部门名称
select * from emp, dept where emp.dept_id = dept.id;

-- 以后尽量采用这种方式写语句
select
	e.name, -- 员工表姓名
	d.name, -- 部门名称
from
	emp e,
	dept d
where 
	e.dept_id = d.id;
```

![image-20230707224026577](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072240653.png)

**2. 显示内连接**

使用inner join... on 语句，可以省略 inner

```mysql
select 字段名 from 左表 别名 [inner] join 右表 别名 on 条件;

-- 查询唐僧的信息，显示员工id，姓名，性别，工资和所在的部门名称
select 
	e.id 编号, 
	e.name 员工姓名, 
	gender 性别, 
	salary 工资,  
	d.name 部门名字 
from 
	emp e 
inner join 
	dept d 
on 
	e.dept_id=d.id 
where 
	e.name='唐僧';
```

内连接的步骤：

- 确定查询哪些表
- 确定表连接的条件
- 确定查询的条件
- 确定查询的字段



**3. 左外连接**

使用 left outer join ... on, outer 可以省略

```mysql
select 字段名 from 左表 left [outer] join 右表 on 条件;
```

用左边表的记录去匹配右边表的记录，如果符合条件的则显示输出；否则，显示 null。

可以理解为：在内连接的基础上保证左表的数据全部显示（左表是部门，右边是员工）

```mysql
-- 使用内连接查询
select * from dept d inner join emp e on d.id=e.dept_id ;

-- 使用左外连接查询
select * from dept d left outer join emp e on d.id=e.dept_id;
```

![image-20230707230003490](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072300573.png)

![image-20230707230013545](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072300623.png)

有这个结果可知：使用内连接，用左边表匹配右边表，匹配上的结果输出；而使用左外连接，左边表的数据全部输出，右边表的数据如果被匹配上则输出，没有匹配上就输出null。因此可以理解为：左外连接，实则在内连接的基础上保证左边表的数据全部显示出来。



**4. 右外连接**

使用 right outer join  ... on，outer可以省略。

```mysql
select 字段名 from 左表 right [outer] join 右表 on 条件;
```

```mysql
insert into emp(name, gender, salary, join_date) values('沙僧', '男', 6666, '2013-02-24');

select * from dept d right outer join emp e on d.id=e.dept_id;
```

![image-20230707231403559](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072314634.png)

用右表的记录去匹配左表的记录，如果符合条件的则显示；否则，显示NULL。

可以理解：在内连接的基础上保证右表的数据全部显示。



**5. 子查询**

- 一个查询的结果作为另一个的查询条件
- 有查询的嵌套，内部查询称为子查询
- 子查询要使用括号

子查询的结果有三种情况：

- 子查询的结果是单行单列

  在where后面作为条件，父查询使用，常用比较运算符：>，<，<>，= 等

  ```mysql
  select 查询字段 from 表 where 字段=（子查询）;
  
  -- 根据最高工资到员工表查询对应的员工信息
  select * from emp where salary=(select max(salary) from emp);
  
  -- 到员工表查询工资小于平均工资的员工信息
  select * from emp where salary < (select avg(salary) from emp);
  ```

- 子查询的结果是多行单列

  结果类似一个数组，父查询使用时用 IN 运算符

  ```mysql
  select 查询字段 from 表 where 字段 in (子查询);
  
  -- 查询工资大于5000的员工所在的部门名称
  select name from dept where id in (select dept_id from emp where salary > 5000);
  
  -- 查询开发部与财务部所有的员工信息
  select * from emp where dept_id in (select id from dept where name='开发部' or name = '销售部');
  select * from emp where dept_id in (select id from dept where name in ('开发部','销售部'));
  ```

- 子查询的结果是多行多列

  子查询结果是多列，肯定在from后面作为表；子查询作为表需要取别名，否则这张表没有名称访问表中的字段。

  ```mysql
  select 查询字段 from  (子查询) 表别名 where 条件;
  
  -- 查询出2011年以后入职的员工信息，包括部门名称
  select * from (select * from emp where join_date>'2011') c , dept d where c.dept_id = d.id;
  
  -- 也可以使用表连接
  select * from emp, dept  where emp.join_date>'2011' and dept.id = emp.dept_id;
  
  select * from emp inner join dept on emp.dept_id = dept.id where emp.join_date>'2011';
  
  select * from emp inner join dept on emp.dept_id = dept.id and emp.join_date>'2011';
  ```

  子查询小结：

  - 子查询结果是单列，则可以在where后面作为条件
  - 子查询结果是多列，则在from后面作为表进行二次查询





## 事务

1、事务的场景说明

> 什么是事务

在实际的开发中，一个业务操作如：转账，往往是要多次访问数据库才能完成操作的。转账是一个用户扣钱，另一个用户加钱的过程。如果一条SQL语句出现异常，那么这条SQL语句就可能执行失败。

事务执行是一个整体，所有的sql语句都必须执行成功。如果其中一条执行失败，那么所有的SQL语句都必须回滚，整个业务执行失败。

2、手动提交事务

MySQL中有两种事务提交方式：

1）手动提交方式

2）自动提交方式

> 手动提交方式

| 功能     | SQL语句            |
| -------- | ------------------ |
| 开启事务 | start transaction; |
| 提交事务 | commit;            |
| 回滚事务 | rollback;          |

> 回滚点的操作语句

| 回滚点的操作语句 | 语句             |
| ---------------- | ---------------- |
| 设置回滚点       | savepoint 名字   |
| 回到回滚点       | rollback to 名字 |

手动提交事务的过程：

1）执行成功的过程：开启事务——>执行多条语句——>成功提交事务

2）执行失败的情况：开启事务——>执行多条语句——>事务的回滚

![image-20230709000428574](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307090004648.png)



```mysql
-- 开启事务
start transaction;

-- 执行SQL语句
update account set balance = balance - 500 where name='张三';
update account set balance = balance + 500 where name='李四';

-- 发现执行没有问题，提交事务
commit;

-- 发现出问题，回滚事务
rollback;
```

MySQL数据库事务默认自动提交事务：

- 事务提交的两种方式
  - 自动提交
    - MySQL就是自动提交的
    - ==**一条DML（增删改）语句会自动提交一次事务**==
  - 手动提交
    - oracle数据库默认是手动提交
    - 需要先开启事务，再提交
- 修改事务的默认提交方式
  - 查看事务的默认提交方式：select @@autocommit;   -- 1 代表自动提交、0 代表手动提交
  - 修改默认提交方式：set @@autocommit = 0;

```mysql
select @@autocommit;-- 1 代表自动提交，0代表手动提交
set @@autocommit = 0;

update account set balance=30;
select * from account;

commit;-- 如果设置成了手动提交，则一定要有commit，否则对数据库的更改不会生效。
```



> 事务的四大特征

1、原子性：不可分割的最小操作单位，要么同时成功，要么同时失败。

2、持久性：如果事务提交或回滚，数据库会发生持久化的保存数据。

3、隔离性：多个事务之间相互独立。

4、一致性：事务操作前后，数据总量不变。



> 事物的隔离级别

概念：多个事务之间是隔离的，相互独立的。但是如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别就可以解决这些问题。

存在的问题：

- 脏读：一个事务，读取到另一个事务中没有提交的数据

- 不可重复读（虚读）：在同一个事务中，两次读到的数据不一样

- 幻读：一个事务操作（DML）数据表中的所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改


隔离级别：

- read uncommitted：读未提交
  - 产生的问题：脏读，不可重复读，幻读
  - 在多个事务并发进行过程中，一个事务可以读取到另一个事务未提交的数据。
- read committed：读已提交
  - 产生的问题：不可重复读、幻读
  - 在多个事务并发进行过程中，读事务实际读取到的数据是更新事务以前的数据，更新事务被提交了以后，读事务才可以读到被更新的数据。同时这也就导致了在同一个事务中，出现了两次查询结果不一致的情况
- repeatable read：可重复读
  - 产生的问题：幻读
  - 在多个事务并发进行过程中，有读事务和更新事务两个事务，在更新事务更新了数据并且提交了之后，除非读事务也提交本事务，否则在整个读事务的过程中，读到的数据都是更新事务以前的数据。这也就解决了不可重复读的问题。
- serializable：序列化，串行化顺序执行
  - 解决所有问题
  - 在多事务并发进行过程中，如果一个事务在操作一个数据表，则另一个事务是不可以再操作这个数据表的，提交的操作该事物会让其一直处于等待的状态。只有当前者事务提交或回滚了操作，后者事务才可以继续操作。

注意：隔离级别从小到大安全性越来越高，但是执行效率越来越低。

数据库查询隔离级别：

```mysql
select @@tx_isolation;
```

数据库设置隔离级别：

```MySQL
set global transaction isolation level 级别字符串;

eg:
set global transaction isolation level read committed;
```

注：需要把当前会话关掉重新打开才会生效。



**如何设置事务的隔离级别**

1、在MySQL的配置文件my.cnf，my.ini中设置：

```mysql
transaction-isolation=READ-UNCOMMITTED # 读未提交
或
transaction-isolation=READ-COMMITTED # 读已提交
或
transaction-isolation=REPEATABLE-READ # 可重复读
或
transaction-isolation=SERIALIZABLE # 串行化
```

2、可以在连接MySQL服务端命令行用 `--transaction-isolation`；

3、可以使用 `set transaction`命令改变单个或者所有新链接的事务隔离级别

```mysql
SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE}

# 如
set session transaction isolation level read committed;
```

- 不带 GLOBAL 或 SESSION 关键字表示设置下一个事务的隔离级别；
- 使用 GLOBAL 关键字表示对全局设置事务隔离级别，设置后的事务隔离级别对所有新的数据库连接生效；
- 使用 SESSION 关键字表示对当前的数据库连接设置事务隔离级别，只对当前连接生效；
- 任何客户端都可以自由改变当前会话的事务隔离级别，可以在事务中间改变，也可以改变下一个事务的隔离级别。



**如何查看MySQL事务的隔离级别**

```MySQL
# MySQL8.0之前
select @@global.tx_isolation;
select @@session.tx_isolation;
select @@tx_isolation;

# MySQL8.0
select @@global.transaction_isolation;
select @@session.transaction_isolation;
select @@transaction_isolation;
```

在 MySQL 8.0 之前的版本中 `tx_isolation` 是作为 `transaction_isolation` 的别名被应用的，新版本已经弃用了，需要把 `tx_isolation` 换成 `transaction_isolation`，否则会出现 `1193 - Unknown system variable 'xx_isolation'` 错误。





**隔离级别演示：**

```mysql
set global transaction isolation level read uncommitted;
start transaction;-- 记住演示的两端要开启事务，因为一条DML语句是自动提交的
-- 转账操作
update account set balance = balance - 500 where id = 1;
update account set balance = balance + 500 where id = 2;

rollback;
commit;
```



## 管理用户

SQL分类：

- DDL：操作数据库和表
- DML：增删改表中的数据
- DQL：查询表中的数据
- DCL：管理用户，授权

DBA：数据库管理员

DCL：管理用户、授权

- 管理用户

  - 添加用户

    ```mysql
    create user '用户名'@'主机名' identified by '密码';
    
    create user 'zhangsan'@'localhost' identified by '123';
    create user 'lisi'@'%' identified by '123';
    ```

  - 删除用户

    ```mysql
    drop user '用户名'@'主机名';
    drop user 'lisi'@'%';
    ```

  - 修改用户密码

    ```mysql
    update user set password =password('新密码') where user='用户名';
    update user set password =password('abc') where user='lisi';
    
    -- 另一种修改密码的方式
    set password for '用户名'@'主机名' = password('新密码');
    ```

    以下是MySQL5.7修改密码的方式：

    ```mysql
    mysql> update mysql.user set authentication_string=password('123qwe') where user='root' and Host = 'localhost';
    
    mysql> alter user 'root'@'localhost' identified by '123';
    
    mysql> set password for 'root'@'localhost'=password('123');
    
    -- 最后记得要刷新
    mysql> flush privileges;
    ```

  - 忘记用户密码(适用MySQL5.7)

    ```mysql
    -- 1. 关闭MySQL
    cmd——> net stop mysql   -- 使用管理员权限
    -- 2. 使用无验证方式启动MySQL
    mysqld --skip-grant-tables -- 此时这个窗口处于暂停状态
    -- 3. 重新打开一个窗口，输入MySQL
    mysql
    -- 4. 修改密码
    -- 如果修改密码的时候报错：ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
    -- 你需要先刷新一下权限表：flush privileges;之后再执行修改密码的操作即可。
    -- 别忘记修改密码之后flush privileges
    
    -- 5. 关闭所有窗口,在进程里关掉mysqld.exe进程
    
    -- 6. 管理员启动MySQL
    cmd——> net start mysql
    
    -- 7. 使用新密码登录MySQL
    ```

  - 查询用户

    ```mysql
    -- 1.切换数据库
    use mysql;
    -- 2.查询user表
    select * from user;
    ```

    

- 授权

  ```mysql
  -- 查询权限
  show grants for '用户名'@'主机名';
  -- 创建的用户如果没有授权是不能操作数据库的，只能登陆数据库
  
  -- 授予权限
  grant 权限列表 on 数据库.表名 to '用户名'@'主机名';
  grant select on 数据库名.表名 to '用户名'@'主机名';
  
  -- 授予单个权限
  grant select on zz.account to 'lisi'@'localhost';
  show tables; -- 只能看到被授权的表，也只有查询的权限
  
  -- 授予多个权限
  grant select, delete, update on zz.account to 'lisi'@'localhost';
  
  -- 给张三用户授予所有权限，在任意数据库任意表上
  grant all on *.* to 'zhangsan'@'localhost';
  
  
  -- 撤销权限
  revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
  revoke update on zz.account from 'lisi'@'localhost';
  ```

  





























