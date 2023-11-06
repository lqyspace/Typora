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

## MySQL中的数据类型

![image-20230928000520044](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309280005471.png)

![image-20230928001207394](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309280012469.png)



## SQL

1. Structured Query Language 结构化查询语言

2. Sql作用：

   1. 是一种所有关系型数据库的查询规范，不同的数据库都支持
   2. 通用的数据库操作语言，可以用在不同的数据库中
   3. 不同的数据库SQL语句有一些区别

3. SQL分类：

   1. Data Definition Language（DDL数据定义语言），如：建库，建表（**create，drop，alter，rename，truncate**）
   2. Data Manipulation Language（DML数据操作语言），如：对表中的数据的增删改（**insert，delete，update**）
   3. Data Query Language（DQL数据查询语言），如：对数据的查询（**select**）
   4. Data Control Language（DCL 数据控制语言），如：用户权限的设置（**grant，revoke，commit，rollback，savepoint**）

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

      ==注意：==Database不能改名。一些可视化工具可以改名，它是建新表，把所有的表复制到新库中，再删旧库完成。

   2. 查询数据库

      ```mysql
      // 查询数据库
      show databases;
      // 查看某个数据库的定义信息，即创建语句
      show create database 库名;
      或
      show create database 库名\G;
      // 查询某个变量
      show variables like 'character_%';
      // 查询当前正在使用的数据库
      select database(); # select database() from dual;
      // 查看某个库下的所有表
      show tables from 库名;
      // 使用数据库
      use 库名;
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
         show tables; # show tables from 数据库名;
         
         // 查看表结构
         desc 表名;
         describe 表名;
         
         // 查看创建表的SQL语句
         show create table 表名;
         ```
      
      4. 快速创建一个表结构相同的表
      
         ```mysql
         // 快速创建一个表结构相同的表
         create table 新表名 like 旧表名;
         
         // 基于现有的表创建一个新的表,同时把查询的数据导入
         create table myemp2
         as 
         select employee_id, last_name, salary
         from employees;
         # 注意：如果在select查询的字段中有别名，则新建的表的字段就是别名
         
         
         # 创建表的时候不要数据
         create table employee_blank
         as 
         select employees
         where 1=2; // 因为1不可能等于2，因为where始终为false，所以到时候创建的表为空 
         ```
      
      5. 删除表
      
         - 在MySQL中，当一张数据表没有与其他任何数据存在关联关系时，可以将当前数据表直接删除。
         - 数据和结构都会被删除
         - 所有正在运行的相关事务会被提交
         - 所有相关索引被删除
         - drop table 语句不能回滚
      
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
         
         // 添加字段在某个字段的后面或前面
         alter table 表名 add [column] 字段1 字段类型 [default 默认值] [first|after 字段名2];
         eg: alter table emp add salary double(10, 2) firt last_name;// 表示把这个字段放在第一位置，参数first/after。
         
         // 修改列类型modify
         alter table 表名 modify [column] 列名 新的类型;
         
         // 修改列名change
         alter table 表名 change [column] 旧列名 新列名 类型;
         
         // 删除列drop
         alter table 表名 drop 列名;
         
         // 修改表名
         rename table 表名 to 新表名;
         或
         alter table 旧表名 rename to 新表名;
         
         // 修改字符集
         alter table 表名 character set 字符集;
         
         // 删除一个字段
         alter table myempl drop column email;
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
      
      // 同时插入多条记录
      insert into 表名 values (val1, val2,...), (val1, val2,...), (val1, val2,...), (val1, val2,...)...;
      或者
      insert into 表名(字段1, 字段2,...) values (val1, val2,...), (val1, val2,...), (val1, val2,...), (val1, val2,...)...;
      
      
      // 将查询结果插入到表中
      insert into 目标表名
      (tar_column1 [, tar_column2, tar_column3,...])
      select 
      (src_column1 [, src_column2, src_column3,...])
      from 源表名
      [where condition]
      # 注意：在insert语句中加入子查询；不必书写values子句；子查询中的值列表应于insert子句中的列名对应
      eg:
      insert into emp
      select * from employees where department_id = 90;
      
      // salaries_reps字段的长度不能低于employees表中查询字段的长度
      // 如果低于的话就有添加不成功的风险
      insert into salaries_reps(id, salary, commission_pct)
      select employee_id, last_name, salary, commission_pct
      from employees
      where job_id like '%REP%';
      ```

      ==注意：==

      使用insert同时插入多条数据时，MySQL会返回一些执行单行插入时没有的额外信息，这些信息的含义如下：

      - records：表明插入的记录数
      - duplicates：表明插入时被忽略的记录，原因可能是这些记录包含了重复的主键值。
      - warnings：表明有问题的数据值，例如发生了数据类型转换。

      > 一个同时插入多行记录的insert语句等同于多个单行插入的insert语句，但是多行的insert语句在处理过程中效率更高。因为MySQL执行单条insert语句插入多行数据比使用多条insert语句块，所以在插入多条记录时最好选择使用单条insert语句的方式插入。

      ==小结：==

      - values也可以写成value，但是标椎的写法是values
      - 字符和日期型数据应包含在单引号中

   2. 更新

      ```mysql
      // 不带条件
      update 表名 set 字段名=值, 字段名=值...;  -- 修改所有的行
      
      // 带条件
      update 表名 set 字段名=值, 字段名=值... where 字段名=值; -- 更新多个字段
      ```

      ==注意：==

      - 可以一次更新多条数据

      - 如果需要回滚数据，需要保证在DML前，进行设置：set autocommit = false

      - 使用where子句指定需要更新的数据

      - 如果省略where子句，则表中的数据都将被更新

      - 更新中的数据完整性错误

        ```MySQL
        update employess set department_id=55 where department_id=110;
        ```

        ![image-20230929183715575](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309291837678.png)

        更新数据的时候是有可能不成功的，原因可能多种多样。对于这个错误有可能是因为约束的原因。

   3. 删除

      ```mysql
      // 删除表记录
      delete from 表名 where 条件表达式;
      delete from 表名;
      delete from 表名 where 字段名=值;
      
      // 使用truncate删除表中所有的记录；先删除表，再创建一模一样的表
      truncate [table] 表名;
      
      truncate：删除表中的内容，不删除表结构，释放空间；不能删除一个带有外键的表，如果要删除首先要取消外键，然后再删除
      delete：删除内容，不删除表结构，但不释放空间
      ```

      ==小结==：

      - 使用where子句删除指定的记录

      - 如果省略了where子句，则表中的数据将被全部删除

      - 删除中的数据完整性错误

        ![image-20230929184010670](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309291840759.png)

      

      4. MySQL8新特性：计算列

         什么叫计算列？简单来说就是某一列的值是通过别的列计算得来的。例如，a列值为1，b列值为2，c列不需要手动插入，定义a+b的结果为c的值，那么c就是计算列，是通过别的列计算得来的。

         在MySQL8中，create table 和 alter table都支持增加计算列。

         举例：定义数据表tb1，然后定义字段id，字段a，字段b和字段c，其中字段c为计算列，用于计算a+b的值。首先创建测试表tb1，语句如下：

         ```MySQL
         create table tb1(
         id int,
         a int,
         b int,
         c int generated always as (a+b) virtual
         );
         ```
      
         ![image-20230929184625183](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309291846264.png)
      
         
      
      truncate 与 delete 的区别：
      
      1）条件删除：delete支持条件删除，可以带where，而truncate只能删除整个表
      
      2）事务回滚：delete是数据库操作语言（DML），操作时原数据会被放到rollback segment中，可以被回滚，所以==可以进行回滚和提交操作==；而==truncate是数据库定义语言（DDL）==，操作时自动进行commit，==不能进行回滚==。
      
      committ：提交数据。一旦执行commit，则数据则永久被保存在数据库中，意味着数据不可以回滚。
      
      rollback：回滚数据。一旦执行rollback，则可以实现数据的回滚，回滚到最近的一次commit。
      
      3）清理速度：在数据库量比较小的情况，delete和truncate的清理速度差不多大。但是数据量很大的时候就不一样了，由于truncate是不支持回滚的，所以使用的系统和事务日志资源少，速度快。delete语句每删除一行，并在事务日志中为所删除的每行记录一项，固然会慢，但是相对来说比较安全。
      
      4）高水位重置：随着不断的进行表记录的DML操作，会不断提高表的高水位线（HWM），delete操作之后虽然表的数据删除了，但是并没有降低表的高水位（数据会从删除前的最后一行续写），随着DML操作数据库容量也只上升，不会下降。所以如果使用delete删除数据，就算删除很多，在查询时还是和delete操作前速度一样；而truncate操作会重置高水位线（数据重新从1开始），数据库容量也会被重置，之后再进行DML操作也会有所提升。
      
      **小结：**
      
      ​	truncate table 则一次性地从表中删除所有的数据页并不把单独的删除操作记录记入日志中保存，删除行是不能恢复的。并且在删除的过程中不会激活与表有关的删除触发器。执行速度快。
      
      ​	delete table语句可以通过where对要删除的记录进行选择。而使用truncate table将删除表中的所有记录。因此delete语句更加灵活。
      
      ​	drop和truncate都是DDL，不能够激活触发器，因为该操作不记录各行删除；drop删除表，内容连带结构一起删除，表不复存在。
      
      ​	delete命令不能自动提交事务，操作会触发trigger；而truncate，drop命令，执行后会自动提交事务，操作不触发trigger。
      
      ​	速度：一般来说，drop>truncate>delete
      
      ​	DDL与DML的说明：
      
      ​		DDL的操作一旦执行，就不可以回滚。指令set autocommit = false 对DDL操作失效。(因为在执行完DDL之后，会自动执行commit操作，因此无法回滚)
      
      ​		DML的操作默认情况下，一旦执行就不可以回滚。但是，如果在执行DML之前，执行了 set autocommit = false，则执行的DML操作就可以实现回滚。
      
      ```MySQL
      set autocommit = false;
      delete from emp2;
      # truncate table emp2;
      
      select * from emp2;
      rollback;
      
      select * from emp2;
      ```
      
      > 阿里云开发规范：
      >
      > 【参考】truncate table 比 delete 速度快，且使用的系统和事务日志资源少，但truncate无事务且不触发trigger，有可能造成事故，故不建议在开发代码中使用此语句。
      >
      > 说明：truncate table 在功能上与不带where子句的delete语句相同。

   **内容扩展：**

   ![image-20230929172255772](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309291722902.png)

   ![image-20230929173805361](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309291738481.png)

   ![image-20230929173831392](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309291738466.png)

   在上面的两次实验中，均只是创建了一个表book1，book2并没有被创建。

   从上面的两次实验中可以看出，MySQL5.7并没有DDL的回滚功能，因此在删除book1和book2时，即使出现了报错，也会把已有的表删除；而在MySQL8中，由于存在回滚的功能，报错的时候会把以前的操作撤销掉。

   

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
      eg: select distinct department_id from employees;
      eg: select distinct department_id, salary from employees;
      注意：distinct 是对后面所有的列名的组合进行去重
      ```
   
      注：起别名时，`as`可以省略
   
   2. 查询结果参与运算
   
      ![image-20230915133520764](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151335399.png)
   
      ![image-20230915133731291](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151337373.png)
   
      ![image-20230915133848862](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151338923.png)
   
      ![image-20230915133910924](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151339003.png)
   
      ![image-20230915191837511](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151918594.png)
   
      ==注意：==取模的时候只和被取模数有关系，与取模数没有关系。
   
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
      
      // 空值参与运算
      // 所有运算符或列值遇到null值，运算的结果都为null
      select employee_id, salary, commission_pct, 12*salary*(1+commission_pct) "annual_sal" from employees;
      注：在MySQL里面，空值并不等于空字符串。一个空字符串的长度是0，而一个空值的长度是空。而且，在MySQL里面，空值是占用空间的
      
      select 100 + '1' from dual;// 在Java语言中，结果是 1001，在SQL中，+号没有连接的作用，就表示加法运算。会将字符串变为数值（隐式转换）
      select 100 + 'a' from dual; // 输出100，因为 'a' 没法转换为数值，因此被转换为0
      ```

   3. 条件查询

      比较运算符用来对表达式左边的操作数和右边的操作数进行比较，**比较的结果为真则返回1，比较的结果为假则返回0**，其他情况则返回NULL。 
   
      比较运算符经常被用来作为SELECT查询语句的条件来使用，返回符合条件的结果记录。
   
      **3.1 等号运算符**

      - ==等号运算符（=）判断等号两边的值，字符串或表达式是否相等，如果相等则返回1，否则返回0.==
      - 如果等号两边的值，字符串或表达式都为字符串，则MySQL会按照字符串进行比较，其比较的是字符串中字符的ASCII编码是否相等。
      - 如果等号两边的值都是整数，则MySQL会按照整数来比较两个值的大小。
      - 如果等号两边的值一个是整数，一个是字符串，则MySQL会将字符串转化为数字进行比较。
      - ==如果等号两边的值，字符串或表达式中有一个为null，则比较结果为null==
      - 注意：SQL中的赋值符号使用 `：=`
   
      ![image-20230915171532606](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151715695.png)
   
      **3.2 安全等于运算符**
   
      安全等与运算符（<=>）与等于运算符（=）作用相似，唯一的区别是 `<=>` 可以用来对NULL进行判断。在两个数均为NULL的情况下，其值返回为1，而不为NULL；当一个操作数为NULL时，其值返回为0，并不为NULL。
   
      ![image-20230915193655662](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151936736.png)
   
      **3.3 不等于运算符**
   
      不等于运算符（<> 或 !=）用于判断两边的数字，字符串或表达式的值是否有不相等的情况，如果不相等则返回1，如果相等则返回0；不等于运算符不能用来判断NULL值。**如果两边的值有任意一个为NULL，或者两边都为NULL，则结果为NULL。**SQL语句如下：
   
      ![image-20230915194239567](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151942637.png)
   
      ```mysql
      select * from 表名 where 条件;
      // 比较运算符
      >,<,<=,>=,=,<>   <>在SQL中表示不等于，在mysql中可以使用!=，没有==
      between ... and  在一个范围内，比如 between 100 and 200
      in（not in）     			 集合表示多个值，使用逗号分割
      like "张%"		模糊查询
      is NULL 		查询某一列为空，注：不能写=NULL
      // NULL不能使用=去判断，应该使用 is NULL，is not NULL
      
      eg：
      select 字段名 from 表名 where 字段 in (数据1，数据2...);
      select 字段名 from 表名 where 字段 not in (数据1，数据2...);
      select * from student where english between 75 and 90;
      
      select * from 表名 where 字段名 like "通配符字符串";
      // 通配符：% 匹配任意多个字符串；_ 下划线匹配一个字符
      
      // 此时查询的结果没有，因为salary为null，null与前面的salary进行比较时为0，不是1，因此就没有查询到结果
      select last_name, salary from emp where salary=null;
      ```
   
      **3.4 非符号类型运算符**
   
      ![image-20230915195232395](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309151952523.png)
   
      ```mysql
      # is null ，is not null , isnull
      select last_name, commission_pct from employee where commission_pct is null;
      select last_name, commission_pct from employee where commission_pct is not null;
      select last_name, commission_pct from employee where ISNULL(commission_pct);
      select last_name, commission_pct from employee where not commission_pct <=> null;
      ```
   
      ```mysql
      # least | greatest
      select least('a', 'b', 'c'), greatest('g', 'b', 't', 'm') from dual;
      select least(length(first_name), length(last_name)), least(first_name, last_name) from dual;
      ```
   
      ```mysql
      # between and: 包括左右边界值
      select employee_id, last_name, salary from employee where salary between 6000 and 8000;
      select employee_id, last_name, salary from employee where salary >= 6000 and salary <= 8000;
      select employee_id, last_name, salary from employee where salary between 8000 and 6000;# 查询无结果，条件下届和条件上界不能交换
      
      # not between ... and ...
      select employee_id, last_name, salary from employee where salary not between 6000 and 8000;
      select employee_id, last_name, salary from employee where salary < 6000 or salary > 8000;
      ```
   
      ```mysql
      # in， not in
      select last_name, salary, department_id from employees where department_id in (10, 20, 30);
      select last_name, salary, department_id from employees where department_id not in (10, 20, 30);
      ```
   
      ```mysql
      # like
      select last_name from employees where last_name like '%a%';
      # % 代表不确定个数的字符串：0,1，多个
      # _ 代表一个字符
      
      # 查询last_name中既包含a也包含e的字符
      select last_name from employees where last_name like '%a%e%' or last_name like '%e%a%';
      select last_name from employees where last_name like '%a%' and last_name like '%e%';
      
      # 查询last_name第二个字符是a的字符
      select last_name from employees where last_name like '_a%';
      # 查询last_name第二个字符是下划线第三个字符是a的字符，使用转义符 \
      select last_name from employees where last_name like '_\_a%';
      # 定义 $ 为转义字符
      select last_name from employees where last_name like '_$_a%' escape '$';
      ```
   
      ```mysql
      # regexp | rlike:正则表达式
      REGEXP运算符用来匹配字符串，语法格式为：expr REGEXP 匹配条件。如果expr满足条件，返回1；如果不满足，返回0。若expr货匹配条件任意一个为NULL，则结果为NULL。
      ```
   
      ![image-20230916114819662](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161148775.png)
   
      ![image-20230916115121757](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161151820.png)
   
      ==注意：== `select 'shhkstart regexp 'hk'` 匹配是否包含字符。
   
      `select 'atguigu regexp '[ab]'` 匹配包含是否有a或者b。
   
      `select 'atguigu regexp 'gu.gu'` 匹配包含 `gu.gu`。
   
   4. 逻辑运算
   
      ```mysql
      and 或 &&	与，SQL中建议使用前者，后者并不通用
      or 或 ||		或
      not 或 !		非
      ```
   
      ![image-20230916134214384](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161342470.png)
      
      ==and 的优先级是高于or的优先级。==
      
      ![image-20230916134614318](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161346394.png)
      
      ![image-20230916134717550](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161347625.png)
      
      ![image-20230916134833581](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161348633.png)
      
      ![image-20230916134916864](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161349919.png)
      
   4. 位运算符
   
      ![image-20230916144608257](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161446328.png)
      
      ![image-20230916144636089](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161446148.png)
      
      ![image-20230916144711747](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161447807.png)
   
      ![image-20230916144855561](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161448616.png)
      
      ![image-20230916144918087](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161449618.png)
      
   6. 结论
   
      我们需要保证表中的字段，表名等没有和保留字，数据库系统或常用的方法冲突。如果真的相同，请在SQL语句中使用一对  `` (着重号) 引起来。
   
      select查询还可以对常数进行查询，就是在select的查询结果中新增一列固定的常数列，这列的取值是我们固定的，而不是从数据库动态获取的。
   
       一般来说我们只从一个表中查询数据，通常不需要增加一个 固定的常数列，但如果我们想整合不同的数据源，用常数列作为这个表的标记，就需要查询常数。
   
      ```mysql
      select '尚硅谷' as corporation, last_name from employees;
      ```
   
      ![image-20230916145057922](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161450997.png)
   
   7. 扩展
   
      ![image-20230916150726173](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161507251.png)
   
      （1）查询以特定字符或字符串开头的记录
   
      ```mysql
      select * from fruits where f_name regexp '^b';
      ```
   
      （2）查询以特定字符或字符串结尾的纪录
   
      ```mysql
      select * from fruits where f_name regexp 'b&';
      ```
   
      （3）用符号 . 来代替字符串中的任意一个字符
   
      ```mysql
      select * from fruits where f_name regexp 'a.b';
      ```
   
      （4）使用'*'和'+'来匹配多个字符
   
      ```mysql
      select * from fruits where f_name regexp '^ba+';
      ```
   
      （5）匹配指定字符串：只要包含这个字符即可，多个字符可以使用 '|'
   
      ```mysql
      select * from fruits where f_name regexp 'on';
      select * from fruits where f_name regexp 'on|ap';
      ```
   
      这种方式和`like`是有一定的差别的，like匹配的字符串如果出现在文本的中间位置，那么这个字符就不会被匹配出来，相应的行也不会被返回。而regexp在匹配的过程中，如果要匹配的字符串出现在文本的中间位置，那么regexp就会找到它，相应的行就会被返回。
   
      ```mysql
      mysql> SELECT * FROM fruits WHERE f_name like 'on';
      Empty set(0.00 sec)
      ```
   
      （6）匹配指定字符中的任意一个
   
      ```mysql
      select * from fruits where f_name regexp '[ot]';
      select * from fruits where sid regexp '[456]';
      ```
   
      （7）匹配指定字符以外的字符
   
      ```MySQL
      select * from fruits where f_id regexp '[^a-e0-9]'
      ```
   
      （8）使用{n, }或{n, m}来指定字符串连续出现的次数
   
      ```MySQL
      # 匹配前面出现的x至少2次
      select * from fruits where f_name regexp 'x{2, }';
      # 匹配前面出现的字符串ba最少1次，最多3次
      select * from fruits where f_name regexp 'ba{1, 3}';
      ```
   
   8. 练习题
   
      ```MySQL
      # 选择工资不在5000到12000的员工的姓名和工资
      select name, salary from employees where salary not between 5000 and 12000;
      
      # 选择在20或50号部门工作的员工姓名和部门号
      select name, dept_id from dept where dept_id in (20, 50);# where dept_id=20 or dept_id=50;
      
      # 选择公司中没有管理者的员工姓名及job_id
      select name, job_id from dept where manager_id is null;# manager_id <=> null
      
      # 选择公司中有奖金的员工姓名，工资和奖金级别
      select name, salary, le from employees where le is not null;
      
      # 选择员工姓名的第三个字母是a的员工姓名
      select name from employees where name like '__a%';
      
      # 选择姓名中有字母a和k的员工姓名
      select name from employees where name like '%a%' and name like '%k%';
      select name from employees where name like '%a%k%' or name like '%k%a%';
      
      # 显示出表 employees表中first_name以e结尾的员工信息
      select first_name from employees where first_name regexp 'e$';
      select first_name from employees where first_name like '%e';
      
      # 显示出表employees的manager_id是100,101,110的员工姓名，工资，管理者id
      select name, salary, manager_id from employees where manager_id in (100, 101, 110); 
      
      # 显示出employees部门编号在80-100 之间的姓名，工种
      select name, kinds from employees where dept_id between 80 and 100;# where dept_id>=80 and dept_id<=100;
      ```
   
      
   



