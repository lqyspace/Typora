[TOC]

# 在Jupyter中使用虚拟环境

## 安装虚拟环境

```
conda create -n my-conda-env 
conda activate my-conda-env
```



## 在虚拟环境中安装ipykernel

```
conda install -n my-conda-env ipykernel
```

```python
python -m ipykernel install --user --name=my-conda-env-kernel
jupyter notebook
```

或者

```
conda install -n my-conda-env nb_conda
conda install -n my-conda-env jupyter_contrib_nbextensions
conda install -n my-conda-env packaging
```

然后就可以直接在 jupyter 里选择虚拟环境了

![image-20220529203307155](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205292033256.png)

或者

![image-20220529203356220](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205292033266.png)



## 报错ModuleNotFoundError: No module named 'jupyter_nbextensions_configurator'

启动Anaconda中的Jupyter Notebook的时候，出现如下错误：

```
ModuleNotFoundError: No module named 'jupyter_nbextensions_configurator'
解决方案：
```

（1）打开在Anaconda Prompt

（2）输入以下命令：

```
python -m pip install jupyter_nbextensions_configurator
```

（3）重新打开在Jupyter Notebook即可



# 调用远程jupyter

推荐博客：

配置远程服务器用这个：

https://zhuanlan.zhihu.com/p/375369748

链接远程服务器推荐下面这个：

[https://blog.csdn.net/qq_18256855/article/details/125439096](https://blog.csdn.net/qq_18256855/article/details/125439096)


# 添加pip的清华镜像

参考博客：https://blog.csdn.net/Blackrosetian/article/details/127037267
[https://blog.csdn.net/Blackrosetian/article/details/127037267](https://blog.csdn.net/Blackrosetian/article/details/127037267)