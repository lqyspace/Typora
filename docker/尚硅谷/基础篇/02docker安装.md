[toc]

docker并非是一个通用的容器工具，它依赖于已存在并运行的Linux内核环境。

Docker实质上是在已经运行的Linux下制造一个隔离的文件环境，因此它执行的效率几乎等同于所部署的Linux主机。

因此，Docker必须部署在Linux内核的系统上。如果其他系统想部署Docker就必须安装一个虚拟Linux环境。

![image-20241124210244971](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411242102995.png)

在Window上部署Docker的方法都是先安装一个虚拟机，并在虚拟机Linux中运行Docker。



## 最详细的ubuntu安装docker教程

Docker是一种流行的容器化平台，它能够简化应用程序的部署和管理。

系统ubuntu20.04.

### 卸载老版本docker

ubuntu下自带了docker的库，不需要添加新的源。

但是版本较低，需要卸载旧的再安装新的。

注：docker的旧版本不一定被称为docker，docker.io或docker-engine也有可能，所以我们的卸载命令为:

```shell
$ apt-get remove docker docker-engine docker.io containerd runc
```

如果报无权限的错误，则在命令前加sudo即可。

### 安装步骤

> **1、更新软件包**

```shell
sudo apt update
sudo apt upgrade
```

> **2、安装docker依赖**

安装相关依赖：

```shell
apt-get install ca-certificates curl gnupg lsb-release
```

> **3、添加Docker官方GPG密钥**

执行以下命令来添加Docker官方的GPG密钥：

```shell
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

![image-20241125004639405](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411250046443.png)

> **4、添加Docker软件源**

执行以下命令来添加Docker的软件源：

```shell
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

注：该命令需要使用root权限。

> **5、安装docker**

执行以下命令来安装docker：

```shell
apt-get install docker-ce docker-ce-cli containerd.io
```

> **配置用户组（可选）**

默认情况下，只有root和docker组的用户才能运行Docker命令。我们可以将当前用户添加到docker组，以避免每次使用docker时都需要使用sudo：

```shell
sudo usermod -aG docker $USER
```

**注：重新登陆才能使更改生效**

> **运行docker**

我们可以通过启动docker来验证我们是否成功安装。

```shell
systemctl start docker
```

安装工具：

```shell
apt-get -y install apt-transport-https ca-certificates curl software-properties-common
```

重启docker

```shell
service docker restart
```

验证是否成功：

```shell
sudo docker run hello-world
```

![image-20241125011805879](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411250118921.png)

因为我们之前没有拉取过`hello-world`，所以运行命令后出现本地没有该镜像，并会自动拉取的操作。

> **查看版本**

```shell
sudo docker version
```

![image-20241125011946477](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411250119517.png)

> **查看镜像**

```shell
sudo docker images
```

![image-20241125012028153](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411250120192.png)

> **docker设置临时代理**

```shell
HTTP_PROXY=http://your_proxy_address:port HTTPS_PROXY=https://your_proxy_address:port docker pull ubuntu
```

**注意：**

- 这些设置仅对当前执行的命令有效，并不会影响其他终端会话或系统中的其他进程。
- 如果你的代理需要认证，URL格式应该是http://username:password@your_proxy_address:port。
- 使用临时代理设置时，请确保你的代理服务器地址、端口号以及用户名和密码（如果需要）是正确的。
- 有些应用程序或工具可能不遵循环境变量中的代理设置，需要查阅具体工具的文档来了解如何配置它们使用代理



## Docker三要素

**镜像（image）、容器（container）、仓库（repository）**

> [!tip]
>
> Docker镜像（Image）就是一个只读模板。镜像可以用来创建Docker容器，一个镜像可以创建很多容器。
>
> 镜像也相当于是一个root文件系统。比如官方镜像centos：7就包含了一套完整的centos：7最小系统的root文件系统。
>
> 相当于容器的”源代码“，docker镜像文件类似于Java的类模板，而docker容器实例类似于Java中new出来的实例对象。
>
> 容器与镜像的关系类似于面向对象中的对象与类：
>
> | Docker | 面向对象 |
> | ------ | -------- |
> | 容器   | 对象     |
> | 镜像   | 类       |
>
> 1、从面向对象角度理解
>
> Docker利用容器（Container）独立运行一个或者一组应用，应用程序或者服务运行在容器里面，容器就类似于一个虚拟化的运行环境，容器是用镜像创建的运行实例。就像是Java中的类和实例对象一样，镜像是静态的定义，容器是镜像运行的实体。容器为镜像提供了一个标准的和隔离的运行环境，它可以被启动、开始、停止、删除。每个容器都是相互隔离开的，保证安全的平台。
>
> 2、从镜像容器角度
>
> 可以把容器看作是一个简易的Linux环境（包括root权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。
>
> 3、仓库（Repository）是集中存放镜像的地方：公开库、私有库、Docker Hub是最大的公开仓库,存放了数量庞大的镜像供用户下载.国内的公开仓库包括阿里云,网易云等
>
> 需要正确理解仓库\镜像\容器这几个概念:
>
> Docker本身是一个容器运行载体或称之为管理引擎.我们把应用程序和配置依赖打包形成一个可交付的运行环境,这个打包好的运行环境就是image镜像文件.只有通过这个镜像文件才能生成Docker容器实例(类似Java中new出来一个实体对象).
>
> image文件可以看作是容器的模板.Docker根据image文件生成容器的实例.同一个image文件,可以生成多个同时运行的容器实例.
>
> 镜像文件:image文件生成文件实例,本身也是一个文件,称为镜像文件.
>
> 容器实例:一个容器运行一种服务,当我们需要的时候,就可以通过docker客户端创建一个对应的运行实例,也就是我们的容器.
>
> 仓库:就是存一堆镜像的地方,我们可以把镜像发布到仓库中,需要的时候再从仓库中拉下来就可以了.

