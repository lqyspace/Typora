[TOC]

# 自建梯子教程 --Trojan 版本

<br>

原文：
[自建梯子教程 --Trojan版本 \| trojan-tutor](https://trojan-tutor.github.io/2019/04/10/p41.html)[需翻墙]

## 综述

### 本文简介

本文总结了自己在VPS搭建 [Trojan-GFW](https://github.com/trojan-gfw/trojan) 代理过程中遇到的各种坑，以及最后的解决方案，以供大家参考。

本文的宗旨在于，将大量重复性的工作集中到配置过程中，以让使用过程尽量简单。所以本文的配置过程相较于网上的某些教程稍微复杂一点，但是如果严格按照本文配置过程配置的话，那么配置完成之后服务器端就可以几乎不用搭理他了，然后客户端几乎是拿到手就可以使用，特别适合有好几个人一起共享的情况。

本文将按操作顺序介绍部署用于trojan代理服务的VPS服务器的详细过程，按照步骤操作一切正常的话一小时之内即可配置成功。如果配置过程中有什么疑问，欢迎在留言区交流！

### Trojan工作原理解析

Trojan是一个比较新的翻墙软件，在设计时采用了更适应国情的思路。在穿透GFW时，人们认为强加密和随机混淆可能会欺骗GFW的过滤机制。然而，Trojan实现了这个思路的反面：它模仿了互联网上最常见的HTTPS协议，以诱骗GFW认为它就是HTTPS，从而不被识别。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261130850.png)

如图所示，Trojan工作在443端口，并且处理来自外界的HTTPS请求，如果是合法的Trojan请求，那么为该请求提供服务，否则将该流量转交给web服务器Nginx，由Nginx为其提供服务。基于这个工作过程可以知道，Trojan的一切表现均与Nginx一致，不会引入额外特征，从而达到无法识别的效果。当然，为了防止恶意探测，我们需要将80端口的流量全部重定向到443端口，并且服务器只暴露80和443端口，这样可以使得服务器与常见的Web服务器表现一致。

## 前置条件

**系统要求：Ubuntu >= 16.04 or Debian >= 9 or CentOS >= 7。**

## VPS服务器购买

### 注册vultr

我使用的服务器是vultr。主要因为其按小时计费，随时可以停用服务器。这一点很重要，现在gfw相当高，所以你买到的机器很可能是已经被墙了的。其次，vultr本身没有被墙，这一点很重要，不然就是先有鸡还是先有蛋的问题了。当然，可以支付宝或微信付款也是一个加分点。vultr充值10美元送10美元活动注册地址：https://www.vultr.com。点击链接跳转到vultr，网页右上角有个Create Account就是注册的地方了，接下来就是注册并激活账号了。

### 充值

在购买服务器之前需要先充值。登陆vultr之后如图Billing->Alipay，选好要充值的金额数，然后Pay with Alipay即可跳转到支付宝扫码支付页面。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261131444.png)

### 购买服务器

点击vultr网页右上角的蓝色+图标即可为账户添加服务器。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039415.png)

**注意**：向下滚动有很多选项，不要直接点击Deploy Now，这样会使用默认参数，但不是我们想要的！ 第一个选项为选择服务器类型（Choose Server），这里选择Cloud Compute。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039431.png)

第二个选项为选择服务器地址（Server Location），建议选择硅谷（Silicon Valley，离google最近），而不要选择日本（用的人多，大部分IP已经被墙）；

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261131720.png)

第三个选项为选择服务器类型（Server Type），即选择服务器操作系统。作为示例，这里我们选择选择 **Ubuntu 18.10 x64（强烈推荐）**，不过系统要求部分所列出的操作系统均已测试通过，同一个发行版本的系统在我要求以下的版本测试不通过。注意如果你使用其他vps服务商的vps，可能有minimal选项，不能选择那一个，很多依赖乃没有安装，会导致自己配置失败（当然也可以，缺少哪个依赖自己再用apt安装就好了，例如：``apt install -y sudo，sudo apt install -y vim``等）。最后，如果你确实是有其他系统或者比较旧的系统版本需要安装trojan的话，建议使用docker，正文中部分配置只需要做细微修改即可，为了文章的整体性我就不放在正文了，请参考评论区。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261131395.png)

