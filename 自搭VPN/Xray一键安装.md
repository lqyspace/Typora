# Xray梯子搭建一键安装

## 更改时差

## 执行一下命令

```go
wget -N --no-check-certificate "https://raw.githubusercontent.com/mack-a/v2ray-agent/master/install.sh" && chmod 700 /root/install.sh && /root/install.sh
```
脚本长这样：

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261130969.png)

依次选择 2.任意组合安装–>1.Xray-core–>0.VLESS+TLS/XTLS+TCP


之后按照提示输入之前解析好的域名，依照提示往下进行就好了。

若中途提示TLS证书申请出现问题,第一步检查server是不是Debian 9，之后检查域名域名，不要用汉字域名。

安装好之后，浏览器打开自己的域名，可以看到伪装网站，大概长这样：

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261129092.png)

要是到这一步，打不开伪装网站的，就不要继续进行了，重建VPS重新跑脚本吧。

之后安装BBR加速，会让我们科学上网速度快很多，重新再跑一次脚本：

```go
wget -N --no-check-certificate "https://raw.githubusercontent.com/mack-a/v2ray-agent/master/install.sh" && chmod 700 /root/install.sh && /root/install.sh
```

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261129104.png)

选13–>1

之后会自动安装BBR，安装好之后再选11，开启BBR：

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261130219.png)

开启后，输入reboot，重启server使BBR生效：

```go
reboot
```

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261129102.png)

再跑一次脚本，输入3，1，复制记录下输出信息:

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261129146.png)



# 相关教程

## [X-ui，支持多协议多用户的 Xray 面板！V2-ui全面停止更新，后续逐渐用 X-ui 面板取代 V2-ui 面板！](https://www.v2rayssr.com/x-ui.html)