## MySql约束与设计

1. 排序

   ```mysql
   # 通过 order by 子句将查询的结果进行排序展示出来，不会影响数据库中数据的顺序
   # order by 后面的字段不一定是查询的字段
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
   
   
   # 我们可以使用列的别名进行排序
   # 但是列的别名只能在order by里面使用，不能在where里面使用
   select employee_id, salary, salary * 12 annual_salary
   from employees
   where department_id in (50, 60, 70) # where annual_salary > 81600 是错误的
   order by department_id desc;
   # 注意：where需要申明在from之后，order by 之前
   # 解析select语句时，先from查看表，然后where查看条件过滤的列，再就是select查看需要哪些列（或者别名），最后就是order by，也就是说，别名是在order by 之前，在where之后，所以别名在order by里面可以使用，但是在where里面不可使用。
   ```

2. 分页

   ```MySQL
   limit [位置偏移量, ]行数
   # 前10条记录
   select * from 表名 limit 0,10;
   或者
   select * from 表名 limit 10;
   # 10-20条记录
   select * from 表名 limit 10, 10;
   # 20-30条记录
   select * from 表名 limit 20, 10;
   ```

   ![image-20230916154556470](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309161545540.png)

   

3. 聚合函数

   ```mysql
   // 之前我们进行的都是横向的查询，它们都是根据条件一行一行的进行判断，而使用聚合函数是纵向查询，它是对一列的值进行计算，然后返回一个结果值。
   // 聚合函数会忽略空值NULL。
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

   ==注意==：

   - count(*)会统计表中记录总数，适用于任何数据类型
   - count(列名)返回列名不为空的记录总数
   - ==问题：用count(*)，count(1)，count(列名)谁好呢？==
     - 对于MyISAM引擎是没有区别的，这种引擎内部有一个计数器在维护着行数；Innodb引擎的表用count(*)，count(1)直接读取行数，复杂度是O(n)，因为Innodb真的要去数一遍。但好于具体的count(列名)
   - ==问题：能不能使用count(列名)替换count(*)？==
     - 不能，因为count(*)是SQL92定义的标椎统计行数的语法，跟数据库无关，跟NULL和非NULL无关。
     - count(*)会统计值为NULL的行，而count(列名)不会统计列为null值的行。

4. 分组

   ```mysql
   // 分组查询是指使用 group by 语句对查询信息进行分组，相同数据作为同一组
   select 字段1，字段2... from 表名 group by 分组字段 [having 条件];
   // group by 将分组字段结果中相同内容作为同一组，如按照性别分组则分成2组
   // group by 将分组字段结果中相同内容作为同一组，并且返回每组的第一条数据，所以单独分组没有什么用处。分组的目的是为了统计，一般分组会跟聚合函数一起使用。
   
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
   
   # 多列字段分组
   select department_id, job_id, avg(salary)
   from employees
   group by department_id, job_id;
   # 或
   select department_id, job_id, avg(salary)
   from employees
   group by job_id, department_id; # 效果一样
   
   # 下面的这个是错误的
   select department_id, job_id, avg(salary)
   from employees
   group by department_id;
   # 结论：select中如果出现非组函数字段，那么这些字段必须声明在group by当中；但是group by中声明的字段不一定非得出现在select的查询字段当中。
   # group by 出现在from后面，where后面，order by前面，limit前面
   
   
   
   # group by中使用with rollup
   使用with rollup关键字之后，在所有查询出的分组记录之后增加一条记录，该记录计算查询出的所有记录的总和，即统计记录数量。
   select department_id,avg(salary)
   from employees
   where department_id > 80
   group by department_id with rollup;
   # 注意：当使用rollup时，不能同时使用order by子句对结果进行排序，即rollup和order by不能同时出现。
   ```

   ![image-20230919090222511](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309190902629.png) 

   当遇到数据库重复数据，就要将数据进行分组，取其中的一条来进行展示，此时就要用到 `group by`，但当MySQL的版本高于5.7的时候，在执行 `group by`时，select字段不属于 `group by`的字段的话，就会报如下的错误：

   > 1055 - Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'zz.student.name' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

   永久的解决方法：

   在windows下，用记事本打开my.ini文件，在[mysqld]中添加如下语句：

   ```mysql
   sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
   ```

   之后重启mysql才会生效。

   详细解决方案可以参考：[https://blog.csdn.net/liqinglonguo/article/details/131128659](https://blog.csdn.net/liqinglonguo/article/details/131128659)

5. having 与 where 的区别

   where：

   1. 将不符合where条件的数据在分组之前进行过滤，即==先过滤后分组==
   2. where后面不可以使用聚合函数

   having：

   1. having子句的作用是==筛选满足条件的组==，即==在分组之后过滤数据==，即先分组后过滤
   2. having后面可以使用聚合函数
   3. ==having必须声明在group by的后面==
   4. 开发中我们使用having的前提是：SQL中使用的group by。如果没有group by，那么使用having的意义就不大。因为没有group by的话，我们就不用分组，也就不用考虑每个组里面出现的最大值，即相当于考虑的是整个表的最大值，那么此时完全就可以不用having，因此select里面是可以使用聚合函数的。

   ```MySQL
   # 分组之后过滤数据
   # 查询各个部门中最高工资比10000高的部门信息
   select department_id, max(salary)
   from employees
   group by department_id
   having max(salary)>10000;
   
   # 查询部门ID为10,20,30,40这四个部门中最高工资比10000高的部门信息
   # 方式1
   select department_id, max(salary)
   from employees
   where department_id in (10, 20, 30, 40)
   group by department_id
   having max(salary)>10000;
   # 或 方式2
   select department_id, max(salary)
   from employees
   group by department_id
   having max(salary)>10000 and department_id in (10, 20, 30, 40); # 效果一样
   ```

   思考：既然having既可以用聚合函数，也可以用非聚合函数，那么为什么不把where删除掉不使用呢？

   答：

   - 首先在SQL92中就存在了where和having，然后在SQL99当中where依然存在，这说明where存在即合理，即where一定有存在的必要性。不过我们更推荐使用方式1，==因为方式1的执行效率高于方式2。==
   - 当过滤条件中有聚合函数时，则此过滤条件必须声明在聚合函数中；当过滤条件中没有聚合函数时，则此过滤条件声明在where或having中都可以，但是建议大家都声明在where中。

   - ==where与having对比==

     - ==where可以直接使用表中的字段作为筛选条件，但是不能使用分组中的计算函数作为作为筛选条件；having必须要与group by配合使用，可以把分组计算的函数和分组字段作为筛选条件。==
       - 这决定了，在需要对数据进行分组统计的时候，having可以完成where不能完成的任务。这是因为，在查询语法结构中，where在group by 之前，所以无法对分组结果进行筛选。having 在group by之后，可以使用分组字段和分组中的计算函数，对分组的结果集进行筛选，这个功能是where无法完成的。另外，where排除的记录不再包括在分组中。
     - ==如果需要通过连接从关联表中获取需要的数据，where是先筛选后连接，而having是先连接后筛选。==
       - 这一点决定了在关联查询中，where比having更高效。因为where可以先筛选，用一个筛选后的较小数据集和关联表进行连接，这样占用的资源比较少，执行的效率也比较高。having则需要先把结果集准备好，也就是用未被筛选的数据济南进行关联，然后对这个大的数据集进行筛选，这样占用的资源就比较多，执行效率也就比较低。

   - 小结：

     | where  | 先筛选再关联，执行效率高   | 不能使用分组中的计算函数进行筛选 |
     | ------ | -------------------------- | -------------------------------- |
     | having | 先关联再筛选，执行效率较低 | 可以使用分组中的计算函数         |

   - 开发中的选择，where和having也不是相互排斥的，我们可以在一个查询里同时使用where和having。包含分组统计函数的条件用having，普通条件用where。这样，我们就既利用了where条件的高效快速，又发挥了having可以使用包含分组统计函数的条件查询的优点。当数据量特别大的时候，运行效率会有较大差别。

   

6. limit的作用是限制，限制查询记录的条数

   1. 语法格式

      ```mysql
      select *|字段列表 [as 别名] from 表名 [where子句] [group by子句] [having子句][order by 子句][limit 子句];
      ```

   2. 参数说明

      1. offset：起始行数，从0开始计数，如果省略，默认就是0
      2. length：返回计数的行数

7. 数据库备份与还原

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

8. 数据库中表的约束

   1. 约束的作用

      对表中的数据进行限制，保证数据的正确性，有效性和完整性。一个表如果添加了约束，不正确的数据将无法插入到表中。约束在创建表的时候添加比较合适。

   2. 约束的种类

      主键约束：primary key，不重复，非空

      唯一：unique

      非空：not null

      外键：foreign key

      检查约束：check 注：MySQL不支持

      默认值约束：default
      
      ```mysql
      # 创建主键的方式
      # 1. 在创建表的时候给字段添加主键
      字段名 字段类型 primary key
      # 2. 在已有表中添加主键
      alter table 表名 add primary key(字段名);
      # 注意：一个表中只能有一个主键，但它可以由多个任意列组成，以标识唯一的行，我们称之为联合主键
      # 特点：
      # 1. 主键约束相当于：唯一约束+非空约束
      # 2. 一个表最多只能有一个主键约束，建立主键约束可以在列级上，也可以在表级上创建
      # 3. 主键约束对应着表中的一列或者多列（复合主键）
      # 4. 如果是多列组合的复合主键约束，那么这些列都不允许为空值，并且组合的值不允许重复。
      # 5. MySQL的主键名总是primary，就算自己命名了主键约束名也没用
      # 6. 当创建主键约束时，系统默认会在所在的列或列组合上建立对应的主键索引（能够根据主键查询的，就根据主键查询，效率更高）。如果删除主键约束了，主键约束对应的索引就会被自动删除
      # 7. 需要注意的是，不要修改主键字段的值。因为主键是数据记录的唯一标识，如果修改了主键的值，就有可能会被破坏数据的完整性。
      create table 表名称(
      	字段名 数据类型 primary key, #列级模式
      	字段名 数据类型,
      	字段名 数据类型
      );
      create table 表名称(
      	字段名 数据类型,
      	字段名 数据类型,
      	字段名 数据类型,
      	[constraint 约束名] primary key(字段名) #表级模式
      );
      create table 表名称(
      	字段名 数据类型,
      	字段名 数据类型,
      	字段名 数据类型,
      	[constraint 约束名] primary key(字段名1,字段名2) #表示字段1和字段2的组合是唯一的，也可以有更多个字段
      );
      
      
      # 删除主键
      # 删除st5表的主键
      alter table st5 drop primary key;# 一次性删除所有列的主键
      # 错误的删除方式
      alter table st5 modify id int;
      # 说明：删除主键约束不需要指定主键名，因为一个表只有一个主键，删除主键约束后非空还存在
      
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
      # 1. 一个列最多能只能有一个自增长列
      # 2. 当需要产生唯一标识符或顺序值时，可以设置自增长
      # 3. 自增长列约束的列必须是键列（主键列，唯一键列）
      # 4. 自增约束的列的数据类型必须是整数类型
      # 5. 如果自增列指定了0和null，会在当前最大值的基础上自增；如果自增列手动指定了具体值，直接赋值为具体值。
      
      
      # delete 和 truncate 对自增长的影响
      # delete：删除所有记录以后自增长没有影响
      # truncate: 删除以后，自增长从1重新开始
      ```
      
      ![image-20230929204519065](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292045696.png)
      
      ```MySQL
      # 如何指定自增约束
      # 1. 建表时
      create table 表名称(
      	字段名 数据类型 primary key auto_increment,
          字段名 数据类型 unique key not null,
          字段名 数据类型 unique key,
          字段名 数据类型 not null default 默认值
      );
      
      create table 表名称(
      	字段名 数据类型 default 默认值,
          字段名 数据类型 unique key auto_increment,
          字段名 数据类型 not null default 默认值,
          primary key(字段名)
      );
      
      create table employees(
      	eid int primary key auto_increment,
          ename varchar(20)
      );
      
      # 2. 表建后
      alter table 表名称 modify 字段名 数据类型 auto_increment;
      eg:
      create table employee(
      	eid int primary key,
          ename varchar(20)
      );
      alter table employee modify eid int auto_increment;
      
      # 删除自动增长约束
      alter table employee modify eid int;
      ```
      
      ==MySQL8.0新特性——自增变量的持久化==
      
      在MySQL8.0之前，自增主键auto_increment的值如果大于max(primary key)+1，在MySQL重启后，会重置auto_increment=max(primary key)+1，这种现象在某些情况下会导致业务主键冲突或者其他难以发现的问题。
      
      ![image-20230929212415277](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292124396.png)
      
      ![image-20230929212449170](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292124283.png)
      
      ![image-20230929212506018](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292125129.png)
      
      ==总结：==从上面的实验中可以看，MySQL8.0实现了自增主键的持久化，每次计数器发生改变都会将其重写入重做日志中。如果数据库重启，Innodb会根据重做日志中的信息来初始化计数器的内存值。而MySQL5.7则不能实现计数器的持久化，MySQL每次重启之后，内存中的计数器并不会持久化的内存中，当数据库重启时，该计数器会被初始化。
      
      
      
      ---
      
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
      # 特点
      # 同一个表中可以有多个唯一约束
      # 唯一约束可以是某一个列的值唯一，也可以多个列组合的值唯一
      # 唯一性约束允许列值为空
      # 在创建唯一约束的时候，如果不给唯一约束命名，就默认和列名相同
      # MySQL会给唯一约束的列上默认创建一个唯一索引
      
      # 唯一约束添加
      # 建表时
      create table 表名称(
      	字段名  数据类型,
          字段名  数据类型 unique,
          字段名  数据类型 unique key,
          字段名  数据类型
      );
      create table 表名称(
      	字段名  数据类型,
          字段名  数据类型,
          字段名  数据类型,
          [constraint 约束名] unique key(字段名|字段列表)
      );
      eg:
      create table student(
      	sid int,
      	sname varchar(20),
      	tel char(11) unique,
      	cardid char(18) unique key
      );
      CREATE TABLE t_course(
      	cid INT UNIQUE,
      	cname VARCHAR(100) UNIQUE,
      	description VARCHAR(200)
      );
      CREATE TABLE USER(
      	id INT NOT NULL,
      	NAME VARCHAR(25),
      	PASSWORD VARCHAR(16),
      	-- 使用表级约束语法
      	CONSTRAINT uk_name_pwd UNIQUE(NAME,PASSWORD)
      );
      
      # 建表后指定唯一索引
      # 字段列表中如果是一个字段，表示该列的值唯一。如果是两个或更多的字段，那么复合唯一，即多个字段的组合是唯一的
      alter table 表名称 add unique key(字段列表);
      alter table 表名称 modify 字段名 字段类型 unique;
      eg:
      ALTER TABLE USER ADD UNIQUE(NAME,PASSWORD);
      ALTER TABLE USER ADD CONSTRAINT uk_name_pwd UNIQUE(NAME,PASSWORD);
      ALTER TABLE USER MODIFY NAME VARCHAR(20) UNIQUE;
      
      eg:
      create table student(
      	sid int primary key,
      	sname varchar(20),
      	tel char(11) ,
      	cardid char(18)
      );
      alter table student add unique key(tel);
      alter table student add unique key(cardid);
      
      eg:
      create table 表名称(
      	字段名 数据类型,
      	字段名 数据类型,
      	字段名 数据类型,
      	unique key(字段列表) #字段列表中写的是多个字段名，多个字段名用逗号分隔，表示那么是复合唯一，即多个字段的组合是唯一的
      );
      
      #学生表
      create table student(
      	sid int, #学号
      	sname varchar(20), #姓名
      	tel char(11) unique key, #电话
      	cardid char(18) unique key #身份证号
      );
      #选课表
      create table student_course(
      	id int,
      	sid int,
      	cid int,
      	score int,
      	unique key(sid,cid) #复合唯一
      );
      #课程表
      create table course(
      	cid int, #课程编号
      	cname varchar(20) #课程名称
      );
      
      
      # 删除唯一性约束
      # 添加唯一性约束的列上也会自动创建唯一索引
      # 删除唯一约束只能通过删除唯一索引的方式
      # 删除时需要制定唯一索引名，唯一索引名就和唯一约束名一样
      # 如果创建唯一约束时未指定名称，如果是单列，就默认和列名相同；如果是组合列，就默认和（）中排在第一个的列名相同。也可以自定义唯一约束明
      select * from information_schema.table_constraints where table_name = '表名'; # 查看都有哪些约束
      # 删除唯一约束
      alter table user drop index uk_name_pwd;
      # 注意：可以通过 show index from 表名称; 查看表的索引
      
      
      
      
      
      
      # 非空约束
      # 创建表时添加约束
      字段名 字段类型 not null
      # 删除非空约束
      alter table 表名 modify 字段名 字段类型;
      或
      alter table 表名 modify 字段名 数据类型 NULL;
      eg:
      alter table st4 modify name varchar(20);
      # 添加非空约束
      alter table st4 modify name varchar(20) not null;
      # 特点：
      # 非空约束只能出现在表对象的列上，只能某个列单独限定为非空，不能组合为非空
      # 一个表中可以有很多列被限定为非空
      # 空字符串不等于NULL，0也不等于NULL
      
      
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
      
      ---
      
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
      # 第一步先查看约束名和删除外键约束
      select * from information_schema.table_constraint where table_name = '表名称'; # 查看某个表的约束名
      alter table 从表 drop foreign key 外键约束名称;
      # 第二步查看索引名和删除索引。（注意：只能手动删除）
      show index from 表名称; # 查看某个表的索引名
      alter table 从表 drop index 索引名;
      
      # 外键可以为null，但是不可以是不存在的值
      
      
      思考：如何把部门表中的主键2变为5
      1. 现将员工表中的外键值为2的改为null，然后再将部门表中的主键改为5，最后把员工表中的null改为5。
      
      注：外键可以有多个
      1. 从表的外键列，必须引用/参考主表的主键或唯一约束的列，为什么？因为被依赖，被参考的值必须是唯一的
      2. 在创建外键约束时，如果不给外键约束命名，默认名不是列名，而是自动产生一个外键名（例如：student_ibfk_1），也可以指定外键约束名；
      3. 创建（create）表时就指定外键约束的话，先创建主表，在创建从表
      4. 删除表时，先删除从表，再删除主表
      5. 当主表的记录被从表参照时，主表的记录将不被允许删除，如果要删除数据，需要先删除从表中依赖该记录的数据，然后从可以删除主表的数据
      6. 在从表中建立外键约束，并且一个表中可以有多个外键约束
      7. 从表的外键列与主表被参照的列名字可以不相同，但是数据类型必须一样，逻辑意义一致。如果类型不一样，创建子表时，就会出现错误 "ERROR 1005 (HY000): Can't create table 'database.tablename'(error:150)"
      8. 当创建外键约束时，系统默认会在所在列上建立对应的普通索引。但是索引名是外键的约束名。（根据外键查询效率很高）
      9. 删除外键约束后，必须手动删除对应的索引
      eg:
      # 添加外键约束
      # 1. 建表时
      create table 主表名字(
      	字段1 数据类型 primary key,
          字段2 数据类型
      );
      create table 从表名字(
      	字段1 数据类型 primary key,
          字段2 数据类型,
          [constraint <外键约束名>] foreign key(从表的某个字段) references 主表名(被参考的字段)
      );
      # （从表的某个字段）的数据类型必须与主表名（被参考字段）的数据类型一致，逻辑一致
      # （从表的某个字段）的字段名可以与主表名（被参考字段）的字段名一样，也可以不一样
      -- foreign key：在表级指定子表中的列
      -- references：标识在父表中的列
      
      create table dept(
      	did int primary key,
          dname varchar(50)
      );
      create table emp(
      	eid int primary key,
          ename varchar(5),
          deptid int,
          foreign key(did) references dept(did)
      );
      # 说明：主表dept必须先创建成功，然后才能创建emp表，指定外键成功
      # 删除表时，先删除从表emp，再删除主表dept
      
      
      # 2. 建表后
      # 格式：
      alter table 从表名 add [constraint 约束名] foreign key(从表的字段) references 主表名（被引用字段） [on update xx][on delete xx];
      
      举例：
      alter table emp1 add [constraint emp_dept_id_fk] foreign key(dept_id) references dept(dept_id);
      ```
      
      ![image-20230929223802532](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292238679.png)
      
      ![image-20230929224114645](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292241767.png)
      
      ![image-20230929215431500](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292154621.png)
      
      ![image-20230929215452736](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292154820.png)
      
      ![image-20230929215634260](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292156416.png)
      
      ![image-20230929215730390](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292157520.png)
      
      ![image-20230929215857815](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292158938.png)
      
      ==总结：约束关系是针对双方==
      
      - 添加了外键约束后，主表的修改和删除数据受约束，新增不受约束
      - 添加了外键约束后，从表的修改和添加数据受约束，删除不受约束
      - 在从表上建立外键，要求主表必须存在
      - 删除主表时，要求从表先删除，或将从表中外键引用该主表的关系先删除
      
      ==约束等级：==
      
      - cascade：在父表（主表）上update/delete记录时，同步update/delete掉子表（从表）的匹配记录
      - set null：在父表上update/delete记录时，将子表上匹配记录的列设为null，但是要注意字表的外键列不能为not null
      - no action：如果子表中有匹配的记录，则不允许对父表对应候选键进行update/delete操作
      - restrict：同no action，都是立即检查外键约束
      - set default：在可视化工具SQLyog可能显示空白，父表有变更时，子表将外键列设置成一个默认的值，但Innodb不能识别
      
      如果没有指定等级，就相当于`restrict`
      
      对于外键约束，最好采用：`on update cascade on delete restrict` 的方式。
      
      ![image-20230929222056425](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292220533.png)
      
      ![image-20230929222118475](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292221607.png)
      
      
      
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