第四个选项为选择服务器大小（Server Size），即选择服务器硬件性能。因为我们使用VPS服务器翻墙，所以性能瓶颈不在配置，而在网络带宽之类的。由于vultr的服务器带宽都是100M的，所以服务器当然是往便宜了选。当然，``$2.5/mo``的一般都是缺货的。选择``$5/mo``的就好，一个月三十几块钱，要是有好几个人一起分担的话就相当实惠了。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039093.png)

第五、六、七三个选项可以不用管它。

第八条要求为即将部署的服务器命名并给一个标签，随意就好。

此时可以点击右下角的现在部署（Deploy Now），vultr即开始分配资源，安装系统。此时Servers页面可以看到服务器信息，状态显示installing。当状态切换为Running的时候，服务器就可以使用了。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261131572.png)

### 服务器信息查看

服务器安装结束之后在Servers页面点击刚才购买的服务器名字即可跳转到服务器详情页。如图右上角有一个View console可以直接通过网页连接到服务器，但是由于它不支持复制粘贴，不方便，我们需要使用第三方工具连接到服务器。所以需要知道IP、用户名和密码，如图左下角。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261131468.png)

## 域名申请与解析

trojan需要一个域名用来做伪装，所以需要先申请一个域名。如果自己已经有域名了那么可以不用申请，直接用一个子域名就可以了。如果你只是用来翻墙没有其他作用，那么建议注册一个免费域名即可。本教程使用freenom免费域名为例。

### freenom 免费域名申请

**我自己在使用Edge注册freenom的时候会失败，使用chrome才可以，你如果也失败了请换一个浏览器试试！**

**freenom在检测到ip对应的国家和你填写的个人信息不一致就会不允许你注册，所以要么不要挂VPN去申请域名，要么去网上生成虚拟美国人信息。**

freenom注册地址在这里 [https://www.freenom.com](https://www.freenom.com)，支持简体中文，可以自行切换。在寻找一个免费域名的输入框中输入自己理想的域名点击检查可用性，如图所示。检查到可用的中意的域名之后，点击现在获取即可锁定该域名。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039423.png)

现在可以点击完成按钮跳转到DNS配置和申请年限界面。点击Use DNS>>Use Frenom DNS Service。两个IP address框都直接**输入你购买的VPS服务器的IP地址**。Period选择12 Months @ FREE，然后点击Continue，输入并验证你的邮箱和信息即可。**注意上面的提示，不要挂VPN，很多人会卡在验证账户的地方，一般都是挂了VPN导致的。**

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039442.png)

### 付费域名推荐

在freenom申请的免费域名是可以无限免费续期的，除非被人花钱抢注，所以自己每隔几个月回来续期一下就可以啦（似乎现在已经不能免费续期了）。反正这个域名只是拿来翻墙用，被人抢注了马上换一个成本也不大。如果介意这个的话，建议自己买付费域名。国外域名服务商推荐namecheap，博主自己用的也是namecheap的付费域名。namecheap有一个好处就是免费送个人信息保护功能WhoisGuard，这样无法通过whois查询到域名注册信息，所以不会收到各种广告和避免被查水表的可能。**不要买国内域名服务商的域名。**

## VPS服务器部署

**跳过了上面系统选择与购买部分的要注意啦，本教程目前测试通过操作系统版本是Ubuntu 16.04 or Debian 9 or CentOS 7及以上，更低版本系统无法成功搭建，其他系统尚未测试！**

**跳过了上面系统选择与购买部分的要注意啦，本教程目前测试通过操作系统版本是Ubuntu 16.04 or Debian 9 or CentOS 7及以上，更低版本系统无法成功搭建，其他系统尚未测试！**

