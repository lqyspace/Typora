# X-ui，支持多协议多用户的 Xray 面板！V2-ui全面停止更新，后续逐渐用 X-ui 面板取代 V2-ui 面板！

## 前言

sprov 这位大神开发的这套面板程序，很是方便，可以可视化的搭建SS、V2ray、Xray、Trojan等热门的协议，并且可以实时看到 VPS 的性能状态以及流量的使用情况。

那在经过将近两年的不断更新之后，V2-ui面板，迎来了一个比较大的转折——停止更新了。

sprov 大神又用 GO 语言重新开发了一套面板 X-UI。那这套面板相比原来的 V2-ui，兼容性更强，也便于维护， GO 语言的性能更好，而且内存占用也会相对的低一些。

## 视频观看

[![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202210130016883.png)](https://v2rayssr.com/go?url=https://youtu.be/6ztPETEiY8M)

## 功能介绍

- 系统状态监控
- 支持多用户多协议，网页可视化操作
- 支持的协议：vmess、vless、trojan、shadowsocks、dokodemo-door、socks、http
- 支持配置更多传输配置
- 流量统计，**限制流量**，**限制到期时间**
- 可自定义 xray 配置模板
- 支持 https 访问面板（自备域名 + ssl 证书）
- 更多高级配置项，详见该项目的 GitHub：[点击访问](https://v2rayssr.com/go?url=https://github.com/sprov065/x-ui)

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202210130016866.png)

## 准备工作

1、VPS 一台重置好主流的操作系统 （CentOS 7+、Ubuntu 16+、Debian 8+），作者使用：[搬瓦工 VPS](https://v2rayssr.com/go?url=https://www.v2rayssr.com/bwg.html)

2、域名一个，做好相关的解析，若是需要套用 CDN，请托管域名到 cloudflare ，[不会请点击](https://v2rayssr.com/go?url=https://www.v2rayssr.com/yumingreg.html)

## 安装 X-ui 面板

### 申请 SSL 证书

下面环境的安装方式，大家根据自己的系统选择命令安装就好了。

#### 更新及安装组件

```
apt update -y                # Debian/Ubuntu 命令
apt install -y curl socat    #Debian/Ubuntu 命令

yum update -y               #CentOS 命令
yum install -y curl socat   #CentOS 命令
```

#### 安装 Acme 脚本

```
curl https://get.acme.sh | sh
```

#### 80 端口空闲的证书申请方式

自行更换代码中的域名、邮箱为你解析的域名及邮箱

```
~/.acme.sh/acme.sh --register-account -m xxxx@xxxx.com
~/.acme.sh/acme.sh  --issue -d mydomain.com   --standalone
```

#### 安装证书到指定文件夹

自行更换代码中的域名为你解析的域名

```
~/.acme.sh/acme.sh --installcert -d mydomain.com --key-file /root/private.key --fullchain-file /root/cert.crt
```

### 安装 & 升级 X-ui 面板

> **请注意！！！！！**
>
> 以下的两个安装命令：
>
> 1 为官方原版，但是2021年8月就没有更新了。
>
> 2 为改版X-ui，更新速度值得点赞，加入了很多的新功能，包括最新的 Reality 协议、电报群通知等等功能。
>
> 大家自由选择，二选一，都可以正常的使用

#### 1. 官方原版安装及升级的一键代码

```
bash <(curl -Ls https://raw.githubusercontent.com/vaxilu/x-ui/master/install.sh)
```

#### 2. 改版X-ui

项目地址：[项目地址](https://v2rayssr.com/go?url=https://github.com/FranzKafkaYu/x-ui)

```linux
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install.sh)
```

安装成功，浏览器打开X-UI面板：域名端口改成你的

https://xxx.xxx.xxx.xxx:54321/xui/inbounds

证书路径即上面填写的这个

```
/root/private.key –fullchain-file /root/cert.crt
/root/cert.crt
/root/private.key
```

本文链接：[https://v2rayssr.com/x-ui.html](https://v2rayssr.com/x-ui.html)

