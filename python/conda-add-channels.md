# 如何给anaconda添加清华镜像

## 查看当前的镜像

```python
conda config --show channels
```

## 修改下载源

[anaconda](https://so.csdn.net/so/search?q=anaconda&spm=1001.2101.3001.7020)默认的各种包的下载源，全部在国外，下载速度慢，而且经常中断，所以需要配置国内安装的镜像，这样下载速度就很快了。

```python
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/menpo/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/


conda config --set show_channel_urls yes

```

注意：首次运行conda config会产生一个anacnoda的配置文件，这个配置文件和jupyter的配置文件一样，默认是不存在的。Windows为的默认位置为C://Users/username/.condarc，Linux/Mac为~/.condarc。添加上了上述镜像后，原先源仍然存在，文件中的-defaults就是原来的源。

## 清除添加的所有下载源

当我们想换回Anaconda的默认下载源时，把之前设置的移除就行了：

```python
conda config --remove-key channels
```