**跳过了上面系统选择与购买部分的要注意啦，本教程目前测试通过操作系统版本是Ubuntu 16.04 or Debian 9 or CentOS 7及以上，更低版本系统无法成功搭建，其他系统尚未测试！**

### 远程工具安装

本文使用的第三方远程管理工具叫做Xshell。Xshell学生和家庭版是免费的，可以放心试用（**觉得好用的话，不差钱的各位也可以注册一下支持软件开发者噢**）。安装好Xshell之后即可开始连接服务器进行部署了。不过Xshell没有Mac端，所以Mac可以使用其他ssh客户端，甚至直接用终端都可以，用法大同小异。

### 连接服务器

启动Xshell，从菜单栏的文件->新建打开新建会话窗口如下图。会话名称随便取一个都可以，主机填写刚才记下来的服务器IP地址或者直接填域名也是可以的。点击确定立即尝试连接服务器，如果能连接上服务器会提示输入用户名和密码。用户名为``root``，密码可以从vultr服务器详情页拷贝过来。可以记住用户名和密码，这样下次连接就不用再输入那个复杂的密码了（当然密码是可以修改的，但是没必要。系统自动生成的密码强度相当高，可以避免暴力破解。自己设的密码就不好说了，而且还容易忘记）。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039959.png)

连接成功之后会出现命令提示符：root@username:~#。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039044.png)

连接上服务器后就可以开始安装Trojan了。对于Xshell连不上服务器的情况，如果是使用域名连接的话，先ping一下域名看看是否DNS解析尚未生效，如果尚未生效，那么需要等一下，在等待的过程中可以先用IP将服务器配置好。如果ping不通IP的话，那么多半是被墙了，那就只能换掉服务器了。（**血的教训**：先购买新服务器，再去vultr服务器页面Destroy。反过来的话间隔太小，估计又买到原来的ip了。或者直接开个四五台服务器，然后保留ping结果最好的，删除其余的就好了。）

### 创建用户账户

为了系统安全，一般不建议直接使用``root``用户对系统做设置，而vultr默认只有``root``用户，故可以自己新建一个非``root``但是有``sudo``权限的用户继续后面的操作，代码如下所示，注意密码强度不能太低。第一条命令创建用户，第二条命令设置密码，第三条命令将该用户加入``sudo``组，第四条命令切换到该用户。

```js
sudo useradd -m -s /bin/bash trojanuser
sudo passwd trojanuser
sudo usermod -G sudo trojanuser
su -l trojanuser
```


### 创建服务账户

很多看教程的小伙伴问“为什么非要弄这么多用户，直接root不可以吗？”，这里简单回答一下。既然Trojan是占用443端口，使用https协议，所以必然是要安装证书的。本教程使用acme.sh为Trojan生成证书，并配置了acme.sh的自动更新，包括代码和证书的更新。一方面，既然有配置自动更新就有可能出各种问题，毕竟你对更新之后的代码和证书是否可用一无所知。另一方面，acme.sh和Trojan均为开源软件，不一定值得信赖。基于此，为了降低acme.sh和Trojan对系统的影响和其相互影响，故需要单独为acme.sh和Trojan建立没有``sudo``权限的用户。如果执意使用具有``sudo``权限的用户，或者头铁直接使用``root``，出了问题是要自己承担责任的。

这里我们创建两个用户，分别为``trojan``和``acme``。其中用户``trojan``只需要运行``trojan``服务，无需登录，也无需家目录，故设置为系统用户即可。这里将用户``acme``也设置为系统用户，但是区别在于``acme``需要配置acme.sh，故需要家目录。注意到，我并未给用户``acme``设置密码，所以该用户也不能登录，只能通过其他已经登录的用户切换过去，这样尽可能的保证了系统的安全与任务的独立。因为``trojan``和``acme``都需要读写证书文件，所以将``acme``和``trojan``添加到同一个用户组``certusers``，待申请到证书后将证书所有权交给用户组``certusers``并允许组内用户访问即可。