9. check约束

   ![image-20230929224957124](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292250775.png)

   ![image-20230929225024258](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292250377.png)

10. default约束

    ![image-20230929225115265](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292251381.png)

    ![image-20230929225157081](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292251232.png)

    ![image-20230929225308790](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292254409.png)

    ![image-20230929225509263](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292255379.png)

    ![image-20230929225823692](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292258793.png)

    ![image-20230929225901624](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292259775.png)

    ![image-20230929225918351](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309292259450.png)

    ==面试：==

    **1、为什么建表时，加not null default '' 或 default 0**

    答：不想让表中出现null值

    **2、为什么不想要null值**

    （1）不好比较。null值是一个特殊值，比较时只能用专门的is null和is not null来比较。碰到运算符通常返回null。

    （2）效率不高。影响提高索引效率。因此我们往往在建表时not null default "" 或 default 0

    **3、带auto_increment约束的字段值是从1开始的吗**

    答：在MySQL中，auto_increment的初始值默认是1，每新增一条记录，字段值自动加1.设置自增属性（auto_increment）的时候，还可以指定第一条插入记录的自增字段的值，这样新插入的记录的自增字段值从初始值开始递增，如在表中插入第一条记录，==同时指定id为5，则以后插入的记录的id就会从5开始往上加。添加主键时往往需要设置字段自增属性==。

    **4、并不是每个表都可以任意选择存储引擎**

    外键约束不能跨越引擎使用。MySQL支持多种存储引擎，每个表都可以指定一个不同的存储引擎，需要注意的是：外键约束是用来保证数据的参照完整性的，如果表之间需要关联外键，却指定了不同的存储引擎，那么这些表之间是不能创建外键约束的。所以说，存储引擎的选择也不是随意的。



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

