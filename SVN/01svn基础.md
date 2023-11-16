### 基础

> 安装

```sh
sudo apt install subversion
# 查看版本
svn --version
```

![image-20231111160255392](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111603075.png)

> 创建版本库

```sh
svnadmin create 文件夹目录
如： svnadmin create /opt/svn/runoob
```

![image-20231105215612255](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052156697.png)

> 进行服务端监管

![image-20231105221900354](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311052219403.png)

```sh
svnserve -d -r 目录 --listen-port 端口号
# -r：配置方式决定了版本库的访问方式
# --listen-port：指定svn监听端口号，不加此参数，svn默认监听3690
```

由于-r配置方式的不一样，svn启动就可以有两种不同的访问方式

**方式一**：-r直接指定到版本库（称之为单库svnserve方式）

```sh
svnserve -d -r /opt/svn/runoob
```

在这种情况下，一个svnserve只能为一个单库工作。

authz配置文件中对版本库权限的配置应该这样写：

```sh
[groups]
admin=user1
dev=user2
[/]
@admin=rw
user2=r
```

**方式二：**指定版本库的上级目录（称之为多库svnserve方式）

```sh
svnserve -d -r /opt/svn
```

这种情况下，一个svnserve可以为多个版本库工作

authz配置文件中对版本库权限的配置应工这样写：

```sh
[groups]
admin=user1
dev=user2
[runoob:/]
@admin=rw
user2=r

[runoob01:/]
@admin=rw
user2=r
```

如果此时你还用[/]，则表示所有库的根目录，同理，[/src]表示所有库的根目录下的src目录。

使用类似这样的URL：svn://192.168.0.1/runoob　即可访问runoob版本库。

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



### SVN详解（一）

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

以上过程一般是项目经理完成的。

如果公司来了一个程序员李四，那么他需要哪些操作呢？

![image-20231111183739661](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111837735.png)

在上图的lisi的空白文件夹区域内右键--->TortoiseSVN--->版本库浏览器--->输入浏览器SVN服务器地址。

然后在下面的页面中右键检出，然后单击确定即可。

![image-20231111183944154](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111839281.png)

![image-20231111183957647](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111839752.png)

![image-20231111184045148](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111840257.png)

**小结：**

1）检出操作，效果如下：

![image-20231111184152657](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111841733.png)

2）创建新文件admin.php并提交

![image-20231111184329159](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111843221.png)

3）提交操作

![image-20231111184413620](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111844677.png)

以上指令通常是在模块开发完毕以后上传。

4）项目经理（Update更新操作）

在项目经理的根目录下右键——>SVN更新

![image-20231111184640536](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111846618.png)



### SVN详解（二）

**1、图标集**

![image-20231111185042448](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111850515.png)

1）常规图标：![image-20231111185239743](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111852784.png)

含义：当客户端文件与服务器端文件完全同步时，系统显示以上图标。

2）冲突图标：![image-20231111185343119](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111853173.png)

含义：当客户端提交的文件与服务器端数据有冲突，系统会显示以上图标。

3）删除图标：![image-20231111185425340](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111854380.png)

含义：当服务器端数据已删除，那么客户端该文件将显示该图标

4）增加图标：![image-20231111185612259](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111856339.png)

含义：当我们编写的文件已添加到提交队列，那么系统将自动显示以上图标

5）无版本控制图标：![image-20231111185708777](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111857816.png)

含义：当我们编写的文件没有提交到上传队列时，系统将自动提示以上图标

6）修改图标：![image-20231111185806707](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111858745.png)

含义：当客户端文件有修改但未提交，此时将自动显示该图标

7）只读图标：![image-20231111185918503](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111859542.png)

含义：当客户端文件以只读形式存在时，将自动显示该图标

8）锁定图标：![image-20231111190007611](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111900341.png)

含义：当服务端数据已锁定，那么客户端文件将自动显示锁定图标

9）忽略图标：![image-20231111190110344](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111901381.png)

含义：客户端文件已忽略，不需要进行提交上传，那么将显示以上图标



**2、忽略功能**

有些文件不希望上传到svn服务器，应该将该文件或该类型的文件添加至忽略列表。

1）忽略某个指定的文件

![image-20231111190540962](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111905060.png)

2）忽略某个类型的文件

![image-20231111190725850](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111907959.png)



### SVN详解（三）

**1、版本回退**

有些时候，软件的运行可能使开发者或者是用着不满意，这是我们需要把当前版本回退到以前的某个版本。

![image-20231111191416547](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111914651.png)

**2、版本回退功能**

在项目空白处鼠标右键，采用如下图所示操作：

![image-20231111192050708](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111920246.png)

根据版本日志进行回退：

![image-20231111192233476](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111922547.png)

根据日志信息选择要回退的状态，效果如下：

![image-20231111192352993](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111923112.png)



### SVN详解（四）

**1、什么是版本冲突**

在实际的开发中，如果两个人同时修改了某个文件就可能会产生版本冲突。

**2、模拟版本冲突**

![image-20231111195550125](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111955225.png)

![image-20231111195844201](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311111958284.png)

**3、解决之道**

1）合理分配项目开发时间

2）合理分配项目开发模块

3）通过SVN解决版本冲突问题

1. 更新服务器端数据到本地

   ![image-20231111200120950](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112001011.png)

   ![image-20231111201118374](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112011420.png)

2. 删除除index.php以外的其他三个文件

3. 修改整合index.php冲突文件

   1. 右键TortoiseSVN——>解决冲突
   2. 重新SVN提交



### 配置多仓库与权限控制

**1、配置多仓库**

在实际项目开发中，我们可能同时开发多个项目，那么我们如何进行项目的监管呢？

通过svnserve进行仓库监管，但是监管指令只能监管单个文件夹，而不能同时监管多个仓库。

答：可以通过监管版本库的上一级目录，来实现多个仓库的监管

```shell
svnserve -d -r /opt/svn
```

runoob项目：`svn://localhost/runoob`

**2、权限控制**

首先需要开启权限功能。在每一个仓库中都有一个conf文件夹，里面有三个文件：

- authz：授权文件，告诉哪些用户具有哪些权限
- passwd：认证文件，标识当前svn系统中某个仓库具有哪些用户以及相应的密码。

默认情况下，以上两个文件都是禁用的，如需要使用，首先要开启以上两个文件。

在svnserve.conf中进行开启：

- 关闭非授权用户的读写权限

  ![image-20231111203122768](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112031724.png)

- 开启认证文件和授权文件

  ![image-20231111203416089](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112034578.png)

- 编写认证文件（passwd）定义相关用户名和密码

  ![image-20231111203808201](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112038244.png)

- 编写授权文件（authz）

  ![image-20231111210148829](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112101896.png)

  ![image-20231111210158413](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112101457.png)

- 测试

  ![image-20231111211120320](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202311112111406.png)



### SVN服务的配置与管理

**1、配置自启动服务**

