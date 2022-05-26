[TOC]
# 把文件从一台linux上复制到另一台linux上——两种情况

## 情况一   当前机器---拷贝到--->远程机器

    scp  本地Linux系统文件路径  远程用户名@IP地址:远程系统文件绝对路径名

实例：

1、拷贝当前241这台机器 /usr/local/tools/ 文件夹下的所有文件 到 远程240那台机器的 /usr/local/tools 文件夹下

```python
scp /usr/local/tools/* root@192.168.0.240:/usr/local/tools
```
命令输入完毕，会要求输入“192.168.0.240”服务器root的密码，然后开始远程拷贝数据

```python
[root@nfs_client ~]# scp /usr/local/tools/* root@192.168.0.240:/usr/local/tools
root@192.168.0.240's password: 
mysql-community-client-5.7.22-1.el7.x86_64.rpm                                  100%   24MB  23.9MB/s   00:01    
mysql-community-common-5.7.22-1.el7.x86_64.rpm                                  100%  274KB   2.1MB/s   00:00    
mysql-community-libs-5.7.22-1.el7.x86_64.rpm                                    100% 2187KB  19.1MB/s   00:00    
mysql-community-server-5.7.22-1.el7.x86_64.rpm                                  100%  165MB   9.8MB/s   00:16    
[root@nfs_client ~]# 
```

2、拷贝当前241机器 /usr/local/tools/ 下某个具体的文件 到 远程240机器 /usr/local/tools  下

```linux
scp /usr/local/tools/xxx文件 root@192.168.0.240:/usr/local/tools
```
命令输入完毕，会要求输入“192.168.0.240”服务器root的密码，然后开始远程拷贝数据
```python
[root@nfs_client ~]# scp /usr/local/tools/mysql-community-server-5.7.22-1.el7.x86_64.rpm root@192.168.0.240:/usr/local/tools
root@192.168.0.240's password: 
mysql-community-server-5.7.22-1.el7.x86_64.rpm                                                             100%  165MB  55.0MB/s   00:03    
[root@nfs_client ~]# 
```

3、递归复制整个目录  加参数 -r
```python
scp -r apache-tomcat-8.5.31/ root@solr2:usr/local
```

4、进阶：批量赋值当前机器多个文件夹到远程机器上
```python
[root@solr1 configsets]# scp -r {products,scan_detail,storages,traces}/ root@121.56.166.188:/usr/local/solrconfig_data
```

## 情况二   远程机器---拷贝到--->当前机器

    scp  远程用户名@IP地址:文件的绝对路径  本地Linux系统路径

```python
[root@nfs_server tools]# scp root@192.168.0.241:/usr/local/tools/* /usr/local/tools
root@192.168.0.241's password: 
mysql-community-client-5.7.22-1.el7.x86_64.rpm                                                                                   100%   24MB  52.8MB/s   00:00    
mysql-community-common-5.7.22-1.el7.x86_64.rpm                                                                                   100%  274KB   8.2MB/s   00:00    
mysql-community-libs-5.7.22-1.el7.x86_64.rpm                                                                                     100% 2187KB  73.6MB/s   00:00    
mysql-community-server-5.7.22-1.el7.x86_64.rpm                                                                                   100%  165MB  36.3MB/s   00:04    
[root@nfs_server tools]# ls
mysql-community-client-5.7.22-1.el7.x86_64.rpm  mysql-community-libs-5.7.22-1.el7.x86_64.rpm
mysql-community-common-5.7.22-1.el7.x86_64.rpm  mysql-community-server-5.7.22-1.el7.x86_64.rpm
[root@nfs_server tools]# 
```

# 软链接
```go
ln -s 源文件 目标文件
例如 ln -s /opt/local/python /usr/local/bin/python
```