```python
sudo groupadd certusers
sudo useradd -r -M -G certusers trojan
sudo useradd -r -m -G certusers acme
```

### 安装依赖

由于Debian系列系统和CentOS系列系统使用不同的包管理软件，所以安装软件的命令不一样，下面两个小节自己对照自己系统选择命令。

#### Ubuntu or Debian

更新源

```python
sudo apt update
```

```js
sudo apt upgrade -y
```

安装acme.sh需要的依赖。

```python
sudo apt install -y socat cron curl
```

启动crontab

```js
sudo systemctl start cron
sudo systemctl enable cron
```

安装Trojan需要的依赖。

```js
sudo apt install -y libcap2-bin xz-utils nano
```

安装Nginx。

```js
sudo apt install -y nginx
```

#### CentOS

安装acme.sh需要的依赖。

```js
sudo yum install -y socat cronie curl
```

启动crontab。

```js
sudo systemctl start crond
sudo systemctl enable crond
```

安装Trojan需要的依赖。

```js
sudo yum install -y xz nano
```

安装Nginx。

```js
sudo yum install -y nginx
```
### 配置Nginx

**若你已经有配置Nginx虚拟主机，配置Nginx之前请，可以自己使用``cp``命令备份一下原来的虚拟主机，等介绍完基本配置再讲如何与现有服务集成。**

#### 关闭默认虚拟机

由于Nginx配置在Debian系列系统和CentOS系列系统组织方式不同，所以配置文件位置和使用方式有细微区别，为了统一，我将CentOS系列系统的组织结构做细微调整。

在Debian系列系统中，Nginx的虚拟主机配置文件在``/etc/nginx/sites-available/``文件夹中，如果要开启某一个虚拟主机，则建立一个软连接到``/etc/nginx/sites-enabled/``文件夹并重启Nginx即可。默认虚拟主机在``/etc/nginx/sites-enabled/``文件夹，需要关闭掉，否则会冲突。

在CentOS系列系统中，Nginx的虚拟主机配置文件在``/etc/nginx/conf.d/``文件夹中以``.conf``后缀保存，写入之后就可以使用。默认虚拟主机集成在Nginx配置文件``/etc/nginx/nginx.conf``中，需要打开将其中的server块删除，否则会冲突。Debian系列系统中的``/etc/nginx/sites-enabled/``和``/etc/nginx/sites-available/``文件夹结构在CentOS系列系统中是没有的，不过这个策略很不错，可以很方便的开启和关闭虚拟主机，我这里手动调整一下。

##### CentOS

按上述分析，我们使用下面两条命令在``/etc/nginx/``中添加两个文件夹。

```js
sudo mkdir /etc/nginx/sites-available
sudo mkdir /etc/nginx/sites-enabled
```

执行如下命令使用nano打开Nginx配置文件，删除其中server块，并添加对``/etc/nginx/sites-enabled/``文件夹的索引。

```js
sudo nano /etc/nginx/nginx.conf
```

配置文件修改结果如下图所示。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039124.png)

CentOS反向代理需要配置SELinux允许httpd模块可以联网，否则服务器会返回502错误。

```js
sudo setsebool -P httpd_can_network_connect true
```

##### Ubuntu or Debian

使用如下命令关闭Nginx默认虚拟主机。

```js
sudo rm /etc/nginx/sites-enabled/default
```

#### 写入虚拟主机到Nginx配置文件

1.执行如下命令，使用``nano``添加虚拟主机。(**注意域名``<tdom.ml>``改为你自己的域名，这是虚拟主机的文件名，只是用来自己识别的**。)

```js
sudo nano /etc/nginx/sites-available/<tdom.ml>
```

