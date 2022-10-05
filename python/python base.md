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



## 4、关于开代理后requests连接失败的问题解决方法

> 报错

requests.exceptions.ProxyError: HTTPConnectionPool(host=‘xx.xx.xx.xx’, port=808): Max retries exceeded with url: http://xxxxx (Caused by ProxyError(‘Cannot connect to proxy.’, NewConnectionError(’<urllib3.connection.HTTPConnection object at 0x7f6af4708f60>: Failed to establish a new connection: [Errno 111] Connection refused’,)))

> 解决方法 

两种方式:
`session = requests.Session()`
`session.trust_env = False`
`response = session.get('http://ff2.pw')`
或者:
`proxies = { "http": None, "https": None}`
`requests.get("http://ff2.pw", proxies=proxies)`
都可以绕过系统设置的代理

> 另一篇博客

requests模块为常用的Python http请求的模块。

如果需要使用代理，可以这样设置：

```python
import requests
proxies = {
  'http': 'http://10.10.1.10:3128',
  'https': 'http://10.10.1.10:1080',
}
requests.get('http://example.org', proxies=proxies)
```

但是有一点，如果没有在代码中指定使用代理，但是在Windows的IE中设置了代理服务器，则requests还是会使用代理。（被坑过，导致响应很慢）

如果在IE中设置了代理，但是在脚本中不需要使用代理，可以这样：

```python
proxies = {'http': None, 'https': None}
s.get('http://10.114.19.53:8080/job/SOP_Replay/', proxies=proxies)
```

这样的话，就可以确保requests不使用系统代理，直连服务器了。
