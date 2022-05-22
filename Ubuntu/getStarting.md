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