基于综述部分讲解的Trojan工作原理，现给定Nginx虚拟主机如下所示。这些虚拟主机可以直接拷贝到上面虚拟主机配置文件中再修改为你自己的，其中要修改的地方包括：

    1、第4行的 server_name 的值 <tdom.ml> 改为你自己的域名；
    
    2、第7行的 proxy_pass 随便指向一个没有敏感信息的网站都可以，这就是你要反向代理的网站，这里我是用了RFC文档的地址；
    
    3、第15行的 server_name 的值 <10.10.10.10> 改为你自己的 IP；
    
    4、第17行 <tdom.ml> 改为自己的域名，注意别填错了。

```js
server {
    listen 127.0.0.1:80 default_server;

    server_name <tdom.ml>;

    location / {
        proxy_pass https://www.ietf.org;
    }

}

server {
    listen 127.0.0.1:80;

    server_name <10.10.10.10>;

    return 301 https://<tdom.ml>$request_uri;
}

server {
    listen 0.0.0.0:80;
    listen [::]:80;

    server_name _;

    location / {
        return 301 https://$host$request_uri;
    }

    location /.well-known/acme-challenge {
       root /var/www/acme-challenge;
    }
}
```

解释一下这些虚拟主机的一些细节：第一个server接收来自Trojan的流量，与上面Trojan配置文件对应；第二个server也是接收来自Trojan的流量，但是这个流量尝试使用IP而不是域名访问服务器，所以将其认为是异常流量，并重定向到域名；第三个server接收除127.0.0.1:80外的所有80端口的流量并重定向到443端口，这样便开启了全站https，可有效的防止恶意探测，其中``.well-known``是为申请证书所准备的，下文用到的时候自然明白。注意到，第一个和第二个server对应综述部分原理图中的蓝色数据流，第三个server对应综述部分原理图中的红色数据流，综述部分原理图中的绿色数据流不会流到Nginx。

2.使能配置文件**注意域名``<tdom.ml>``改为你自己的域名**

```js
sudo ln -s /etc/nginx/sites-available/<tdom.ml> /etc/nginx/sites-enabled/
```

#### 启动Nginx

Nginx启动命令和Trojan一样，就不过多解释了.

```js
sudo systemctl restart nginx
sudo systemctl status nginx
```

### 配置证书

当从Let’s Encrypt获得证书时，Let’s Encrypt会验证证书中域名的控制权。一般采用HTTP-01或DNS-01方式来验证，详情参考官方文档 [验证方式](https://letsencrypt.org/zh-cn/docs/challenge-types/)。本文使用HTTP-01方式验证，若需要使用DNS-01方式验证，参考acme.sh官方文档 [How to use DNS API](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)。

#### 创建证书文件夹

第一条命令新建一个文件夹``/etc/letsencrypt/live``用于存放证书。第二条命令将证书文件夹所有者改为``acme``，使得用户``acme``有权限写入证书。

```js
sudo mkdir -p /etc/letsencrypt/live
sudo chown -R acme:acme /etc/letsencrypt/live
```

#### 创建webroot并修改所有者

本文使用acme.sh的http方式申请证书，http方式需要在网站根目录下放置一个文件来验证域名所有权，故需要acme.sh和nginx均对webroot目录有权限，故将运行Nginx的worker进程加入certusers组，下文再将webroot目录附加给certusers组即可。

在不同的Linux发新版本中，nginx可能使用不同的用户运行``worker process``，可能为``www-data``，``nginx``，``nobody``中的一个，故需要自己运行下述命令查找``nginx: worker process``所属用户：

```js
ps -eo user,command|grep nginx
```

上述命令输出第二行第一列即为``nginx: worker process``所属用户，然后根据实际情况，运行下面三个命令之一：

```js
sudo usermod -G certusers www-data

sudo usermod -G certusers nginx

sudo usermod -G certusers nobody
```

运行下面两条命令，第一条命令新建一个文件夹``/var/www/acme-challenge``用于给acme.sh存放域名验证文件。第二条命令将证书文件夹所有者改为``acme``，使得用户``acme``有权限写入文件，同时当验证的时候Nginx可以读取该文件。

```js
sudo mkdir -p  /var/www/acme-challenge
sudo chown -R acme:certusers /var/www/acme-challenge
```

#### 安装acme.sh自动管理CA证书脚本

**分别** 执行如下命令，注意看是否报错。第一条命令切换到用户``acme``。第二条命令安装acme.sh。第三条命令退出当前用户。第四条命令再次切换到用户``acme``。注意到这里两次切换用户的操作不能省略，因为安装完acme.sh之后要重新登录当前用户，否则无法识别出acme.sh命令。

```js
sudo su -l -s /bin/bash acme
```

```js
curl  https://get.acme.sh | sh
exit
```

```js
sudo su -l -s /bin/bash acme
```

#### 申请证书

执行如下命令将默认CA修改为Let's Encrypt：

```js
acme.sh --set-default-ca  --server  letsencrypt
```

执行如下命令（**注意域名``<tdom.ml>``改为你自己的域名**），等待一会儿。

```js
acme.sh --issue -d <tdom.ml> -w /var/www/acme-challenge
```

看到下图的提示表示证书申请成功。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261132456.png)

