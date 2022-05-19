[TOC]

# Git 远程仓库

Git并不像SVN那样有个中心服务器。

目前我们使用的 git 命令都是在本地上执行的，如果你想通过 Git 分享你的代码或者与其他的开发人员合作。你就需要将数据放到一台其他的开发人员可以连接的服务器上。



## 添加远程库

要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，命令格式如下：

```
git remote add [shortname] [url]
```



可以使用以下命令生成 SSH Key：

```
ssh-keygen -t rsa -C "youremail@example.com"
```

后面的 youremail@example.com 改为你在 github 上注册的邮箱，之后会按要求确认路径和输入密码，我们这使用一路的默认回车就行。成功的话会在 ~/下生成 .ssh 文件夹，进去，打开 id_rsa.pub，复制里面的 key。

回到 github 上，进入 Account Setting（账户配置），左边选择 SSH Keys，Add SSH Key，title随便填，粘贴上你电脑上生成的 key。



为了验证是否成功，输入以下命令：

```
ssh -T git@github.com
Hi *********************,but Github does not provide shell access.
```

以上说明我们已经成功连上 Github。



## 查看当前的远程库

要查看当前配置里有哪些远程仓库，可以使用命令：

```
git remote

git remote -v # 执行时加上 -v 参数，可以看到每个别名实际的链接地址
```



## 提取远程仓库

Git 有两个命令用来提取远程仓库的更新。

1、从远程仓库下载新分支和数据：

```
git fetch
```

该命令执行完以后需要执行 git merge 远程分支到你所在的分支。



2、从远程仓库提取数据并尝试合并到分支：

```
git pull
```





## 推送到远程仓库

推送你的新分支与数据到远程仓库：

```
git push [alias] [branch]
```

以上命令将你的 [branch] 分支推送成为 [alias] 远程仓库上的 [branch] 分支。



## 删除远程仓库

删除远程仓库：

```
git remote rm [别名]
```

```
$ git remote -v
origin  git@github.com:WongJay/w3cschool.cn.git (fetch)
origin   git@github.com:WongJay/w3cschool.cn.git (push)

$ git remote add origin2 git@github.com:WongJay/w3cschool.cn.git
$ git remote -v
origin   git@github.com:WongJay/w3cschool.cn.git (fetch)
origin   git@github.com:WongJay/w3cschool.cn.git (push)
origin2   git@github.com:WongJay/w3cschool.cn.git (fetch)
origin2  git@github.com:WongJay/w3cschool.cn.git (push)

$ git remote rm origin2
$ git remote -v
origin  git@github.com:WongJay/w3cschool.cn.git (fetch)
origin   git@github.com:WongJay/w3cschool.cn.git (push)
```





