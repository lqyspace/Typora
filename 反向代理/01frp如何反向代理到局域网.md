[toc]

# 使用frp搭建内网穿透，实现本地服务公网访问

## 1、frp简介

frp是一款高性能的反向代理应用，专注于内网穿透。它支持多种协议，包括TCP、UDP、HTTP、HTTP等，并且具备P2P通信功能。使用frp，您可以安全、便捷地将内网服务暴露到公网，通过拥有公网ip的节点进行中转。

官网地址：[https://gofrp.org/zh-cn/docs/overview/](https://gofrp.org/zh-cn/docs/overview/)

## 2、工作原理

frp主要由两个组件组成：客户端（frpc）和服务端（frps）。通常情况下，服务端部署在具有公网ip的机器上，而客户端部署在需要穿透的内网服务所在的机器上。

由于内网服务缺乏公网ip地址，因此无法直接被非局域网内的用户直接访问。用户通过访问直接访问服务端的frps，frp负责根据请求的端口或其他信息将请求路由到相应的内网机器上，从而实现通信。

## 3、使用方法

frp搭建分为服务端（frps）和客户端（frpc），其中服务端需要搭建在有公网固定ip地址的机器上，客户端则搭建在需要被代理的内网机器上。

### 3.1、搭建服务端frps

【下载解压安装包】

安装包地址：[https://github.com/fatedier/frp/releases/tag/v0.61.0](https://github.com/fatedier/frp/releases/tag/v0.61.0)

下载对应服务器版本的压缩包。比如我使用的服务端机器是Ubuntu20，客户端是macbook。

![image-20241206231647254](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202412062316304.png)

下载后使用ftp工具上传到服务器或客户端，或者使用wget命令下载。

```shell
wget https://github.com/fatedier/frp/releases/download/v0.61.0/frp_0.61.0_linux_amd64.tar.gz
```

将压缩包解压，可以看到以下的文件：

```bash
tar zxvf frp_0.61.0_linux_amd64.tar.gz
```

![image-20241206232514559](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202412062325585.png)

【修改配置文件frps.toml】

```toml
bindPort = 7000

# auth 配置服务端与客户端访问的token
auth.token = "1234321"

# web-ui 开启web界面监控
# 默认为 127.0.0.1 如果需要公网访问，需要修改为 0.0.0.0
webServer.addr = "0.0.0.0"
webServer.port = 7500
# dashboard 用户名密码，可选，默认为空
webServer.user = "admin"
webServer.password = "admin"
```

> [!tip]
>
> 需要配置防火墙打开 7000,7500端口，否则不能访问
>
> 更多配置：[frp说明文档](https://gofrp.org/zh-cn/docs/features/common/configure/)

【启动服务】

```bash
./frps -c ./frps.toml
```

出现以下信息表示frp服务器端启动成功。

![image-20241206234308706](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202412062343733.png)

浏览器打开`http://your ip:7500`可以进入web监控界面，输入设置的账号密码既可查看出每个代理的出入网流量。

![image-20241206234543928](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202412062345954.png)

> [!note]
>
> **注意：**此处启动的方式为前台启动，退出ssh链接或关闭控制台会退出服务
>
> 可以使用systemd设置为后台服务进行管理，配置方式：[https://gofrp.org/zh-cn/docs/setup/systemd/](https://gofrp.org/zh-cn/docs/setup/systemd/)

### 3.2、搭建客户端frpc

【下载解压安装包】

```bash
wget https://github.com/fatedier/frp/releases/download/v0.61.0/frp_0.61.0_darwin_arm64.tar.gz

tar zxvf frp_0.61.0_darwin_arm64.tar.gz
```

【修改配置文件】

```bash
# 公网服务ip地址
serverAddr = "xxx.xxx.xxx.xxx"
serverPort = 7000

# auth访问公网的服务端的token，服务端与客户端需要保持一致
auth.token = "1234321"

[[proxies]]
name = "tcp-demo-1"
type = "tcp"
# 内网服务器地址
localIP = "127.0.0.1"
# 内网服务端口
localPort = 6666
# 公网服务器代理端口
remotePort = 6666
```

> [!tip]
>
> 此处配置的 `remotePort` 需要在公网服务器防火墙上配置端口开放，否则不能访问；
>
> 更多的配置信息请查看官方文档：[https://gofrp.org/zh-cn/docs/features/common/configure/](https://gofrp.org/zh-cn/docs/features/common/configure/)

【启动服务】

```bash
./frpc -c ./frpc.toml
```

![image-20241207000559865](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202412070005902.png)

参考：[https://juejin.cn/post/7357994849386168372](https://juejin.cn/post/7357994849386168372)