**申请失败怎么办**？ 证书申请失败的可能性一般有：1. 文件夹权限问题，请仔细检查每一步是否都正确；2. 证书申请次数超限，此时**切忌反复尝试**。证书每一个周申请次数是有限制的（20次），如果超限了就需要等一个周或者更换域名了（这个限制是争对每一个子域单独做的限制，所以万一超限了还可以用子域名继续部署）。解决方案是：在上述命令后加``--staging``参数继续测试。测试通过之后，删除上图所示四个证书文件以及该域名对应文件夹并取消 ``--staging`` 参数再执行一次。``--staging`` 参数申请的证书只作为测试用，客户端是无法认证通过的（提示``SSL handshake failed: tlsv1 alert unknown ca``），所以使用 ``--staging`` 参数申请到了证书之后要去掉 ``--staging`` 参数重新申请一次。

#### 安装证书

执行如下命令（**注意域名``<tdom.ml>``改为你自己的域名**），第一条命令使用acme.sh将证书安装到``certfiles``目录，这样acme.sh更新证书的时候会自动将新的证书安装到这里。第二条命令是配置acme.sh自动更新和自动更新证书，这样配置完Trojan之后一般不用管服务器。

```js
acme.sh --install-cert -d <tdom.ml> --key-file /etc/letsencrypt/live/private.key --fullchain-file /etc/letsencrypt/live/certificate.crt
```

```js
acme.sh  --upgrade  --auto-upgrade
```

#### 修改权限

最后还要允许组内用户访问证书。可通过如下命令实现。第一条命令将证书文件夹所在用户组改为``certusers``。第二条命令是赋予证书文件夹组内用户读取权限。运行这两条命令之后用户``trojan``就有权限读取证书了。第三条命令退出用户``acme``，因为证书已经安装完成。

```js
chown -R acme:certusers /etc/letsencrypt/live
chmod -R 750 /etc/letsencrypt/live
exit
```

### 配置Trojan

#### 安装Trojan

分别执行如下四个命令，注意看是否报错。第一个命令是安装Trojan，安装完成一般会提示版本号注意看是否是最新版本。第二个命令是将Trojan配置文件的所有者修改为用户``trojan``，由于使用``sudo``安装的Trojan，该配置文件默认是属于``root``用户的，而我们需要使用用户``trojan``运行Trojan，不修改所有者会导致启动Trojan遇到权限问题。第三个命令备份Trojan配置文件，以防万一。第四个命令是使用nano修改配置文件。

```js
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/trojan-gfw/trojan-quickstart/master/trojan-quickstart.sh)"
```

```js
sudo chown -R trojan:trojan /usr/local/etc/trojan
sudo cp /usr/local/etc/trojan/config.json /usr/local/etc/trojan/config.json.bak
sudo nano /usr/local/etc/trojan/config.json
```

