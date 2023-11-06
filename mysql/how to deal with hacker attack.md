[TOC]

# 在Ubuntu上安装Mariadb以及初步设置

> 更新系统

```linux
apt update -y
apt upgrade -y
```

> 安装

```linux
apt install mariadb-server
```

安装完了以后查看状态

```linux
service mariadb status
或
systemctl status mariadb
```

> 设置root用户的登录密码

```mysql
mysql_secure_installation
```

接下来一步步的执行。

> 登录

```
mysql -uroot -p
```



# 如何预防黑客攻击



## 服务器需要做以下调整

- 服务器管理员的密码具备一定的复杂度，建议使用字母，数字，字符组合的密码。
- 服务器关闭远征访问（或关闭外网的远程访问），调整远程访问默认的端口号。
- mysql的root用户密码具备一定的复杂度，建议使用字母，数字，字符组合的密码。
- mysql不允许任意远程端链接，建议仅对协同系统所在的ip地址开放链接。
- mysql不使用默认的3306端口，建议修改为其他的端口号。（mysql的配置文件：windows为my.ini，Linux为my.cnf，修改端口号）
- MySQL设置定时备份，并将数据包备份在异地（或异机）



## mariadb数据库修改root密码

> 方法1

直接在shell命令行里使用mysqladmin 命令修改

```mysql
mysqladmin -uroot -poldpassword password newpassword
```

这种放大的弊端在于会明文显示密码。

> 方法2

登录数据库修改密码。

```mysql
mysql -uroot -p
```

2.1 更新mysql库中的user表的字段。

```mysql
use musql;
update user set password=password('newpassword') where user='root';
flush privileges;
exit;
```

2.2 或者使用set指令设置root密码：

```mysql
set password for 'root' @ 'localhost' =password('newpassword');
exit;
```

2.3 如果是忘记了密码，或者被恶意窜改了密码，则需要跳过授权模式启动mariadb来修改密码。

先停掉服务：

```nginx
systemctl stop mariadb
```

如果关不掉，只能杀进程了：

查看mariadb的进程：

```nginx
ps -ef | grep mariadb
```

关闭进程：

```nginx
kill -9 51154
```

跳过密码进入：

```mysql
mysql_safe --skip-grant-tables &
```

```mysql
use mysql;
```

```mysql
update user set password=password(‘123’) where user=‘root’;
```

```mysql
flush privileges;
```

如果是mysql8.0，则需要使用以下方式：

```mysql
flush privileges;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
或者直接：
ALTER USER 'root'@'localhost' IDENTIFIED BY '你的密码';
flush privileges;
exit; #退出
```

或者

```mysql
show databases;
use mysql;
update user set authentication_string=PASSWORD("你的密码") where user='root';
update user set plugin="mysql_native_password";
flush privileges;
exit;
```

杀死进程：

```linux
kill -9 166404
```

```
systemctl restart mariadb
```

解决mariadb免密码登录：

```mysql
alter user 'root'@'localhost' IDENTIFIED BY 'password';
```

## 配置远程登录

有的时候，配置文件那么多，如何查找我们需要的配置信息，此时可以使用grep在 /etc/mysql 目录的所有文件中递归查找，看哪个文件中含有这个字符串

我们输入：

```mysql
grep -rn 'bind-address'
```

此时我们注销掉 "bind-address"即可。

mysql（mariadb）通过远程访问，需要在数据库中配置用来远程访问的账号、密码、IP地址
通过命令：

```mysql
grant all privileges on *.* to 'remoteuser'@'123.123.123.%' identified by 'password' with grant option;
flush privileges;
```

或者

```mysql
grant all privileges on *.* to 'remoteuser'@'%' identified by 'password' with grant option;
flush privileges;
```

使用sql查询命令可以查询更改：

```mysql
select host, user from mysql.user;
```



使用远程连接的命令行：

```mysql
mysql -h 192.168.0.201 -P 3306 -u root -p pwd
```



# 数据备份

## 物理备份

参考：[实现MySQL数据自动备份](https://blog.csdn.net/qfxietian/article/details/121211970)

1、创建备份目录

```
mkdir /home/backup
```

2、创建备份shell脚本

```
vi bkdatabase.sh
```

输入以下内容

```mysql
#!/bin/bash
mysqldump -uusername -ppassword DatabaseName > /home/backup/DatabaseName_$(date +%Y%m%d_%H%M%S).sql
# date 后面的空格不能省
```

对备份进行压缩

```mysql
#!/bin/bash
mysqldump -uusername -ppassword DatabaseName | gzip > /home/backup/DatabaseName_$(date +%Y%m%d_%H%M%S).sql.gz
```

注意：

把 username 替换为实际的用户名；

把 password 替换为实际的密码；

把 DatabaseName 替换为实际的数据库名；

3、添加可执行权限

```linux
chmod u+x bkDatabaseName.sh
```

添加可执行权限之后先执行一下，看看脚本有没有错误，能不能正常使用

```shell
./bkDatabaseName.sh
```

4、添加定时执行计划

```
crontab -e
```

```
/1 * * * * /home/backup/bkDatabaseName.sh
```

每一分钟执行一次shell脚本“/home/backup/bkDatabaseName.sh”

如果任务执行失败了，可以通过以下命令查看任务日志：

```
# tail -f /var/log/cron
```



### 报错情况

> 情况1

在mysql5.5中，增加了performance_schema，当我们进行mysqldump的时候，会报如下错误信息：


mysqldump: Got error: 1142: SELECT,LOCK TABL command denied to user 'root'@'localhost' for table 'cond_instances' when using LOCK TABLES


我们可以mysqldump中加上参数 --skip-lock-tables 如下实例

mysqldump  -uroot  -p   --skip-lock-tables  performance_schema > performance_schema.sql

原文链接：https://blog.csdn.net/aeolus_pu/article/details/8996300

> 情况2

#### mysqldump 导出的sql语句，导入到数据库时错误不完整怎么处理

##### 原因

过排查，后发现是出现的两次错误提示，都是是发生在 **insert** 语句中，并且该语句**特别特别**长。

##### 解决方法

```mysql
mysqldump --extended-insert=FALSE --complete-insert=TRUE dbname > sql.sql
```

执行mysqldum时候，通过添加 **--extended-insert=FALSE --complete-insert=TRUE** 参数，导出的sql语句中, insert 一条一条的语句，只导出一条一条的insert数据。

原文链接：[https://www.liuhaolin.com/mysql/1132.html](https://www.liuhaolin.com/mysql/1132.html)

## binlog备份

在配置文件中添加配置：

```
log-bin = /Users/caoxl/WWW/mysqlbinlog/mysql-binlog
binlog_format = row
```

详情参考：

[MySQL通过binlog恢复数据](https://blog.caoxl.com/2021/03/24/MySQL-Restore-By-Binlog/)

mariadb自动备份请参考：[mariadb自动备份](https://www.laoyuyu.me/2019/04/04/mysql/mariadb_auto_backup/)