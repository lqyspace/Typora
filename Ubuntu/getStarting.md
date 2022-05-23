[TOC]

## 设置root密码

```
sudo passwd root
```



## 切换用户

```
su -l <user>
```



## Ubuntu20.04使用vi编辑时输入异常且方向键乱码

> 卸载

```
apt remove vim-common
```

> 安装

```
apt install vim
```



## 如何查看Ubuntu的CPU占用信息

> 直接使用 top

```
top
```

> 或者安装 htop

```
apt install htop

htop
```

> 按 Ctrl+C 退出



## 解决Ubuntu下SSH无法连接的问题

> 检查是否开启ssh服务

```
ps -e | grep ssh
```

输出的结果 ssh-agent 表示 ssh-client 启动，sshd 表示 ssh-server启动，我们需要安装服务端，所以需要看是否有 sshd，如果没有则说明没有安装。



> 安装SSH服务

```
apt install openssh-client
apt install openssh-server

或者

apt install ssh
```



> 启动ssh服务

```
/etc/init.d/ssh start
```



> 修改ssh配置文件

可以通过SSH配置文件更改端口，是否允许root登录等设置，配置文件位置如下：

```
/etc/ssh/sshd_config
```

默认是不允许root远程登录的，通过修改配置文件：

- 找到PermitRootLogin prohibit-password 修改为 PermitRootLogin yes
- PasswordAuthentication yes



> 重启SSH

```
/etc/init.d/ssh restart

或者

service ssh restart
```



## Ubuntu20.04使用root用户登录失败

> 创建root用户

```
sudo passwd root
接下来输入密码和确认密码
```



> 修改50-Ubuntu.conf文件

```
vi /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf
```

在文件末尾增加如下两行并保存：

```
greeter-show-manual-login=true #手工输入登陆系统的用户名和密码
allow-guest=false #不允许guest登录（可选）
```



> 修改gdm-password文件

````
vi /etc/pam.d/gdm-password
````

在第三行前面加#以注释掉auth required pam_succeed_if.so user != root quiet_success，随后保存并退出



> 修改/root/.profile文件

```
vi /root/.profile
```

将文件末尾的 mesg n 2> /dev/null || true 这一行注释，并随后添加：

```
tty -s&&mesg n || true
```



> 重启

```
reboo
```

## Ubuntu20.04不能上网，VM虚拟机，ens33网卡没起来

1、使用 ifconfig 命令查看网卡信息，发现没有ens33网卡，有ens33网卡，上面的地址也获取的不对

```
ifconfig
```

2、使用 dhclient 命令重新获取 ip 地址，需要管理员权限，输入密码后确定执行，再使用 ifconfig 查看。

```
sudo dhclient
```

3、再使用 ifconfig 查询，如果 nes33 网卡还是没有出现，需要手动用命令启动 sudo ifconfig ens33 up ，再使用 ifconfig 查看。

```
sudo ifconfig ens33 up
```



## 如何给用户赋予操作某个文件夹的权限

```
su -l root

# 更改文件夹的用户为 user1
chown -R user1:user1 dirname

# 修改user1对文件夹的权限
chmod 755 dirname
```



## 如何设置或者更改时区在Ubuntu20.04

`timedatectl` 是用于查询和改变系统时钟以及市区，开启或者禁用时间同步的服务。



### 系统时区

`timedatectl` 是一个命令行程序，可以让您查看和更改系统的时间和日期。它在所有的基于现代system的Linux系统上都可用，包括Ubuntu20.04。



在调用 `timedatectl` 命令不使用任何参数时，`timedatectl` 将打印系统的时区。如果安装系统时没有设置时区，系统将使用默认的**UTC**时区。



以下`timedatectl`命令将会打印系统的时区和所在时区的当前时间。并显示系统时钟服务同步以及NTP服务的状态：

```
timedatectl
```

```
		        Local time: Wed 2020-05-06 19:33:20 UTC
           Universal time: Wed 2020-05-06 19:33:20 UTC
                 RTC time: Wed 2020-05-06 19:33:22    
                Time zone: UTC (UTC, +0000)           
System clock synchronized: yes                         
              NTP service: active                      
          RTC in local TZ: no 
```