第四个命令执行完之后屏幕会显示Trojan的配置文件，定位到``password``、``cert``和``key``并修改。密码按自己喜好，``cert``和``key``分别改为``/etc/letsencrypt/live/certificate.crt``和``/etc/letsencrypt/live/private.key``。编辑完成配置文件之后按屏幕下方快捷键提示（``^O``和``^X``即：``Ctrl+O``和``Ctrl+X``）保存并退出``nano``。修改之后的``config``文件如图所示。另外，如果有``IPv6``地址，将``local_addr``的``0.0.0.0``改为``::``才可以使用。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200039333.png)

#### 启动Trojan

##### 修改Trojan启动用户

执行如下命令，打开``trojan.service``文件，并将用户修改为``trojan``。

```js
sudo nano /etc/systemd/system/trojan.service
```

添加用户效果如图所示，注意等号旁边没有空格。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205261132530.png)

然后重新加载配置文件。

```js
sudo systemctl daemon-reload
```

##### 赋予Trojan监听443端口能力

执行如下命令，赋予Trojan监听1024以下端口的能力，使得Trojan可以监听到443端口。这是由于我们使用非``root``用户启动Trojan，但是Linux默认不允许非``root``用户启动的进程监听1024以下的端口，除非为每一个二进制文件显式声明。

```js
sudo setcap CAP_NET_BIND_SERVICE=+eip /usr/local/bin/trojan
```

##### 使用systemd启动Trojan

Trojan启动、查看状态命令分别如下，第一条是启动Trojan，第二条是查看Trojan运行状态。启动之后再查看一下状态，Trojan显示active (running)即表示正常启动了。如果出现``fatal: config.json(n): invalid code sequence``错误，那么是你的配置文件第``n``行有错误，请检查。如果启动失败，还可以用``journalctl -f -u trojan``查看``systemd``的日志。

```js
sudo systemctl enable trojan
sudo systemctl restart trojan
sudo systemctl status trojan
```

#### 更新证书

当acme.sh重新安装证书之后，需要通知Trojan重新加载证书。最简单的方案是每三个月登录服务器重启Trojan，但是不够完美，毕竟重启的时候会导致服务中断。其实Trojan有实现reload certificate and private key功能，只需要在证书更新后给Trojan发送``SIGUSR1``消息即可。Trojan收到``SIGUSR1``消息后便会自动加载新的证书和密钥文件，这样就不用重启Trojan了。手动给Trojan发送``SIGUSR1``消息的命令是``sudo -u trojan killall -s SIGUSR1 trojan``，但是这样也不够完美，也得每三个月登录服务器运行一次该命令。其实我们可以给用户``trojan``添加定时任务，使其每个月运行一次该命令即可。实现如下。

首先，编辑用户``trojan``的crontab文件

```js
sudo -u trojan crontab -e
```

