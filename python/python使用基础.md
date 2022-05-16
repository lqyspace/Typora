[TOC]



# python 使用基础

## 1、在conda下激活虚拟环境，仍然出现pip安装包到全局的情况

### 问题查找

使用 `pip -V` 或者 `pip --version` 可以查看你此时用的是不是虚拟环境里面的 `pip`

### 解决问题

在 pip 前面加上 `python -m`

`python -m pip install <package>`

**==注==**：如果你是用 `pip -V` 发现你是用的 正是虚拟环境下的 pip ，那么你就可以直接使用 `pip install <package>` 进行下载。



## 2、ERROR: Command errored out with exit status 1: 

原因：python版本和第三方要求的python版本不一致



**此时切记：**
python版本不一致的时候，不要用remove卸载python重装！！！这样会导致所有依赖python的都丢失。



**要用** `conda install` 来覆盖安装： `conda install python=3.8`.

 

## 3、ImportError: cannot import name 'cygrpc' from 'grpc._cython' (D:\Anaconda3\install\envs\PARL\lib\site-packages\grpc\_cython\__init__.py)

如果报这样的错误，可以把报错的模块先删除，然后再重新安装。

