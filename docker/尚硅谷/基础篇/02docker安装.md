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