> **分类**

角度1：等值连接 vs 非等值连接

角度2：自连接 vs 非自连接

角度3：内连接 vs 外连接

总结：连接n个表，至少需要n-1个连接条件



> **笛卡尔积现象**

笛卡尔积，又叫cross join，**是SQL中两表连接的一种方式**。 假如A表中的数据为m行，B表中的数据有n行，那么A和B做笛卡尔积，结果为m*n行。 通常我们都要在实际SQL中避免直接使用笛卡尔积，因为它会使“数据爆炸”，尤其是数据量很大的时候。



如何清除笛卡尔积现象？

我们发现不是所有的数据都是有用的，因此需要使用条件多滤掉没用的数据。

比如：

```mysql
select * from dept,emp where dept.id = emp.dept_id;
```



多表连接的优化？

- 如果查询语句中出现了多个表中都存在的字段，则必须指明此字段所在的表。
- 从SQL优化的角度，建议多表查询时，每个字段前都指明其所在的表。可以给表起别名。
- 如果给表起了别名，一旦在select或where中使用表名的话，则必须使用表的别名，而不能使用表的原名。





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
[inner] join 
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

可以理解为：==在内连接的基础上保证左表的数据全部显示（左表是部门，右边是员工）==

```mysql
-- 使用内连接查询
select * from dept d inner join emp e on d.id=e.dept_id ;

-- 使用左外连接查询
select * from dept d left outer join emp e on d.id=e.dept_id;
```

