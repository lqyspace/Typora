[TOC]

# 2.x相比1.x的优势

- Tensorflow是学习深度学习的框架，定位是工具，帮助我们完成实验和任务，所以工具当然是越使用越简单的
- 2版本的难度不是很大，而是大大简化了建模的方法和步骤，比1版本更简单难度更小
- 终于把 Keras Api当作核心，使用起来一句话概述就是简单他妈给简单开门简单到家了
- 跨平台，各种现成模型，eager mode是的调试起来终于不再那么难受。
- 深度学习入门和进阶的最好的方法就是多练习多试验，看论文复现代码和调试的过程就是提升最快的途径。 



# TF基础

```python
import tensorflow as tf
import numpy as np
```

```python
tf.__version__
out: 
    '2.6.0'
```

```python
x = [[1.]]
m = tf.matmul(x, x)
m
out: 
    <tf.Tensor: shape=(1, 1), dtype=float32, numpy=array([[1.]], dtype=float32)>
```

```python
x = tf.constant([[1,9],[3,6]])
x
out:
    <tf.Tensor: shape=(2, 2), dtype=int32, numpy=
    array([[1, 9],
           [3, 6]])>
```

```python
x = tf.add(x, 1)
x
out:
    <tf.Tensor: shape=(2, 2), dtype=int32, numpy=
    array([[ 2, 10],
           [ 4,  7]])>
```

```
Tensor 是什么
当做是进行gpu加速运算的矩阵。

格式转换
```

```
x.numpy()
out:
	array([[ 2, 10],
       [ 4,  7]])
```

```
x = tf.cast(x, tf.float32)
x
out:
    <tf.Tensor: shape=(2, 2), dtype=float32, numpy=
    array([[ 2., 10.],
           [ 4.,  7.]], dtype=float32)>
```

```
x1 = np.ones([2,2])
x2 = tf.multiply(x1, 2)
x2
out:
    <tf.Tensor: shape=(2, 2), dtype=float64, numpy=
    array([[2., 2.],
           [2., 2.]])>
```



# 深度学习

机器学习流程：

- 数据获取

- 特征工程
- 建立模型
- 评估与应用

![image-20220715123030726](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151230750.png)

特征工程的应用：

- 数据特征决定了模型的上限
- 预处理和特征提取是最核心的
- 算法与参数选择决定了如何逼近这个上限



深度学习的应用：

- 图像识别（计算机视觉）
- 医学应用（检测细胞）
- 文本处理

![image-20220715124623432](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151246514.png)

![image-20220715125151183](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151251223.png)

![image-20220715125213907](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151252949.png)

机器学习常规套路：

- 收集数据并给定标签
- 训练一个分类器
- 测试，评估



常规算法：

- K近邻算法：
  - 计算已知类别数据集中的点与当前点的距离
  - 按照距离依次排序
  - 选取与当前点距离最小的k个点
  - 确定前k个点所在类别出现的频率
  - 返回前k个点出现频率最高的类别作为当前点的预测分类
- K近邻分析
  - KNN算法本身简单有效，是一种lazy-learning算法
  - 分类器不需要使用训练集进行训练，训练时间复杂度为0
  - KNN分类的计算复杂度和训练集中的文档数目成正比，也就是说，如果训练集中的文档数目总数为n，那么KNN的分类时间复杂度为O(n)
  - K值的选择，距离度量和分类决策规则是该算法的三个基本要素。
- 为什么K近邻不能用来图像分类
  - 背景主导是一个最大的问题，我们关注的却是主体（主要成分）
  - 如何才能让机器学习到哪些是最重要的成分呢？



# 神经网络基础

## 线性函数

![image-20220715154519148](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151545180.png)

![image-20220715164634939](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151646979.png)

![image-20220715164916071](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151649109.png)

**损失函数里面的 +1 就相当于 能够容忍出错的范围 **

![image-20220715165426058](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151654094.png)

![image-20220715165505872](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151655906.png)

![image-20220715165814065](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151658100.png)

![image-20220715165858201](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151658234.png)

![image-20220715170046950](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151700989.png)

![image-20220715184240279](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151842311.png)

![image-20220715184509630](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207151845674.png)

![QJG$JJD~}YX@TMR6D~_501H](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207152126649.png)

![image-20220715212624269](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207152126356.png)

![image-20220715212703427](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202207152127575.png)[]()