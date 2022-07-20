[TOC]

# GitHub与Git

## 基本概念

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341256.png)

![视频格式](D:/Boostnote%E7%AC%94%E8%AE%B0/img/github/2.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341279.png)

![视频格式](D:/Boostnote%E7%AC%94%E8%AE%B0/img/github/4.png)

## Git 相关命令

> 1、安装Git

已安装windows版，待需要时再安装其他版本

> 2、使用Git Bash

1、创建一个文件夹，作为一个仓库，本人创建的文件夹地址：D:\WORK\GitHubRepository

2、同时在这个目录下打开git Bash，可以在这个文件夹里创建一个仓库，例如：test。
![视频格式](D:/Boostnote%E7%AC%94%E8%AE%B0/img/github/5.png)

3、对这个test进行git初始化
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341295.png)
```go
git init # 初始化仓库
git status # 查看仓库所有文件状态
git status <file> # 查看某一个文件的状态
git config --list # 查看config信息
# 为计算机上的每个仓库设置Git用户名
git config --global user.name "XiaoYun"
# 确认您设置了正确的用户名
git config --global user.name
> XiaoYun

# 为计算机的一个仓库设置Git用户名
# 将当前的工作目录更改为您想要在其中配置与Git提交关联的名称的本地仓库
git config user.name "XiaoYun"
#确认您设置了正确的是用户名
git config user.name
> XiaoYun

git add <file> # 记住，一定使用这个命令添加文件，这样git才可以进行追踪，一定要确保提前有这个文件，否则无法 git add；可以提前使用touch、vim等命令创建的文件。
或者
git add . # 提交所有文件到暂存区

```
使用vi，touch等命令时相当于是在工作区；使用git add 相当于是将工作区提交到暂存区；然后使用git commit 相当于是将暂存区提交到仓库。

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341685.png)

4、修改文件
当你对仓库中的文件修改后，你使用 git statsu 命令会出现一下命令：
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341747.png)

然后使用 git add 命令就可以对修改后的文件重新提交
或者
使用 git restore 命令丢弃工作区中的修改。

使用 git commit -m "提交描述" 。

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341086.png)


5、删除文件
当你删除仓库中的每一个文件后：
- 使用 git status 命令来查看状态。
- 使用 git add/rm <文件名> 来更新提交的删除操作
- 使用 git restore <文件名> 来丢弃你的删除
- 使用 git commit -m "提交描述"

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341093.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341108.png)

## Git远程仓库

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341500.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341640.png)

### Git克隆操作

> 目的

将远程仓库（github对应的项目）复制到本地

> 代码

```go
git clone https://github.com/lqyspace/myrepo.git
```

> 在工作区编写文件

1、首先创建文件 index.php
    
    vi index.php

2、添加到暂存区

    git add index.php

3、添加到仓库

    git commit -m "提交描述"

4、添加到远程仓库

    git push

### 为什么无法远程同步
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341951.png)

## GitHub Pages搭建网站
参考：
[GitHub Pages 文档 - GitHub Docs](https://docs.github.com/cn/pages)

## GitHub自定义搜索

> 1、收藏量-stars：

例：输入：stars:>=500，意为匹配收藏量超过500的项目。

> 2、复刻量-forks：

例：输入：forks:>=500，意为匹配复刻量超过5000的项目。

> 3、关注者-followers：

例：输入：followers:>=500，意为匹配关注者数量超过5000的项目。

> 4、计算机语言-language：

例：输入：language:Python，意为匹配python语言完成的项目。

> 5、地域-location：

例：输入：location:chain，意为匹配中国境内的项目。

> 6、姓名搜索-in:fullname:

例：输入：Ben in:fullname，意为搜索名字里面

# Git使用
## 版本控制
![视频格式](D:/Boostnote%E7%AC%94%E8%AE%B0/img/github/15.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341168.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341515.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341771.png)

## Git环境配置
```python
git config --list 或 git config -l # 查看所有配置
git config --system --list # 查看系统配置
giit config --global -l # 查看用户配置

```
> 设置用户与邮箱（必要）

```js
git config --global user.name "xiaoyun"
git config --global user.email "15543495640m@sina.cn"
```
注：
全局环境配置位置：安装目录下的etc文件夹中的 gitconfig 文件，如：D:\WORK\Git\install\Git\etc\gitconfig
用户环境配置位置：电脑C盘目录下，用户目录的 .gitconfig 文件，如：C:\Users\XiaoYun\.gitconfig

## Git基本理论
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341776.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341573.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341268.png)
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341075.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341924.png)

## Git项目搭建
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341278.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341855.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341687.png)

## Git文件操作
![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341697.png)

注：部分命令已经更新
例如：在 Modified 模式下，使用 git restore 覆盖当前修改。 

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341856.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341161.png)

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341812.png)

## Git分支
```js
git branch // 列出所有本地分支

git branch -r // 列出所有远程分支

git branch [branch-name] // 新建一个分支，但依然停留在当前分支

git checkout -b [branch] // 新建一个分支，并切换到该分支

git merge [branch]// 合并指定分支到当前分支

git branch -d [branch-name]// 删除分支

// 删除远程分支
git push origin --delete [branch-name]
git branch -dr [remote/branch]
```

![视频格式](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192341412.png)