![image-20230707230003490](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072300573.png)

![image-20230707230013545](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307072300623.png)

由这个结果可知：==使用内连接，用左边表匹配右边表，匹配上的结果输出==；==而使用左外连接，左边表的数据全部输出，右边表的数据如果被匹配上则输出，没有匹配上就输出null。因此可以理解为：左外连接，实则在内连接的基础上保证左边表的数据全部显示出来。==



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

==用右表的记录去匹配左表的记录，如果符合条件的则显示；否则，显示NULL。==

可以理解：在内连接的基础上保证右表的数据全部显示。



例题：查询所有的员工的last_name，department_name的信息。

注意：题目中说到所有的，因此一定使用的是外连接

```mysql
select employee_id, department_name from employees e, department_name, d where e.department_id = d.department_id；
```

![image-20230916225308849](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162253916.png)

![image-20230916225005640](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162250804.png)

![image-20230916225437980](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162254088.png)

![image-20230916225646127](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162256197.png)

> **总结：**

![image-20230916230501997](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162305603.png)

```MySQL
# 中图：内连接
select employee_id, last_name, department_name
from employees e join department d
on e.department_id = d.department_id;

# 左上图
select employee_id, last_name, department_name
from employees e left join department d
on e.department_id = d.department_id;
```

![image-20230916230853227](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162308298.png)

```mysql 
# 左下图：满外连接
select employee_id, department_name from employees e left join departments d
on e.department_id = d.department_id
union all
select employee_id,department_name from employees e right join departments d
on e.department_id = d.department_id
where e.department_id = d.department_id;
```

![image-20230916231304785](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162313850.png)

![image-20230916225316254](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309162253344.png)

## 不相关子查询

**5. 子查询**

- 一个查询的结果作为另一个的查询（父查询或主查询）条件
- 有查询的嵌套，内部查询称为子查询(内查询)
- 子查询要使用括号

子查询的分类：

- 从内查询返回的条目数：单行子查询 vs 多行子查询
  - 按内查询的结果返回一条还是多条记录，将子查询分为单行子查询还是多行子查询