上面的 `timedatectl`命令输出，可以注意到系统使用的UTC时区。如果这与你所在的时区不一致。可以继续使用`timedatectl`修改系统时区。



### 更改时区

更改时区之前，您需要找出与你当前时区匹配的时区的长名称。长时区的名称使用`地区/城市`格式作为命名。



如你不知道你所处位置的时区长名称，可以通过使用`timedatectl`命令的`list-timezones`选项列出全世界所有可用的时区。



通常`timedatectl`命令会打印所有时区。我们将`timedatectl`列出的时区名称通过管道传递给grep命令过滤时区名称。



在本教程中，我们将使用`亚洲/上海`的时区作为系统的时区。我将大概知道长时区的名称中将包含`shanghai`的字符。此时我么使用`shanghai`作为`grep`的关键词过滤即可找到上海的时区。



以下`timedatectl`命令打印所有时区，然后通过管道传递grep命令不区分大小写搜索包含括`shang`关键词的时区：

```
timedatectl list-timezones | grep -i shang
```

该命令将打印以下输出：

```
Asia/Shanghai
```



现在，已经找到所在位置的时区长名称。

我们就可以使用`timedatectl`的`set-timezone`选项设置系统的时区，并在`set-timezone`选项之后传递长时区名称。

请以root或[具有sudo权限的用户](https://www.myfreax.com/how-to-add-user-to-sudoers-in-ubuntu/)运行以下`timedatectl`命令设置系统的时区为`Asia/Shanghai`：



再次不带任何选项参数调用`timedatectl`命令，打印系统当前设置的时区即可：

```
timedatectl
```

```
               Local time: Wed 2020-05-06 15:41:42 EDT  
           Universal time: Wed 2020-05-06 19:41:42 UTC  
                 RTC time: Wed 2020-05-06 19:41:48      
                Time zone: America/New_York (EDT, -0400)
System clock synchronized: yes                         
              NTP service: active                      
          RTC in local TZ: no   

```





### 软链接修改系统时区

Linux系统使用`/etc/localtime`文件存储着系统的时区，它是一个[软链接](https://www.myfreax.com/how-to-create-a-sudo-user-on-ubuntu/)/[符号链接](https://www.myfreax.com/how-to-create-a-sudo-user-on-ubuntu/)文件。它指向`/usr/share/zoneinfo/`目录以及子目录下的时区文件。



这些时区文件以二进制的存储着时区的信息。当应用程序需要用户展示时区时。应用程序将读取`/etc/localtime`最终指向的二进制时区文件。



因此，我们还可以通过修改`/etc/localtime`[符号链接](https://www.myfreax.com/how-to-create-a-sudo-user-on-ubuntu/)最终指向的二进制时区文件来配置系统的时区。也可以使用`ls`命令查看当前时区。



以ls命令将打印`/etc/localtime`所有信息，你会看到`/etc/localtime`文件的类型是符号链接，并指向`/usr/share/zoneinfo/Hongkong`。

```
ls -al /etc/localtime
```

```
lrwxrwxrwx 1 root root 28 5月  12 19:03 /etc/localtime -> /usr/share/zoneinfo/Hongkong
```

现在已经确定了系统所有使用的时区。假如我们将时区更改为上海。可使用ln命令直接改变`/etc/localtime`指向的二进制时区文件修改系统时区。



如你不确定所在位置的时区名称，可使用`find . -iname "你时区关键字*"`命令搜索时区名称的关键字，`i`表示不区分大小写。

`find`命令将列出所有可能包含时区名称的关键字的二进制时区。这些文件都可用于使用`ln`命令设置系统的时区。



以下`ln`命令修改/创建文件`/usr/share/zoneinfo/Asia/Shanghai`到`/etc/localtime`的软链接：

```
sudo ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

如果如果你需要验证系统时区是否成功修改或者设置。可使用`date`命令打印当前系统的时间。当时区发生变化，`date`命令时间也将会更改。



以下date命令将会打印系统的当前时间：

```
date
```