在文件末尾添加一行如下，该行表示每个月1号的时候运行命令``killall -s SIGUSR1 trojan``，由于是使用用户``trojan`运行的，故不需要在前面加``sudo -u trojan``。

```js
0 0 1 * * killall -s SIGUSR1 trojan
```

最后查看crontab是否生效.

```js
sudo -u trojan crontab -l
```

#### 更新Trojan

如果Trojan版本有更新（可以去这里查看是否有更新），那么使用本教程搭建的服务器端更新Trojan版本只需要三条命令即可，不过要注意的是，第一条命令会提示是否覆盖配置文件，如果没有必要请回答n，否则配置文件将会被覆盖（如果不小心覆盖了就得自己重新编辑了）。第二条命令重新赋予Trojan监听443端口的能力。第三条命令重启Trojan。

```js
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/trojan-gfw/trojan-quickstart/master/trojan-quickstart.sh)"
```

```js
sudo setcap CAP_NET_BIND_SERVICE=+eip /usr/local/bin/trojan
sudo systemctl restart trojan
```

### 配置Trojan和Nginx开机自启

虽然开机自启一般用不着，除非vultr机房停电，但是反正也没什么代价，弄一下吧。

```js
sudo systemctl enable trojan
sudo systemctl enable nginx
```

### 检查服务器是否配置成功

到这里服务器就配置完成了。此时你可以在浏览器里面访问你的网站看是否能够访问，如果你的网站可以访问了，那么就一切正常啦。

另外，基于以上考虑到的可能的恶意探测，可以验证一下以下情况是否正常。

    1、浏览器中使用ip访问：重定向到https://tdom.ml;
    2、浏览器中使用https://ip访问：重定向到https://tdom.ml(跳转的时候浏览器可能提示不安全是正常的);
    3、浏览器中使用tdom.ml访问：重定向到https://tdom.ml。

### 启动bbr(可选,建议)

启动bbr需要Linux内核版本在4.10及以上，如果低于该版本需要自己升级（这不在本教程范围之后）内核版本，保证内核版本不低于4.10。查看系统内核版本命令如下。

```js
uname -a
```

bbr是谷歌开发的网络控制算法，可以加快访问速度。执行下面命令查看当前系统是否启用bbr。

```js
sudo sysctl net.ipv4.tcp_congestion_control
```

执行完成之后如果提示``net.ipv4.tcp_congestion_control = bbr``即表示启动了bbr，可以跳过下面启动bbr的步骤。

直接将下面三条命令拷贝粘贴到Xshell里面执行即可启动bbr，检查启动状态同上。

```js
sudo bash -c 'echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf'
sudo bash -c 'echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf'
sudo sysctl -p
```

### 配置防火墙（可选）

只打开22、80和443端口可以有效的阻止外部恶意流量，降低服务器暴露的风险。此步骤非必须，而且自己有其他服务记得其他服务的端口也要处理。

本文以ufw为例配置防火墙，ufw是一个很好用的防火墙配置命令，可以简化操作，减少错误的发生。

> **Debian or Ubuntu执行如下命令安装ufw**

```js
sudo apt install -y ufw
```

> **CentOS执行如下两个命令安装ufw**

```js
sudo yum install -y epel-release
sudo yum install -y ufw
```

如果服务器无IPv6地址那么需要执行如下命令，将IPV6=yes修改为IPV6=no。

```js
sudo nano /etc/default/ufw
```

执行如下命令即可成功配置防火墙。注意，如果ssh端口不是22那么第一行需要调整（将ssh改为端口号）。

```js
sudo ufw disable
sudo ufw allow ssh
sudo ufw allow https
sudo ufw allow http
sudo ufw enable
```

```js
sudo ufw status
sudo ufw status verbose
```

另外，如果对Trojan不放心，那么可以参考[trojan wiki](https://github.com/trojan-gfw/trojan/wiki/Limiting-Server-Network-Access)，优化防火墙配置，使得Trojan只能给127.0.0.1:80发送数据和响应外部请求。

### 与现有Nginx服务集成
如果你本机已经有Nginx服务，那么Nginx配置文件需要做适当修改以和现有服务兼容。

    1、在原服务与Trojan使用同一个域名且原来是监听443端口的情况下，那么需要将你的ssl配置删除并将监听地址改为第一个server监听的地址127.0.0.1:80，然后直接用修改好的server代替上述配置文件中第一个server即可。这样https加密部分将会由Trojan处理之后转发给Nginx而不是由Nginx处理，原来的服务对于客户端来说就没有变化。
    
    2、如果原来的服务与Trojan使用不同的域名，建议是修改Trojan与原来的服务使用同一个域名，如果非要使用不同的域名，请参考第3点。
    
    3、如果原来的服务就监听了多个域名，那么请自己琢磨Nginx的sni，参考连接：ngx_stream_ssl_preread_module。
    
    4、如果原来的服务是监听80端口，想要继续监听80端口那么直接去除第三个server即可（将域名验证相关location与自己的虚拟主机组合，否则无法更新证书），如果要改为监听443端口参考第1点。