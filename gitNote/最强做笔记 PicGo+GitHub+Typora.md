[TOC]

# Typora+PicGo+GitHub使用方法

## typora与picgo下载与安装

### typora下载与安装

> 版本要求
>
> typora 0.9.84 及以上
>
> 我的版本是1.2.4

![image-20220517004052339](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170040400.png)

可以在百度上找资源，这里不再赘述。



### picgo下载与安装

**官网：** [PicGo]( https://github.com/Molunerfinn/PicGo/releases)

下载最新版本，这里我使用的版本是 v2.3.0。（**根据你们的系统版本选择下载安装包**）

![image-20220517004038483](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170040545.png)

安装打开之后可以看到如下：

![image-20220517004014233](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170040298.png)



## github的简单实用

### github创建新库作为图床

1、如果没有GitHub的账号的话可以申请注册一个

2、登陆以后，点击右上角的 **+** 号，然后点击 new repository 新建仓库（这个仓库作为你的图床）

![image-20220517004805860](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170048895.png)

3、新建仓库时，仓库名必填（仓库名尽量不要有空格），私有性选择公开，然后点击 create repository。

![image-20220517005856674](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170058732.png)



### 新建token令牌

1、点击右上角头像列表中 **Setting** 选项

![image-20220517010432839](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170104881.png)

2、进入后点击最后一项： **Developer setting**

![image-20220517010519662](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170105714.png)

3、进入后点击右边的最后一项，并且创建一个新的 **token**

![image-20220517010618641](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170106706.png)



4、在 Note 填写你的token的名字，将 repo 打上勾就行，其余的不用管，然后直接点击 绿色图标 Generate token。

![image-20220517010803107](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170108151.png)

5、完成后，一定要保存此token，因为只出现一次，一定要保存！！！！



## typora+picgo+github的关联

### PicGo图床的配置

1、下载完成后打开PicGo

2、首次使用时进入PicGo，点击设置Server，将监听窗口设为：36677。可保持默认。

3、点击左边图床设置进入GitHub图床。

4、设定仓库名，填写格式：你的GitHub名/新建的仓库名

5、设定分支名，填写自己想要填写的分支，也可以填写默认的 main

6、设定 Token，填写刚刚保存的 token令牌。

7、指定存储路径，默认为img/，这个路径是你GitHub的新建仓库中某一个文件夹的路径

8、自定义域名：这里使用 https://raw.githubusercontent.com/你的GitHub名/仓库名/master，这里的 master 是你的仓库下的一个分支

9、最后点击确定和设为默认图床。

![image-20220517011751312](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170117368.png)



### Typora相关设置

1、设置完PicGo之后，打开Typora，找到文件中的 **偏好设置**。

2、点击 **图像** ，**前三项** 以及 **最后一项** 打钩。

3、将 **上传服务改为PicGo（app）**。

4、PicGo路径改为你的**本地PicGo安装目录中的PicGo.exe文件**

5、最后点击**验证图片上传**来验证。

6、查看 PicGo 相册 和 GitHub 同步的上传

7、在Typora中插入图片时，图片可以通过PicGo直接上传到GitHub仓库中，以后传输Typora文件时便不用再将图片一起打包上传。

![image-20220517012521419](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170125477.png)

![image-20220517012556859](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170125917.png)

![image-20220517012649572](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170126617.png)





# 使用此教程你可能会踩到的坑

## 错误排查

### 端口号

> 一般报错：**Failed to fetch**

一般是由端口号设置错误造成，详细错误信息你可以查看日志log。

> 解决办法：打开picgo，点击设置Server选项，更改端口号，推荐使用默认的端口号：**36677**

不过有的时候，仍然会出现这样的报错，打开端口号一看的话，端口号可能不是36677，而是其他的端口号，

问题存在端口冲突，如果你打开了多个picgo程序时，就会有端口冲突，picgo会自动帮你把 **36677端口** 改为其他的 **端口** 。



解决办法：先把picgo里面的端口号设置回 **36677**，然后退出所有程序，再使用 Typora 上传功能。



### 文件命名错误

> 上传图片失败：错误信息：{"success" : false}

这个错误是会经常遇见的错误，原因大多是因为文件名冲突了 。

详细信息可以查看日志log。



> 解决办法：打开picgo设置，将时间戳重命名打开。如图所示。

![image-20220517015154304](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170151374.png)



## 一些坑

1、如果你发现你的上传功能不好使了，即使改了Token，重装了PicGo，也无济于事，那么，请你检查一下你的仓库名中是否含有空格。请删去空格，因为在你的github仓库中是没有空格的（一般会以“-”代替），如果你的仓库有空格，软件会自动帮你转换：code dog转换为 code-dog。

2、改完以后如果还是不能上传，检查文件名是不是不规范，比如这个样子：

![image-20220517015936990](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170159027.png)

​	由于文件命中含有“+”号，所以上传不成功。

​	这里再次建议使用 “**时间戳重命名**”。



## 查看日志信息

![image-20220517015313202](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205170153248.png)





