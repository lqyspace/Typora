# RMS-Hrec复现

- python==3.8
- pytorch
  - `conda install pytorch==1.12.1 torchvision==0.13.1 torchaudio==0.12.1 cudatoolkit=11.6 -c pytorch -c conda-forge`
- dgl
  - `conda install -c dglteam/label/cu116 dgl`
- 其他的包正常装，缺啥补啥
- HAN.py有两处错误需要修改：
  - 1、86行的g.adj改为g.adj_external
  - 2、198行的NodeDataLoader改为DataLoader



# 结果

代码支持的数据集：

- yelp_data
- douban_movies

代码运行：

```python
User DQN loss:  0.02733464725315571
Generated meta-path set: [[['2', '4', '8', '1'], ['2', '1'], ['2', '1', '6']], [['1', '2']]]
User DQN loss:  0.022991318255662918
Generated meta-path set: [[['2', '4', '8', '1'], ['2', '1'], ['2', '4', '8', '1', '6'], ['2', '1', '6']], [['1', '2']]]
User DQN loss:  0.2710060179233551
Generated meta-path set: [[['2', '4', '8', '1'], ['2', '1'], ['2', '4', '8', '1', '6'], ['2', '1', '6']], [['1', '2']]]
Meta-path:  ['2', '3', '7', '1', '6'] Density:  0.6184054006292885 #Edges:  162390000 

LIBXSMM_VERSION: main-1.17-3659 (25693771)
LIBXSMM_TARGET: clx [11th Gen Intel(R) Core(TM) i7-11700F @ 2.50GHz]
Registry and code: 13 MB
Command: /home/xiaoyun/anaconda3/envs/rms-hrec1/bin/python -u /tmp/pycharm_project_165/train_fm.py --data_name yelp_data --episode 2000 
Uptime: 690.997911 s

Process finished with exit code 134
```





# 新环境

- python==3.7.0

  - 安装了以后注意升级pip
  - `python -m pip install --upgrade pip`

- pytorch

  - `conda install pytorch==1.7.0 torchvision==0.8.0 torchaudio==0.7.0 cudatoolkit=11.0 -c pytorch`
  - [pytorch旧版本](https://pytorch.org/get-started/previous-versions/)

- dgl

  - `conda install -c dglteam dgl-cuda11.0==0.7.0 -n rms-hrec`
  - 参考：[cuda下安装dgl](https://blog.csdn.net/qq_30049011/article/details/120763171)
  - [dgl对应的版本号](https://conda.anaconda.org/dglteam/linux-64)

- 其他包正常装，缺啥补啥

  - ```python
    python -m pip install pandas
    python -m pip install requests
    python -m pip install urllib3==1.26.6
    python -m pip install dataclasses future
    python -m pip install gym
    python -m pip install scikit-learn
    python -m pip install matplotlib
    ```

- 代码可以正常运行

- 运行过程的报错：

  ![image-20230902220242571](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202309022202725.png)

报错的原因：没有model文件夹，新建一个就行

- 核心已转存
  - 可能是内存不足的问题
