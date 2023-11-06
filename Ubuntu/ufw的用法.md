**ufw是Ubuntu自带的防火墙软件**

> 安装

```
sudo apt install ufw
```

> 启动

```
sudo ufw enable
```

> 开放端口

```
sudo ufw allow 2021
```

> 查看状态

```
sudo ufw status
```

> 删除端口

```
sudo ufw delete allow 8080
```

> 重载

```
sudo ufw reload
```

> 设置为开机自启

```
systemctl enable ufw
```

