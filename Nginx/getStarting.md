# Nginx简介

![image-20220520172222263](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205201722376.png)

# Nginx版本

> **常用版本分为四大阵营**

- [Nginx开源版本](http://nginx.org/)：Nginx最原始的版本，网站服务器，代理服务器，负载均衡服务器，功能比较少，基本的功能都支持，二次开发的难度比较大
- [Nginx plus商业版](https://www.nginx.com/)：功能强大，高并发，微服务整合
- [Openresty](https://openresty.org/cn/)：免费开源，主要是Nginx与LuaJIT脚本整合，开发的难度不是很大（推荐）
- [Tengine](https://tengine.taobao.org/)：性能高，稳定，集群，负载均衡



# Nginx开源版本安装

> 安装环境

```
Ubuntu20.04.4 桌面版
```

> 获取压缩包文件

```go
wget http://nginx.org/download/nginx-1.21.6.tar.gz
```

> 解压缩

```go
tar -zxvf nginx-1.21.6.tar.gz -C /usr/local/src # 解压缩到指定文件夹/usr/local/src
```

> 重命名

```go
mv nginx-1.21.6.tar.gz nginx
```

> 将nginx安装到 /usr/local/nginx 目录下

```go
# 创建文件夹
mkdir /usr/local/nginx
# 进入到解压nginx的目录
cd /usr/local/src/nginx
# 显示nginx下的文件
ls /usr/local/src/nginx
# 进行安装，一定在 nginx 解压的目录 (/usr/local/src/nginx)
./configure --prefix=/usr/local/nginx
# 编译
make
# 编译安装
make install 
```

> 编译的时候报错
>
> ./configure: error: C compiler cc is not found
>
> 说明缺少编译语言

```
apt install -y gcc
```

> 继续运行：./configure --prefix=/usr/local/nginx

> 报错：./configure: error: the HTTP rewrite module requires the PCRE library.
>
> 继续安装依赖

```
apt-get install libpcre3-dev -y
```



> 继续运行：./configure --prefix=/usr/local/nginx

> 报错：./configure: error: the HTTP gzip module requires the zlib library.
>
> 安装依赖：

```
apt install -y zlib1g-dev
```

> 继续运行：./configure --prefix=/usr/local/nginx

> 报错：缺少 openssl包

````
apt install openssl libssl-dev
````

> 然后执行：开启ssl模块

```
./configure --prefix=/usr/local/nginx --with-http_ssl_module
```

如果想启用 ”server + status" 页，执行 ：

```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module
```

如果两个都启用：

```
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

---

那么configure就通过了

接下来可以执行:

```
make
make install
```

如果发现：找不到make命令，请安装：

```
apt install make -y
```

然后继续执行：

```
make 
make install 
```

至此，nginx安装成功。

> 查询安装是否成功

![image-20220521224641649](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205212246955.png)



> conf：配置文件； html：静态文件；logs：日志文件；sbin：命令（启动、停止）

> 配置环境变量

```
vi ~/.bashrc

export NGINX_HOME=/usr/local/nginx
export PATH=$PATH:$NGINX_HOME/sbin
```

> 设置开机自启

在系统服务目录里面创建 nginx.service 文件

```
vi /usr/lib/systemd/system/nginx.service
```

写入以下内容：

```
[Unit]
Description=nginx
After=network.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
ExecStop=/usr/local/nginx/sbin/nginx -s stop
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

重新加载系统配置：

```
systemctl daemon-reload
```





设置为开机自启：

```
systemctl enable nginx.service
```

```
reboot 
```

```
systemctl status nginx
systemctl start nginx
systemctl restart nginx
systemctl reload nginx
```



# Nginx 的目录结构

- conf：配置文件，主要配置文件是 nginx.conf
- html：静态资源、网页，默认的网站根目录
- logs：access.log 访问日志、error.log 发生错误似的日志、nginx.pid 记录主进程的ID号
- sbin：命令（启动、停止）

![image-20220522162118710](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205221621824.png)

**说明**：

1、Nginx会启动多个进程，第一个是主进程 master，主进程会检查nginx.conf是否存在问题。启动成功之后会创建一个子进程，主进程主要是用来协调子进程。

2、用户的请求接入到系统当中之后会由work子进程响应及处理。

3、master 主进程fork出来 多个子进程来处理请求。



# Nginx配置文件

## 最小配置

```
# 开启几个主进程,基本的对应是 1cpu内核对应1个worker_processes
worker_processes 1;

# 事件驱动模块：每个worker进程默认可以创建多少个链接
events {
	worker_connections 1024;
}

# 
http {
	# 请求头，会标明当前返回或发送的文件是什么类型的，比如文本文档、html文档等，比如浏览器发送这个头，来明确告诉客服端发送的是一个什么文件，主要是服务器端来定义
	include mime.types; 
	
	# 默认的请求头的文件类型
	default_type application/octet-stream;
	
	# 数据零拷贝：免除中间拷贝过程，相当于直接通过无线直接传输，省去了中间复制，调度的过程。
	sendfile on;
}
```

**无sendfiie的模式：**

![image-20220522155919332](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205221559455.png)



假设客户端发送了一个请求，nginx会将服务器中的文件复制到应用程序，然后应用程序在通过网络接口程序，将文件返回给客户端。这是 read/write 过程。



**有 sendfile 的模式**：

![image-20220522160926249](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205221609376.png)

客户端发送这个请求给服务器，通过Nginx可以向操作系统的内核发送一个信号，操作系统内核就可以把用户请求发送到Nginx，应用内存不去加载磁盘上的文件，直接推送一个文件sendfile给网络接口，使网络接口来读取这个文件，读到这个文件之后通过网络就发给用户了。这中间减少了一次数据拷贝的过程。



```
# 保持链接超时的时间
keepalive_timeout 65;

# 虚拟主机：一个server代表一个主机，代表一个站点，每个主机之间互相不干扰，使用端口号区分不同主机的不同
server {
	listen 80; # 监听的端口号
	server_name localhost; # 域名、或者主机名
	
	# 域名后面跟的子目录url：http://www.baidu.com/xxxx/index.html
	location / {
		# 相对路径，html是一个文件夹
		root html;
		index index.html index.htm; # 默认访问的首页
	}
	
	# 服务器端发生错误时：500,502，503,504错误码时，转向 /50x.html
	error_page 500 502 503 504 /50x.html;
	location = /50x.html {
		root html; # 当发生错误码时，让它在html的目录下找 50x.html
	}
}
```