![image-20241125231454621](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411252315707.png)

客户端运行相应的指令,如果镜像主机上有镜像,那么可以直接从本地取出镜像并且可以生成多个容器实例.软如果本地没有镜像,就会去远程服务器上拉取镜像,并生成对应的容器实例.

Docker是一个client-server结构的系统,Docker守护进程运行在主机上,然后通过Socket连接从客户端访问,守护进程从客户端接受命令并管理运行在主机上的容器.容器是一个运行时环境,就是我们前面说到的集装箱.



## Docker平台架构图解

Docker是一个C/S模式的架构，后端是一个松耦合架构，众模块各司其职。

Docker运行的基本流程：

- 用户是使用Docker client与Docker Daemon建立通信，并发送请求给后者。
- Docker Maemon作为Docker架构的主体部分，首先提供Docker Server的功能使其可以接受Docker Client的请求
- Docker Engine执行Docker内部的一系列工作，每一项工作都是以一个Job的形式存在
- Job运行过程中，当需要容器镜像时，则从Docker Registry中下载镜像，并通过镜像管理驱动Graph driver将下载镜像以Graph的形式存储。
- 当需要为Docker创建网络环境时，通过网络管理驱动Network driver创建并配置Docker容器的网络环境
- 当需要限制Docker容器运行资源或执行用户指令等操作时，则通过Exec driver来完成
- Libcontainer是一项独立的容器管理包，network driver以及Exec driver都是通过Libcontainer来实现具体对容器进行的操作。

![yuque_diagram](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411282219800.png)

[图片所在文档地址|语雀](https://www.yuque.com/g/xiaoyun-guxn0/bdrip1/agzh88q7ce9m94rr/collaborator/join?token=SSfJOs1sNPOfXROd&source=doc_collaborator# 《Docker平台架构图解》)



## Docker配置国内镜像源加速器

### 1. 国内镜像源总览[#](https://www.cnblogs.com/cao-lei/p/14448052.html#1689145947)

| 名称         | 路径                                                         |
| ------------ | ------------------------------------------------------------ |
| 中国官方镜像 | [https://registry.docker-cn.com](https://registry.docker-cn.com/) |
| 网易163镜像  | [http://hub-mirror.c.163.com](http://hub-mirror.c.163.com/)  |
| 中科大镜像   | [https://docker.mirrors.ustc.edu.cn](https://docker.mirrors.ustc.edu.cn/) |
| 阿里云镜像   | https://[xxx].mirror.aliyuncs.com                            |
| DaoCloud镜像 | http://[xxx].m.daocloud.io                                   |

### 2. 阿里云镜像源[#](https://www.cnblogs.com/cao-lei/p/14448052.html#3287251753)

  访问：https://cr.console.aliyun.com/#/accelerator
[![img](https://img2020.cnblogs.com/blog/1119097/202102/1119097-20210225170921811-443270618.png)](https://img2020.cnblogs.com/blog/1119097/202102/1119097-20210225170921811-443270618.png)

### 3. DaoCloud镜像源[#](https://www.cnblogs.com/cao-lei/p/14448052.html#2441363181)

  访问：https://www.daocloud.io/
[![img](https://img2020.cnblogs.com/blog/1119097/202102/1119097-20210225170932101-949099246.png)](https://img2020.cnblogs.com/blog/1119097/202102/1119097-20210225170932101-949099246.png)
[![img](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411290057864.png)](https://img2020.cnblogs.com/blog/1119097/202102/1119097-20210225170938389-660372850.png)

### 4. 配置镜像源[#](https://www.cnblogs.com/cao-lei/p/14448052.html#2450768600)

```bash
vi /etc/docker/daemon.json

# 内容如下：
{
  "registry-mirrors": [
    "https://xx4bwyg2.mirror.aliyuncs.com",
    "http://f1361db2.m.daocloud.io",
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}{}

# 退出并保存
:wq

# 使配置生效
systemctl daemon-reload

# 重启Docker
systemctl restart docker
```
