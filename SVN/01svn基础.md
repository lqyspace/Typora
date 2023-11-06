### 基础

> 安装

```sh
sudo apt install subversion
```

> 创建版本库

```sh
svnadmin create 文件夹目录
eg: svnadmin create /opt/svn/runoob
```

![image-20231105215612255](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052156697.png)

> 进行服务端监管

![image-20231105221900354](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052219403.png)

```sh
svnserve -d (后台运行) -r (监管目录) 版本仓库路径
```

> 权限控制

默认情况下，svn服务器是不允许匿名用户上传文件到服务端的，所以必须更改项目的相关配置文件。

![image-20231105223431093](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052234142.png)

打开svnserve.conf文件，修改配置文件：

![image-20231105223606654](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052236691.png)

![image-20231105223640830](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052236881.png)

> 安装SVN客户端

[https://tortoisesvn.net/downloads.html](https://tortoisesvn.net/downloads.html)

查看教程：

[https://www.runoob.com/svn/tortoisesvn-intro.html](https://www.runoob.com/svn/tortoisesvn-intro.html)

谨记：安装了以后一定要重启计算机，否则SVN的图标是无法显示的

**使用SVN客户端连接SVN服务器**

（checkout检出）

首先在**你的项目目录**鼠标右键--->TortoiseSVN--->版本库浏览器--->输入浏览器SVN服务器地址

![image-20231105234425604](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052344501.png)

svn://SVN服务器地址--->shop项目仓库，然后在svn服务器地址上鼠标右键，单击**检出**

![image-20231105234538891](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052345966.png)

显示隐藏文件，既可以看到如下：

![image-20231105234628454](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052346478.png)

说明客户端与服务器端建立了联系，检出成功。



### SVN详解

> 回顾SVN三大指令

- （checkout）检出操作：
  - 连接到SVN服务器端
  - 更新服务器端数据到本地
  - checkout只在第一次连接时操作一次，以后如果进行更新操作请使用Update（更新操作）

- （commit）提交操作：

  - 提交本地数据到服务器端

  - 在本地工作区进行了新建或修改操作，只需要在本地工作区的空白区域右键，然后单击`SVN提交`

    ![image-20231105235606541](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052356598.png)

    ![image-20231105235705781](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052357845.png)

  如显示一下界面，说明提交成功：

  ![image-20231105235836128](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052358184.png)

以上过程一般是项目经理完成的。如果公司来了一个程序员李四，那么他需要哪些操作呢？