- 内查询是否被执行多次：相关子查询 vs 不相关子查询
  - 相关子查询：查询的条件与子查询具有相关性
  - 不相关子查询：查询的条件与子查询不具有相关性

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

---

> **单行子查询**

**1、单行比较符**

![image-20230919150042117](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191500331.png)

![image-20230919150135290](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191501350.png)

![image-20230919150338035](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191503140.png)

```MySQL
select * from employees where salary > (select salary from employees where employee_id=149);

select employee_name, job_id, salary from employees where job_id=(select job_id from employees where employee_id=141) and salary>(select salary from employees where employee_id=143);

select last_name, job_id, salary from employees where salary=(select min(salary) from employees);

select employee_id, manager_id, department_id from employees where (manager_id, department_id) in (select manager_id, department_id from employees where employee_id in (141,174)) and employee_id not in (141, 174);
```



> **Having中的子查询**

- 首先执行子查询
- 向主查询中的having子句返回结果

查询最低工资大于50号部门最低工资的部门id和其最低工资

```MySQL
select department_id, min(salary) from employees where department_id is not null group by department_id having min(salary)>(select min(salary) from employees where department_id=50);
```

> **Case中的子查询**

显示员工的employee_id，last_name和location

其中，若员工department_id与location_id为1800的department_id相同，则location为Canada，其余则为USA。

```MySQL
select employee_id, last_name, (case department_id when (select department_id from employees where location=1800) then 'Canada' else 'USA' end) location from employees;
```

> **子查询中的空值问题**

```MySQL
SELECT last_name, job_id
FROM employees
WHERE job_id =
            (SELECT job_id
            FROM employees
            WHERE last_name = 'Haas');
```

当子查询为空时，对结果没有什么影响，只不过不显示记录而已。

> **非法使用子查询**

```MySQL
SELECT employee_id, last_name
FROM employees
WHERE salary =
                (SELECT MIN(salary)
                FROM employees
                GROUP BY department_id);
```

由于子查询返回的是多行数据，所以不能使用单行比较符。



**2、多行子查询**

- 也称集合比较子查询
- 内查询返回多行
- 使用多行比较操作符

多行比较操作符：

![image-20230919201833027](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192018095.png)

代码实例：

```MySQL
# 返回其他job_id中比job_id为'IT_PROG'部门任一工资低的员工的员工号，姓名，job_id以及salary
select employee_id, last_name, job_id, salary from employees where salary < ANY (select salary from employees where job_id='IT_PROG') and job_id<>'IT_PROG';
```

![image-20230919202842653](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192028757.png)

![image-20230919202540376](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192025464.png)

```mysql 
# 返回其它job_id中比job_id为‘IT_PROG’部门所有工资都低的员工的员工号、姓名、job_id以及salary
select employee_id, last_name, job_id, salary from employees where salary < ALL(select salary from employees where job_id='IT_PROG') and job_id<>'IT_PROG';
```

![image-20230919202925178](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192029249.png)

题目：查询平均工资最低的部门

```mysql
select department_id from employees group by department_id having avg(salary)=(select min(avg_sal) from (select avg(salary) avg_sal from employees group by department_id) dept_avg_sal);# 要给表取一个别名
```

```mysql 
select department_id from employees group by department_id having avg(salary)<=ALL(select avg(salary) avg_sal from employees group by department_id);
```

==注意==：在SQL中聚合函数是不能够嵌套的。

**空值问题：**

```MySQL
select last_name from employees where emplyee_id not in (select manager_id from employees  where manager is not null);
# 一定要主要manager_id是否为空，要不然就会导致主查询为空
```



## 相关子查询

![image-20230919211622904](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192116764.png)



==注意：==子查询中使用主查询中的列

题目：查询员工中工资大于本部门平均工资的员工的last_name，salary和其department_id

方式一：相关子查询

```MySQL
select last_name, salary, department_id from employees e where salary>(select avg(salary) from employees where department_id=e.department_id);
```

方式二：在from中使用子查询

```MySQL
select last_name, salary, e1.department_id from employees e1, (select department_id, avg(salary) as avg_sal from employees group by department_id) e2 where e1.department_id = e2.department_id and e1.salary > e2.avg.sal;
```

from型的子查询：子查询作为from的一部分，子查询要用()引起来，并且要给这个子查询起别名，把它当成一张“临时的虚拟的表”来使用。



**在order by中使用子查询**

题目：查询员工的id，salary，按照department_name排序

```MySQL
select employee_id, salary from employees e order by (select department_name from departments d where e.department_id = d.department_id) desc;
```

题目：若employees表中employee_id与job_history表中employee_id相同的数目不小于2，输出这些相同 id的员工的employee_id,last_name和其job_id

```MySQL
select employee_id, last_name, job_id from employees e where 2<=(
	select count(*) from job_history j where e.employee_id=j.employee_id 
);
```

==注意：== 在查询当中，除了group by 和 limit之外，其他的位置都可以声明子查询！



**相关更新**

![image-20230919224559502](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192245748.png)





**相关删除**

![image-20230919224634378](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192246459.png)



![image-20230919225102444](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309192251014.png)



---

**exists与not exists关键字**

- 关联子查询通常也会和exists操作符一起使用，用来检查在子查询中是否存在满足条件的行。
- 如果在子查询不存在满足条件的行：
  - 条件返回false
  - 继续在子查询中查找
- 如果在子查询中存在满足条件的行
  - 不在子查询中继续查找
  - 条件返回true
- not exists关键字表示如果不存在某种条件，则返回true，否则返回false。



题目：查询公司管理者的employee_id，last_name，job_id，department_id信息

方式一：存在一条记录就会输出

```MySQL
select employee_id, last_name, job_id, department_id
from employees e1
where exists (
	select * from employees e2
    where e2.manager_id = e1.employee_id
);
```

方式二：自连接

```MySQL
select distinct e.employee_id, e.last_name, e.job_id, e.department_id
from employees e join employees m on e.employee_id = m.manager_id;
```

方式三：

```MySQL
select employee_id, last_name, job_id, department_id from employees where employee_id in (select distinct manager_id from employees);
```



题目：查询departments表中，不存在于employees表中的部门的department_id 和 department_name

```mysql
select d.department_id, department_name from departments d where not exists (select * from employees where department_id = d.department_id);
```

```mysql
select d.department_id, d.department_name from departments d left join employees e on d.department_id = e.department_id where e.department_id is null;
```

**练习：**

```MySQL
# 查询和Zlotkey相同部门的员工姓名和工资
select last_name, salary from employees
where department_id in (select department_id from employees where last_name='Zlotkey');

# 查询工资比公司平均工资高的员工的员工号，姓名和工资
select job_id, job_name, salary from employees where salary > (select avg(salary) from employees);

# 选择工资大于所有JOB_ID='SA_MAN'的员工的工资的员工的last_name，job_id，salary
select last_name, job_id, salary from employees where salary > ALL (select salary from employees where job_id='SA_MAN');

# 查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名
select employee_id, last_name from employees 
where department_id in (select distinct department_id from employees where last_name like '%u%');

# 查询在部门的location_id为1700的部门工作的员工的员工号
select employee_id from employees where department_id in (select department_id from departments where location_id=1700);

# 查询管理者是king的员工姓名和工资
select last_name, salary from employees where manager_id in (select employee_id from employees where last_name='king');

# 查询工资最低的员工信息：last_name，salary
select last_name, salary from employees where salary = (select min(salary) from employees);

# 查询平均工资最低的部门的信息
select * from employees where department_id = (select department_id from employees group by department_id having avg(salary) = 
(select min(avg_sal) from (select avg(salary) from employees group by department_id) t_avg_sal));

select * from employees where department_id = (select department_id from employees group by department_id having avg(salary) <= ALL (select avg(salary) from employees group by department_id));

select * from employees where department_id = (select department_id from employees group by department_id having avg(salary) = (select avg(salary) avg_sal from employees group by department_id order by avg_sal asc limit 1));

select d.* from departments d, (select department_id, avg(salary) as avg_sal from employees group by department_id order by avg_sal asc limit 0,1) t_dept_avg_sal where d.department_id = t_dept_avg_sal.department_id;

# 查询平均工资最低的部门信息和该部门的平均工资（相关子查询）
select d.*, (select avg(salary) from employees where department_id=d.department_id) avg_s
from departments d, (select department_id, avg(salary) as avg_sal from employees group by department_id order by avg_sal asc limit 0,1) t_dept_avg_sal where d.department_id = t_dept_avg_sal.department_id;

# 查询平均工资最高的job信息
select *
from jobs
where job_id = (
select job_id from employees group by job_id having avg(salary)=(
	select max(avg_sal) from (select avg(salary) as avg_sal from employees group by job_id) t_dept_job
));

select *
from jobs
where job_id = (
select job_id from employees group by job_id having avg(salary) >= ALL (
	select avg(salary) as avg_sal from employees group by job_id
));

select *
from jobs
where job_id = (
select job_id from employees group by job_id having avg(salary) = (
	select avg(salary) as avg_sal from employees group by job_id order by avg_sal desc limit 0,1
));


select j.* from jobs j, (
	select job_id, avg(salary) as avg_sal from employees group by job_id order by avg_sal desc limit 0,1
) t_job where j.job_id=t_job.job_id;

# 查询平均工资高于公司的平均工资的部门有哪些
select department_id from employees where department_id is not null group by department_id having avg(salary) > (select avg(salary) from employees);

# 查询公司中所有manager的详细信息
select employee_id, last_name, job_id, department_id
from employees e1
where exists (
	select * from employees e2
    where e2.manager_id = e1.employee_id
);

# 各个部门中，最高工资中最低的那个部门的最低工资是多少？
select min(salary) from employees where department_id in (select department_id from employees group by department_id having max(salary) = (select max(salary) max_sal from employees group by department_id order by max_sal asc limit 0,1));
# 还有其他的写法就不写了，跟上面的类似


# 查询平均工资最高的部门的manager的详细信息：last_name, department_id, email, salary 
select last_name, department_id, email, salary from employees where employee_id = ANY (
select distinct manager_id from employees where department_id = (select department_id from employees group by department_id having avg(salary) = (select avg(salary) avg_sal from emlpoyees group by department_id order by avg_sal desc limit 0,1)));
# 还有其他的写法就不写了，跟上面的类似


# 查询部门的部门号，其中不包括job_id是"ST_CLERK"的部门号
# 方式一
select dept_id from departments where dept_id not in (select distinct dept_id from employees where job_id='ST_CLERK');
# 方式二
select dept_id from departments d where not exists (select * from employees e where d.dept_id = e.dept_id and e.job_id = 'ST_CLERK');


# 选择所有没有管理者的员工的last_name
select last_name from employees emp where not exists (select * from employees mgr where emp.manager_id = mgr.employee_id);

# 查询员工号，姓名，雇佣时间，工资，其中员工的管理者为 'De Haan'
select employee_id, last_name, hire_date, salary from employees where manager_id in (select employee_id from employees where last_name = 'De Haan');

select employee_id,last_name, hire_date, salary from employees e1 where exists (select * from employees e2 where e1.manager_id = e2.employee_id and e2.last_name='De Haan');

# 查询各部门中工资比本部门平均工资高的员工的员工号，姓名和工资（相关子查询）
select employee_id, last_name, salary from employees e1 where salary > (select avg(salary) from employees where dept_id = e1.dept_id);

select e.employee_id, e.salary, e.dept_id from employees e, (select dept_id, avg(salary) avg_sal from employees group by dept_id) t_dept_sal where e.dept_id=t_dept_sal.dept_id and e.salary > t_dept_sal.avg_sal;

# 查询每个部门下的部门人数大于5的部门名称（相关子查询）
select dept_name from departments d where exists (select * from employees where employee_id = d.dept_id having count(*)>5);

select dept_name from departments d where 5<(select count(*) from employees e where  e.dept_id = d.dept_id);

#查询每个国家下的部门个数大于2的国家编号（相关子查询）
select country_id from locations l where 2<(select count(*) from departments d where l.dept_id = d.dept_id);
```

