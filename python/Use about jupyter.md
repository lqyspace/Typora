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

```
ipython kernel install --user --name=my-conda-env-kernel
jupyter notebook
```



然后就可以直接在 jupyter 里选择虚拟环境了

![image-20220529203307155](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205292033256.png)

或者

![image-20220529203356220](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205292033266.png)

