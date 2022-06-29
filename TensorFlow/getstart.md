[TOC]

# Tensorflow2.0

## 简介

一、tf.keras 

构建和训练模型的核心高级API。

主要包含两部分：

- 单输入单输出 Sequential 顺序模型
- 函数式API



二、Eager模式与自定义的训练

直接迭代和直观调试，Eager模式下求解梯度与自定义训练

主要包含两部分：

- Eager模式
  - 直接迭代和直观调试
- tf.GradientTape
  - 求解梯度，自定义训练逻辑
- tf.data
  - 加载图片数据与结构化数据
- tf.function
  - 自动图运算



三、模型

- 卷积神经网络
- 多输出卷积神经网络综合实例
- 迁移学习

- 模型的保存与可视化
- Tensorboard可视化



四、安装

> CPU版本安装

```
pip install tensorflow -i https://pypi.doubanio.com/simple/
pip install pandas matplotlib notebook -i https://pypi.doubanio.com/simple/
```

> GPU安装

GPU版本有两个依赖库Cudatoolkit和Cudnn，当我们使用conda安装的时候，他会自动帮助我们安装这两个库。

```
conda install tensorflow-gpu=2.6.0 -n myenv
```

为什么是2.6.0,因为tensorflow更新的版本比较慢，目前2022年最新的版本仍然是2.6.0。

然后安装其他的辅助库：

```
pip install pandas matplotlib notebook keras==2.6.0 -i https://pypi.doubanio.com/simple/
```



## tf.keras 概述

官方推荐使用搭建网络。

> 相关命令

```python
import tensorflow as tf
# 版本   
print(tf.__version__)
# 检测是否可用
print(tf.test.is_gpu_available())
```

> 逻辑回归 

sigmod函数是一个概率分布函数， 给定某个输入，它将输出一个概率值。

平方差所惩罚的是与损失为同一个数量级的情形。

对于分类问题，使用交叉熵损失函数会更有效，交叉熵会输出一个更大的损失

> softmax多分类

在 tf.keras 里，对于多分类问题的使用 categorical_crossentropy 和 sparse_categorical_crossentropy 来计算 softmax 交叉熵。



> Fashion MNIST 数据集

Fashion MNIST 的作用是成为经典 MNIST 数据集的简易替代。

MNIST 数据集包含手写数字（0,1,2等）的图像，Fashion MNIST 比常规 MNIST 手写数据集更具有挑战性。

这两个数据集都相对较小，用于验证某个算法是否能够如期正常运行。他们都是测试和调试代码的良好起点。

Fashion MNIST数据集包含70000张灰度图像，涵盖10个类别。

可以从TensorFlow直接访问Fashion MNIST，只需导入和加载数据即可。

> 学习速率

梯度就是表明损失函数相对参数的变化率。

对梯度进行缩放的参数被称为学习速率（learning rate）。

在调整学习速率时，既需要使其足够小，保证不至于发生超调，也要保证他足够大，以使损失函数能够尽快下降，从而可以通过较少次数的迭代更快地完成学习任务。

> 反向传播算法

前馈时，从输入开始，逐一计算每个隐含层的输出，直到输出层。

然后开始计算导数，并从输出层经各隐含层逐一反向传播。为了减少计算量，还需要对所有已完成计算的元素进行复用，这便是反向传播算法名称的由来。

> 常见的优化函数

- 优化器（optimizer）是编译模型的所需的两个参数之一。你可以先实例化一个优化器对象，然后将它传入 model.compile()，或者你可以通过名称来调用优化器。在后一种情况下，将使用优化器的默认参数。
- SGD（随机梯度下降优化器）：随机梯度下降优化器SGD和mini-batch是同一个意思，抽取 m 个小批量（独立同分布）样本，通过计算他们平均梯度均值。
  - lr：float>=0，学习率
  - momentum：float>=0，参数，用于加速SGD在相关方向上前进，并抑制震荡。
  - decay：float>=0，每次参数更新后学习率衰减值
  - nesterov：boolean，是否使用Nesterov动量
- RMSprop：经验上，RMSprop被证明有效且实用的深度学习网络优化算法
  - RMSprop增加了一个衰减系数来控制历史信息的获取多少
  - RMSprop会对学习中进行衰减
- Adam优化器：
  - Adam算法可以看成是修正后的Momentum+RMSpprop算法
  - Adam通常被认为对超参数的选择相当鲁棒
  - 学习率建议为0.001
  - Adam是一种可以替代传统随机梯度下降过程的一阶优化算法，它能基于训练数据迭代地更新神经网络权重。
  - Adam通过计算梯度的一阶矩估计和二阶矩估计而为不同的参数设计独立的自适应性学习率
    - lr：float>=0，学习率
    - beta_1：float，0<beta<1，通常接近于1.
    - beta_2：float，0<beta<1，通常接近于1.
    - decay：flaot>=0，每次参数更新后学习率衰减值。