> **子查询的编写技巧**
>
> 从里往外写、从外往里写

如何选择：

- 如果子查询相对较简单，建议从外往里写，一旦子查询结构复杂，则建议从里往外写。
- 如果是相关子查询，通常都是从外往里写。



## 单行函数

- 函数可分为内置函数和自定义函数
- DBMS之间的差异性很大，远大于同一个语言不同版本之间的差异
- 采用SQL函数的代码可移植性是很差的
- 内置函数从实现的功能角度可以分为：数值函数，字符串函数，日期和时间函数，流程控制函数，加密与解密函数，获取MySQL信息函数，聚合函数等。这些内置函数可以再分为 单行函数、聚合函数（分组函数）
- 单行函数
  - 操作数据对象
  - 接收参数返回一个结果
  - 只对一行进行变换
  - 每行返回一个结果
  - 可以嵌套
  - 参数可以是一列或一个值

![image-20230919153954584](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191539649.png)

单行函数：输入一个参数，输出一个结果；多行函数：输入多行参数，输出一行结果。

### 数值函数

#### 1 基本函数

![image-20230919154156521](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191542984.png)

![image-20230919154356726](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191543810.png)

使用round函数时，如果第二个参数为0，则保留0位小数，如果参数为1，则保留一位小数，如果参数为-1，则看个位的值是否大于5，如果大于5，则十位进1，个位变0，否则十位不变，个位变0。

使用truncate函数时，如果第二个参数为0，则保留整数，小数位截断；如果第二个参数为1，则保留一位小数，百分位截断；如果第二个参数为-1，则个位截断，都不进行四舍五入。

#### 2 角度与弧度互换函数

![image-20230919154607994](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191546053.png)



#### 3 三角函数

![image-20230919154635839](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191546908.png)

![image-20230919154745660](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191547849.png)

#### 4 指数与对数

![image-20230919155721293](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191557357.png)

#### 5 进制间的转换

![image-20230919155818329](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191558393.png)



### 字符串函数

![image-20230919155939168](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191559257.png)

![image-20230919155958565](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191559634.png)

![image-20230919160013467](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191600541.png)

### 日期和时间函数

#### 1 获取日期、时间

![image-20230919160206335](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191602412.png)

![image-20230919160243592](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191602651.png)

#### 2 日期与时间戳转换

![image-20230919160317866](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191603940.png)

![image-20230919160445152](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191604210.png)

#### 3 获取月份、星期、星期数、天数等函数

![image-20230919160521078](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191605162.png)

#### 4 日期的操作数

![image-20230919160854238](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191608321.png)

#### 5 时间与秒钟转换的函数

![image-20230919160958575](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191609630.png)

![image-20230919161008947](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191610010.png)

#### 6 计算日期和时间的函数

![image-20230919161044812](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191610896.png)

![image-20230919164931749](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191649810.png)

![image-20230919165007374](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191650459.png)

![image-20230919165152802](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191651892.png)

![image-20230919165211686](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191652748.png)

![image-20230919165231380](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191652439.png)

![image-20230919171857684](C:/Users/XiaoYun/AppData/Roaming/Typora/typora-user-images/image-20230919171857684.png)

![image-20230919171926014](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191719101.png)

![image-20230919173624823](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191736911.png)

![image-20230919173718687](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191737787.png)

### 控制流程函数

![image-20230919194651524](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191946713.png)

![image-20230919194859552](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191948737.png)

![image-20230919195110615](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191951691.png)

![image-20230919195146698](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191951777.png)

### 加密与解密

 加密与解密主要用于对数据库中的数据进行加密和解密处理，以防止数据被他人窃取。这些函数在保证数据库安全时非常有用。

![image-20230919195421975](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191954059.png)

### MySQL信息函数

MySQL中内置了一些可以查询MySQL信息的函数，这些函数主要用于帮助数据库开发或运维人员更好地对数据库进行维护工作。

![image-20230919195623406](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191956515.png)

![image-20230919195640016](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191956076.png)

### 其他函数

![image-20230919195723956](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191957032.png)

![image-20230919195754333](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191958909.png)

![image-20230919195822776](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309191958533.png)

```MySQL
# 显示系统时间
select NOW(), SYSDATE() from dual;

# 将员工的姓名按照首字母排序，并写出姓名的长度
select last_name, length(last_name) "name_length" from employees order by last_name asc, name_length asc; 

# 查询员工的id，last_name，salary，并作为一个列输出，别名为out_put
select concat(employee_id, last_name, salary) as "output" from employees;

# 查询公司各员工工作的年数，工作的天数，并按照工作年数的降序排序
select employee_id, datediff(curdate(), hire_date)/365 "work_years", datediff(curdate(), hire_date) "work_days" from employees order by work_years desc;

# 查询员工姓名，hire_date，department_id，满足以下条件：雇佣时间在1997年以后，department_id为80或110，commission_pct 不为空
select name, hire_date, department_id from employees where hire_date>='1997-01-01' and department_id in (80,90,110) and commission_pct is not null;
# and date_format(hire_date, '%Y-%m-%d') >= '1997-01-01'
# and date_format(hire_date, '%Y') >= '1997'
# and hire_date >= str_to_date('1997-01-01', '%Y-%m-%d');

# 查询公司中入职时间超过10000天的员工姓名，入职时间
select name, hire_date from employees where datediff(curdate(), hire_date)>=10000;

# 做一个查询，产生下面的结果
# <last_name> earns <salary> monthly but wants <salary*3>
select concat(last_name, 'earns', truncate(salary, 0), 'monthly but wants', truncate(salary*3, 0)) 'Dream Salary' from employees;

# 查询公司中平均奖金率
select sum(commission_pct) / count(ifnull(commission_pct, 0)), avg(ifnull(commission_pct, 0)) from employees;
```



## 视图

![image-20230930104507897](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309301045030.png)

> **为什么要使用视图**
>
> 视图是部分表而不是所有表，可以针对不同的用户制定不同的查询视图

> **视图的理解**
>
> - 视图是一种虚拟表，本身**不具有数据**的，占有很少的内存空间，它是SQL中的重要概念。
> - 视图建立在已有表的基础上，视图赖以建立的这些表称为**基表**。
> - 视图的创建和删除只影响视图本身，不影响对应的基表。但是当对视图中的数据进行增加，删除和修改操作时，数据表中的数据会相应地发生变化，反之亦然。
> - 向视图提供内容的语句为select语句，可以将视图理解为存储起来的select语句
>   - 在数据库中，视图不会保存数据，数据真正保存在数据表中。当对视图中的数据进行增加、删除和修改操作时，数据表中的数据会发生相应的变化；反之亦然
>   - 视图中不存在约束，主键，外键的影响
> - 视图，是向用户提供基表数据的另一种表现形式。通常情况下小型项目的数据库可以不使用视图，但是在大型项目中，以及数据表比较复杂的情况下，视图的价值就凸显出来了，他可以帮助我们把经常查询的结果集放到虚拟表中，提升使用效率，理解和使用起来都非常的方便。
> - 简化查询；控制数据的访问



> **创建视图**

- 在create view 语句中嵌入子查询

  ```MySQL
  create [or replace]
  [algorithm = {undefined | nerge | temptable}]
  view 视图名称 [(字段列表)]
  as  查询语句
  [with [cascade | local] check option]
  ```

- 精简版

  ```MySQL
  create view 视图名称
  as 查询语句
  ```

- 创建单表视图

  ```MySQL
  create view empuv80
  as 
  select empployee_id, last_name, salary
  from employess
  where department_id=80;
  
  # 查询视图
  select * from empuv80;
  
  --- 分割线
  create view emp_year_salary(ename, year_salary)
  as
  select ename, salary*12*(1+IFNULL(commission_pct, 0))
  from t_employee;
  
  
  create view salvu50
  as 
  select employee_id ID_NUMBER, last_name NAME, salary*12 ANN_SALARY
  from employees
  where department_id = 50;
  
  
  # 可以把数据复制过来，但是并不能复制约束以及主键
  create table depts2
  as 
  select ename, salary*12*(1+IFNULL(commission_pct, 0))
  from t_employee; 
  ```

  说明：

  - 在SQL语句的基础上封装了视图view，这样就会基于SQL语句的结果集形成一张虚拟表
  - 在创建视图时，如果视图名后面没有字段列表，则视图中字段列表默认和select语句中的字段列表一致。如果select语句中给字段列表取了别名，那么视图中的字段名和别名相同。

