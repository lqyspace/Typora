# 免费解锁奈飞 (Netflix)！WARP 添加 IPv6，配合 V2-ui （Xray面板）进行奈飞 (Netflix)非自制剧全解锁！

## 前言

Netflix —— 大家也并不是很陌生，美国奈飞公司，我们经常简称它为网飞或是奈飞。它是一家会员订阅制的流媒体播放平台，成立于1997年，总部位于美国的加利福尼亚州。经常看奈飞的人，应该知道下面的五个概念：

`不提供服务` – 所在的地区奈飞没开通，连自制剧也看不了

`宽松版权` – 有些奈飞拍摄的影片不是特别注重版权，所以限制放的很开

`解锁自制剧` – 代表可以看由奈飞自己拍摄的影片

`解锁非自制剧` – 代表可以看奈飞买下的第三方版权影片

`地域解锁` – 奈飞在不同的地区可以看的片源都是不同的，有些影片只能在特定区观看

## 视频教程

[![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202210130024351.png)](https://v2rayssr.com/go?url=https://youtu.be/-TVlCKvOFT4)

## 准备工作

1、VPS 一台，重置主流的操作系统（本期视频使用 Debian 的系统进行演示）

2、域名一个，做好相应的解析（用于搭建 Xray 节点）——[不会点击这里](https://v2rayssr.com/go?url=https://www.v2rayssr.com/yumingreg.html)

3、奈飞会员账号一个（[可以点击这里合租一个](https://v2rayssr.com/go?url=https://nf.run/gTXfsQ)）

[![img](https://v2rayssr.com/indexpic/320_316.png)](https://v2rayssr.com/go?url=https://nf.run/gTXfsQ)

## Netflix 解锁检测脚本

运行以下代码，即可查询自己的 VPS 是否支持奈飞全解锁或是半解锁！

> 奈飞 (Netflix) 解锁，分为自制剧解锁、非自制剧解锁、或是直接打不开奈飞 (Netflix) 页面。一般来说，电影上面有 N 符号，或是电影开始有 Netflix 标识的可认定为奈飞(Netflix) 的自制剧，其他的片源为奈飞(Netflix) 的非自制剧。非自制剧 ≥ 自制剧 ＞ 看不了 … … 一般情况下，解锁了奈飞 (Netflix) 的非自制剧，所有的电影也就都能看了（当然，奈飞(Netflix) 的片源是分区的，解锁相应的地区就会有相应的电影）

```
wget -O nf https://github.com/sjlleo/netflix-verify/releases/download/2.01/nf_2.01_linux_amd64 && chmod +x nf && clear && ./nf
```

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202210130024235.png)

## 安装 WireGuard

### 添加 back­ports 源

```
apt install curl lsb-release -y

echo "deb http://deb.debian.org/debian $(lsb_release -sc)-backports main" | tee /etc/apt/sources.list.d/backports.list

apt update
```

### 安装网络工具包

```
apt install net-tools iproute2 openresolv dnsutils -y
```

### 安装 wireguard-tools

```
apt install wireguard-tools --no-install-recommends
```

先执行 `uname -r` 命令查看内核版本。如果是 5.6 以上内核则已经集成了 Wire­Guard ，就不需要安装了。如果不是，执行下面的命令

```
apt -t $(lsb_release -sc)-backports install linux-image-$(dpkg --print-architecture) linux-headers-$(dpkg --print-architecture) --install-recommends -y

reboot     # 重启
```

### 生成 WireGuard 配置文件

wgcf 是 Cloud­flare WARP 的非官方 CLI 工具，它可以模拟 WARP 客户端注册账号，并生成通用的 Wire­Guard 配置文件。

```
curl -fsSL git.io/wgcf.sh | bash    #  安装 wgcf
wgcf register                       #  注册 WARP 账户 (生成 /root/wgcf-account.toml 文件保存账户信息)
wgcf generate                       #  生成 /root/wgcf-profile.conf 配置文件
```

### 编辑 WireGuard 配置文件

编辑 `/root/wgcf-profile.conf` 文件，其中可以在服务器端解析 engage.cloudflareclient.com 的 ip

```
nslookup engage.cloudflareclient.com
```

解析的结果为 `162.159.192.1`

将配置文件中的 `engage.cloudflareclient.com` 替换为 `162.159.192.1` ，并删除 `AllowedIPs = 0.0.0.0/0` 。

即配置文件中 `[Peer]` 部分为：

```
[Peer]
PublicKey = bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo=
AllowedIPs = ::/0
Endpoint = 162.159.192.1:2408
```

![img](https://www.v2rayssr.com/wp-content/uploads/2021/07/截屏2021-07-06-12.42.52.png)

> **若你的 VPS 只有 IPv6，请看这里：**
>
> 下面详细说了 IPv4 以及 IPv6 分别应该如何去做。
>
> 只有 IPv4 的服务器添加 WARP IPv6 网络支持
> 将配置文件中的 engage.cloudflareclient.com 替换为 162.159.192.1，并删除 AllowedIPs = 0.0.0.0/0。即配置文件中 [Peer] 部分为：
>
> ```
> [Peer] 
> PublicKey = bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo= 
> AllowedIPs = ::/0 
> Endpoint = 162.159.192.1:2408
> ```
>
> 原理：AllowedIPs = ::/0参数使得 IPv6 的流量均被 Wire­Guard 接管，让 IPv6 的流量通过 WARP IPv4 节点以 NAT 的方式访问外部 IPv6 网络。
> 此外配置文件中默认的 DNS 是 1.1.1.1，博主实测其延迟虽然很低，但解析结果并不理想。由于它将替换掉系统中的 DNS 设置 (/etc/resolv.conf)，建议小伙伴们请根据实际情况来进行替换，或者直接删除 DNS 这行。以下配置供参考：
>
> ```
> DNS = 8.8.8.8,8.8.4.4,2001:4860:4860::8888,2001:4860:4860::8844
> ```
>
> 只有 IPv6 的服务器添加 WARP IPv4 网络支持
>
> 将配置文件中的 engage.cloudflareclient.com 替换为 [2606:4700:d0::a29f:c001]，并删除 AllowedIPs = ::/0。即配置文件中 [Peer] 部分为：
>
> ```
> [Peer] 
> PublicKey = bmXOC+F1FxEMF9dyiK2H5/1SUtzH0JuVo51h2wPfgyo= 
> AllowedIPs = 0.0.0.0/0 
> Endpoint = [2606:4700:d0::a29f:c001]:2408
> ```
>
> 原理：AllowedIPs = 0.0.0.0/0参数使得 IPv4 的流量均被 Wire­Guard 接管，让 IPv4 的流量通过 WARP IPv6 节点以 NAT 的方式访问外部 IPv4 网络。
> 此外配置文件中默认的 DNS 是 1.1.1.1，由于是 IPv4 地址，故查询请求会经由 WARP 节点发出。由于它将替换掉系统中的 DNS 设置 (/etc/resolv.conf)，为了防止当节点发生故障时 DNS 请求无法发出，建议替换为 IPv6 地址的 DNS 优先，或者直接删除 DNS 这行。以下配置供参考：
>
> ```
> DNS = 2001:4860:4860::8888,2001:4860:4860::8844,8.8.8.8,8.8.4.4
> ```

### 测试 WireGuard 网络接口

将 Wire­Guard 配置文件复制到 `/etc/wireguard/` 并命名为 `wgcf.conf`。

```
cp wgcf-profile.conf /etc/wireguard/wgcf.conf
```

开启网络接口（命令中的 wgcf 对应的是配置文件 wgcf.conf 的文件名前缀）。

```
wg-quick up wgcf
```

执行以下命令检查是否连通。同时也能看到正在使用的是 Cloud­flare 的网络。

```
curl -6 ip.p3terx.com
```

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202210130024211.png)
测试完成后关闭相关接口，因为这样配置只是临时性的

```
wg-quick down wgcf
```

### 启用 Wire­Guard 网络接口

```
systemctl start wg-quick@wgcf   # 启用守护进程
systemctl enable wg-quick@wgcf  # 设置开机启动
```

## 安装 Xray 面板

下面是安装 V2-UI 面板的官方命令

```
bash <(curl -Ls https://blog.sprov.xyz/v2-ui.sh)
```

### 修改 V2-UI 配置

以下的配置是视频中说用的配置文件，大家可以照搬，或是自行修改。

```
{  
	"api": {    
		"services": [      
			"HandlerService",      
			"LoggerService",      
			"StatsService"    
		],    
		"tag": "api"  
	},  
	"inbounds": [    
		{      
			"listen": "127.0.0.1",      
			"port": 62789,      
			"protocol": "dokodemo-door",      
			"settings": {        
				"address": "127.0.0.1"      
			},      
			"tag": "api"    
		}  
	],  
	"outbounds": [      
		{      
			"protocol": "freedom",      
			"settings": {         
				"domainStrategy": "UseIPv6"                
			},      
			"tag": "IP-V6"    
		},    
		{      
			"protocol": "blackhole",      
			"settings": {},      
			"tag": "blocked"    
		}  
	],  
	"policy": {    
		"system": {      
			"statsInboundDownlink": true,      
			"statsInboundUplink": true    
		}  
	},  
	"routing": {    
		"rules": [      
			{        
				"type":"field",        
				"domain": [          
					"geosite:netflix"        
				],        
				"inboundTag":  [          
					"all-in"         
				],        
				"outboundTag": "IP-V6"      
			},      
			{        
				"inboundTag": [          
				"api"        
				],        
				"outboundTag": "api",        
				"type": "field"      
			},      
			{        
				"ip": [          
					"geoip:private"        
					],        
				"outboundTag": "blocked",        
				"type": "field"      
				},      
				{        
					"outboundTag": "blocked",        
					"protocol": [          
						"bittorrent"        
						],        
					"type": "field"      
					}    
				]  
			},  
			"stats": {}
		}
```

若你是自建的 Xray 节点，不是用面板搭建的，可以通过修改 Xray 的配置文件，来进行 IPv6 的出口指定和路由设置。

在 VPS 目录 `/usr/local/etc/xray` 找到 `config.json` 文件，替换整个 `outbounds` ，请注意 `"outbounds"` 后面的标点符号 。

比如：找到如下图所示的地方，选中后，全部粘贴就好了。

![img](https://www.v2rayssr.com/wp-content/uploads/2021/07/截屏2021-07-06-13.12.43.png)

```
 "outbounds": [    
 	{      
 		"tag":"IP4_out",      
 		"protocol": "freedom",      
 		"settings": {}    
 	},    
 	{      
 		"tag":"IP6_out",      
 		"protocol": "freedom",      
 		"settings": {        
 			"domainStrategy": "UseIPv6"      
 		}    
 	}  
 ],  
 "routing": {    
 	"rules": [      
 		{        
 			"type": "field",        
 			"outboundTag": "IP6_out",        
 			"domain": ["geosite:netflix"] // netflix 走 IPv6，可以多写几个，比如 "domain": ["geosite:netflix","geosite:google"]       
 		},      
 		{        
 			"type": "field",        
 			"outboundTag": "IP4_out",        
 			"network": "udp,tcp"      
 		}    
 	]  
 }
}
```

### 申请 SSL 证书

因为 Acme 脚本更新 `Please update your account with an email address first.` ，目前需要申请一个账号才可以申请，所以原先的命令做了下面的更新，更改下面的域名以及邮箱为你自己的。具体更多的最新 ACME 脚本申请证书请 [点击这里](https://v2rayssr.com/go?url=https://www.v2rayssr.com/ssl.html)

```
apt install socat -y
curl https://get.acme.sh | sh
~/.acme.sh/acme.sh --register-account -m xxxxxx@gmail.com
~/.acme.sh/acme.sh --issue -d test.bozai1.xyz --standalone
~/.acme.sh/acme.sh --installcert -d test.bozai1.xyz --key-file /root/private.key --fullchain-file /root/cert.crt
```

### 设置 V2-UI 账号

设置节点，很简单，这里不多做介绍，若是不明白，请观看 [本期视频](https://v2rayssr.com/go?url=https://youtu.be/-TVlCKvOFT4) ！

## 后记

通过 Qv2ray 软件的日志功能，我们可以明显的发现，我们现在访问奈飞的时候，用的是 IPV6 的方式，如下图：

![img](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202210130024432.png)

若是 CentOS 系统，请参考这篇文章：[点击访问](https://v2rayssr.com/go?url=https://www.zhangjun.sh.cn/linux/centos_wgcf_cf_ipv6.html)

复查其他线路有没有走 IPv6，[点击检测你的线路](https://v2rayssr.com/go?url=https://test-ipv6.com/index.html.zh_CN)

本期参考资料：[点击访问](https://v2rayssr.com/go?url=https://www.vjsun.com/685.html)



本文链接：[https://v2rayssr.com/warp-netflix.html](https://v2rayssr.com/warp-netflix.html)