- 创建多表联合视图

  ```MySQL
  create view empview
  as
  select e.employee_id emp_id, e.last_name NAME, d.department_name 
  from employees e, departments d
  where e.department_id = d.department_id;
  
  create view emp_dept
  as
  select ename, dname
  from t_employee left join t_department
  on t_employee.did = t_department.did
  
  create view dept_sum_vu
  (name, minsal, maxsal, avgsal)
  as
  select d.department_name, min(e.salary), max(e.salary), avg(e.salary)
  from employees e, departments d
  where e.department_id = d.department_id
  group by d.department_name;
  ```

- 利用视图对数据进行格式化

  我们经常需要输出某个格式的内容，比如我们想输出员工姓名和对应的部门名称，对应的格式为emp_name(department_name)，就可以使用视图来完成数据格式化的操作：

  ```MySQL
  create view emp_depart
  as
  select concat(last_name, '(', department_name, ')') as emp_dept
  from employees e join departments d
  where e.department_id = d.department_id;
  ```

- 基于视图创建视图

  ```MySQL
  create view emp_dept_ysalary
  as
  select emp_dept.ename, emp_dept.dname, emp_year_salary.year_salsry
  from emp_dept inner join emp_year_salary
  on emp_dept.ename = emp_year_salary.ename;
  ```

- 查看视图

  语法1：查看数据库的表对象，视图对象

  ```MySQL
  show tables;
  ```

  语法2：查看视图的结构

  ```MySQL
  describe / desc 视图名称;
  ```

  语法3：查看视图的属性信息

  ```MySQL
  # 查看视图信息（显示数据表的存储引擎，版本，数据行数和数据大小等）
  show table status like '视图名称'\G
  ```

  ![image-20230930143851938](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309301438092.png)

  执行结果显示，注释Comment为view，说明该表为视图表，其它的信息为null，说明这是一个虚表。

  语法4：查看视图的详细定义信息

  ```MySQL
  show create view 视图名称;# 这个create并不会重新创建一个视图，而是会查看这个视图的创建过程，就跟用show查看表的创建过程一样
  ```

- 更新视图

  一般情况下，**可以直接向视图更新和删除数据，同时也会影响到基本表的更新和删除**；但是不能直接通过向视图插入数据的方式向基本表插入数据，可以通过下面的两种方式：

  - 插入数据时使用视图所依赖的表：如果视图是基于一个或多个表的查询结果，可以直接向这些表插入数据。视图会自动更新，反映出插入的数据。

    ```MySQL
    CREATE TABLE users (
      id INT PRIMARY KEY,
      name VARCHAR(50)
    );
    
    CREATE TABLE orders (
      id INT PRIMARY KEY,
      user_id INT,
      product VARCHAR(50),
      FOREIGN KEY (user_id) REFERENCES users(id)
    );
    -- 创建视图
    CREATE VIEW user_orders AS
    SELECT users.name, orders.product
    FROM users
    JOIN orders ON users.id = orders.user_id;
    -- 插入数据
    INSERT INTO users (id, name) VALUES (1, 'Alice');
    INSERT INTO orders (id, user_id, product) VALUES (1, 1, 'Product A');
    ```

  - 使用触发器向视图插入数据：如果向视图插入数据，可以通过在视图所依赖的表上创建触发器来实现。触发器是在表上执行的特殊操作，比如插入，更新或删除数据时触发。我们可以在触发器中定义向视图插入数据的逻辑。例如，创建一个触发器，当向users表插入数据时，自动向视图user_orders插入相应的数据：

    ```MySQL
    create trigger insert_user_orders after insert on users
    for each row
    insert into user_orders(name, product)
    values(NEW.name, null);
    ```

    这样当向users表插入数据时，视图user_orders会自动插入相应的数据。

  - 使用insert into select语句插入数据：如果想直接向视图中插入数据，可以使用insert into select 语句。该语句可以将查询结果插入到目标表中。由于视图是一个查询结果集，我们可以通过select语句查询视图的数据，并将结果插入到目标表中。例如，向视图user_orders中插入数据：

    ```MySQL
    insert into user_orders(name, product)
    select users.name, null
    from users;
    ```

    这样，视图user_orders会插入与users表中相同的数据。

- 不可更新的视图

  要使视图更新，视图中的行要和底层基本表中的行质检部必须存在**一对一**的关系。另外当视图定义出现如下情况时，试图不支持更新操作：

  - 在定义视图的时候指定了 “algorithm=temptable”，视图将不支持**insert**和**delete**操作；
  - 视图中不包含基本表中所有被定义为**非空又未指定默认值的列**，视图将不支持insert操作；
  - 在定义视图的select语句中使用了**join联合查询**，视图将不支持**insert**和**delete**操作；
  - 在定义视图的select语句后的字段列表中使用了数学表达式或子查询。视图将不支持insert，也不支持update使用了数学表达式，子查询的字段值；
  - 在定义视图的select语句后的字段列表中使用distinct，聚合函数，group by，having，union等，视图将不支持insert，update，delete
  - 在定义视图的select语句中包含了子查询，而子查询中引用了from后面的表，试图将不支持insert，update，delete；
  - 视图定义基于一个 `不可更新视图`
  - 常量视图

  虽然可以更新视图数据，但总的来说，视图作为虚拟表，主要用于方便查询，不建议更新视图的数据。对视图数据的更改，都是通过对实际数据表里数据的操作票来完成的。

- 修改视图

  ```MySQL
  # 方式一
  create or replace view v_emp1
  as
  select employee_id, last_name, salary
  from employees
  where salary>7000;
  
  # 方式二
  alter view v_emp1
  as
  select employee_id, last_name, salary
  from employees
  where salary>7000;
  ```

- 删除视图

  删除视图只会删除视图的定义，并不会删除基表的数据。

  ```MySQL
  drop view if exists 视图名称;
  drop view if exists 视图名称1, 视图名称2, ...
  ```

  说明：基于视图a，b创建了新的视图c，如果将视图a或者b删除，会导致视图c查询失败。这样的视图c需要手动删除或修改，否则影响使用。

- 视图的优缺点：

  ![image-20230930173009787](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309301730127.png)

  ![image-20230930173119850](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309301731975.png)



## 存储过程与函数

### 1. 理解

**含义：**存储过程英文是stored procedure。他的思想很简单，他的思想很简单，就是一组经过预先编译的SQL语句的封装。

执行过程：存储过程预先存储在MySQL服务器上，需要执行的时候，客户端只需要向服务器端发出调用存储过程的命令，服务端就可以把预先存储好的这一系列SQL语句全部执行。

**好处：**

1、简化操作，提高了sql语句的重用性，减少了开发程序员的压力；

2、减少操作过程中的失误，提高效率；

3、减少网络传输量（客户端不需要把所有的SQL语句通过网络发送给服务器端）

4、减少了SQL语句暴露在网上的风险，也提高了数据查询的安全性

**和视图、函数的对比：**

它和视图有着同样的优点，清晰，安全，还可以减少网络传输量。不过它和视图不同，视图是虚拟表，通常不对底层数据表直接操作，而存储过程是程序化的SQL，可以直接操作底层数据表，相比于面向集合的操作方式，能够实现一些更复杂的数据处理。

一旦存储过程被创建出来，使用它就像使用函数一样简单，我们直接通过调用存储过程名即可。相比较于函数，存储过程是没有返回值的。

### 2. 分类

存储过程的参数类型可以是 in，out和inout。根据这点分类如下：

1. 没有参数（无参数无返回）
2. 仅仅带in类型（有参数无返回）
3. 仅仅带out（无参数又返回）
4. 既带in又带out（有参数有返回）
5. 带inout（有参数有返回）

注意：in，out，inout都可以在一个存储过程中带多个。

### 3. 创建存储过程

#### 3.1 语法分析

```MySQL
create procedure 存储过程名(in|out|inout 参数名 参数类型, ...)
[characteristics ...]
begin
	存储过程体
end
```

说明：

1、参数前面的符号的意思

- in：当前参数为入参

  存储过程只是读取这个参数的值。如果没有定义参数种类，默认就是in，表示入参。

- out：当前参数为输出参数，也就是表示出参

  执行完成之后，调用这个存储过程的客端或者应用程序就可以读取这个参数返回的值了。

- inout：当前参数既可以为输入参数也可以为输出参数

2、形参类型可以是MySQL数据库中的任意类型。

3、characteristics：表示创建存储过程时指定的对存储过程的约束条件，其取值信息如下：

![image-20231001000217353](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010002263.png)

![image-20231001001213844](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010012941.png)

![image-20231001001254802](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010012876.png)

![image-20231001001425055](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010014139.png)

![image-20231001001459309](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010014385.png)

![image-20231001001759498](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010017716.png)



### 4 调用存储过程

![image-20231001001927899](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010019033.png)

![image-20231001002036460](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010020788.png)

![image-20231001012006271](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010120334.png)

![image-20231001012201371](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010122667.png)

![image-20231001012747824](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010127906.png)

![image-20231001013152990](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010131068.png)

![image-20231001013243687](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010132756.png)

![image-20231001013324721](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010133798.png)

![image-20231001013740999](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010137077.png)

![image-20231001013812126](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010138218.png)

![image-20231001013834346](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010138413.png)

![image-20231001014104334](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010141408.png)

![image-20231001014134389](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010141531.png)

![image-20231001014430462](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202310010144556.png)

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

- **脏读**：一个事务，读取到另一个事务中没有提交的数据

- **不可重复读（虚读）**：在同一个事务中，两次读到的数据不一样

- **幻读**：一个事务操作（DML）数据表中的所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改


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

  




























