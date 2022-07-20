[TOC]
# pytorch 零散的笔记

## 1 torch.FloatTensor()
类型转换, 将list ,numpy转化为tensor。 以list -> tensor为例：

```python
print(torch.FloatTensor([1,2]))
# 输出: tensor([1., 2.])evaluate()

import torch
a=torch.FloatTensor([[1,2,3]，[4,5,6]])
b= torch.FloatTensor(2, 3)
print (a)
print (b)

# 输出
tensor([[1.,2., 3. ]，
       [4., 5., 6. ]])
tensor([[0., 0. ,0. ]，
        [0., 0., 0. ]])

```

## 2 torch.Tensor和torch.tensor的区别

在Pytorch中，Tensor和tensor都用于生成新的张量
```python
>>>  a = torch.Tensor([1, 2])
>>>  a
tensor([1., 2.])
>>> a=torch.tensor([1,2])
>>> a
tensor([1, 2])

```

### torch.Tensor()

torch.Tensor()是Python类，更明确的说，是默认张量类型torch.FloatTensor()的别名，torch.Tensor([1,2]) 会调用Tensor类的构造函数__init__，生成单精度浮点类型的张量

```python
>>> a=torch.Tensor([1,2])
>>> a.type()
'torch.FloatTensor'

```

### torch.tensor()
torch.tensor()仅仅是Python的函数，函数原型是：
```python
torch.tensor(data, dtype=None, device=None, requires_grad=False)

```

其中data可以是：list, tuple, array, scalar等类型。
torch.tensor()可以从data中的数据部分做拷贝（而不是直接引用），根据原始数据类型生成相应的torch.LongTensor，torch.FloatTensor，torch.DoubleTensor。

```python
>>> a = torch.tensor([1, 2])
>>> a.type()
'torch.LongTensor'


>>> a = torch.tensor([1., 2.])
>>> a.type()
'torch.FloatTensor'



>>> a = np.zeros(2, dtype=np.float64)
>>> a = torch.tensor(a)
>>> a.type()
torch.DoubleTensor

b = torch.rand(3,3)#得到的是floattensor值，
b = b.long()#得到的是longtensor值
 
```

## 3 python之TensorDataset和DataLoader
### 一 TensorDataset
TensorDataset 可以用来对 tensor 进行打包，就好像 python 中的 zip 功能。该类通过每一个 tensor 的第一个维度进行索引。因此，该类中的 tensor 第一维度必须相等. 另外：TensorDataset 中的参数必须是 tensor。

```python
import torch
from torch.utils.data import TensorDataset
from torch.utils.data import DataLoader

a = torch.tensor([[1, 2, 3], [4, 5, 6], [7, 8, 9], [1, 2, 3], [4, 5, 6], [7, 8, 9], [1, 2, 3], [4, 5, 6], [7, 8, 9], [1, 2, 3], [4, 5, 6], [7, 8, 9]])
b = torch.tensor([44, 55, 66, 44, 55, 66, 44, 55, 66, 44, 55, 66])
train_ids = TensorDataset(a, b) 

# 切片输出
print(train_ids[0:2])
# 输出
#(tensor([[1, 2, 3],
#        [4, 5, 6]]), tensor([44, 55]))
        
        
# 循环取数据
for x_train, y_label in train_ids:
    print(x_train, y_label)
    
# 输出
#tensor([1, 2, 3]) tensor(44)
#tensor([4, 5, 6]) tensor(55)
#tensor([7, 8, 9]) tensor(66)
# ... ...

    
# DataLoader进行数据封装
# batch_size=4 说明每4个数据为一个batch
train_loader = DataLoader(dataset=train_ids, batch_size=4, shuffle=True) # shuffle 是否打乱顺序
print(train_loader) 
# 输出<torch.utils.data.dataloader.DataLoader object at 0x000001F037EDB188>

for i, data in enumerate(train_loader, 1):  # 注意enumerate返回值有两个,一个是序号，一个是数据（包含训练数据和标签）
    x_data, label = data
    print(' batch:{0} x_data:{1}  label: {2}'.format(i, x_data, label))
    print(data)
    
# =========输出
batch:1 x_data:tensor([[4, 5, 6],
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9]])  label: tensor([55, 44, 55, 66])
[tensor([[4, 5, 6],
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9]]), tensor([55, 44, 55, 66])]
batch:2 x_data:tensor([[1, 2, 3],
        [7, 8, 9],
        [4, 5, 6],
        [1, 2, 3]])  label: tensor([44, 66, 55, 44])
[tensor([[1, 2, 3],
        [7, 8, 9],
        [4, 5, 6],
        [1, 2, 3]]), tensor([44, 66, 55, 44])]
 batch:3 x_data:tensor([[1, 2, 3],
        [7, 8, 9],
        [4, 5, 6],
        [7, 8, 9]])  label: tensor([44, 66, 55, 66])
[tensor([[1, 2, 3],
        [7, 8, 9],
        [4, 5, 6],
        [7, 8, 9]]), tensor([44, 66, 55, 66])]

```

### 二 DataLoader
DataLoader就是用来包装所使用的数据，每次抛出一批数据
```python
import torch
import torch.utils.data as Data
BATCH_SIZE = 5
# linspace, 生成1到10的10个数构成的等差数列
x = torch.linspace(1, 10, 10)
y = torch.linspace(10, 1, 10)

# 把数据放在数据库中
torch_dataset = Data.TensorDataset(x, y)
# 从数据库中每次抽出batch size个样本
loader = Data.DataLoader(dataset=torch_dataset,
                         batch_size=BATCH_SIZE, # x, y 是相差为1个数为10的等差数列, batch= 5, 遍历loader就只有两个数据
                         shuffle=False, # 不打乱顺序,便于查看
                         num_workers=0)

def show_batch():
    #for epoch in range(10):
    for step, (batch_x, batch_y) in enumerate(loader):
        # training
        print("steop:{}, batch_x:{}, batch_y:{}".format(step, batch_x, batch_y)) #方便输出

if __name__ == '__main__':
    show_batch()
    
    

# 输出=============================
steop:0, batch_x:tensor([1., 2., 3., 4., 5.]), batch_y:tensor([10.,  9.,  8.,  7.,  6.])
steop:1, batch_x:tensor([ 6.,  7.,  8.,  9., 10.]), batch_y:tensor([5., 4., 3., 2., 1.])
```

## 3 PyTorch里面的torch.nn.Parameter()

在刷官方Tutorial的时候发现了一个用法
self.v = torch.nn.Parameter(torch.FloatTensor(hidden_size)),
看了官方教程里面的解释也是云里雾里，于是在栈溢网看到了一篇解释，并做了几个实验才算完全理解了这个函数。首先可以把这个函数理解为类型转换函数，将一个不可训练的类型Tensor转换成可以训练的类型parameter并将这个parameter绑定到这个module里面(net.parameter()中就有这个绑定的parameter，所以在参数优化的时候可以进行优化的)，所以经过类型转换这个self.v变成了模型的一部分，成为了模型中根据训练可以改动的参数了。使用这个函数的目的也是想让某些变量在学习的过程中不断的修改其值以达到最优化。

作者：VanJordan
链接：https://www.jianshu.com/p/d8b77cc02410


## 4 torch.mul() 、 torch.mm() 及torch.matmul()的区别

1、torch.mul(a, b)是矩阵a和b对应位相乘，a和b的维度必须相等，比如a的维度是(1, 2)，b的维度是(1, 2)，返回的仍是(1, 2)的矩阵；
2、torch.mm(a, b)是矩阵a和b矩阵相乘，比如a的维度是(1, 2)，b的维度是(2, 3)，返回的就是(1, 3)的矩阵。
PS：更接地气来说区别就是点乘，和矩阵乘法的区别

```python
import torch

a = torch.rand(1, 2)
b = torch.rand(1, 2)
c = torch.rand(2, 3)

print(torch.mul(a, b))  # 返回 1*2 的tensor
print(torch.mm(a, c))   # 返回 1*3 的tensor
print(torch.mul(a, c))  # 由于a、b维度不同，报错
```

torch.bmm()

torch.matmul()

torch.bmm()强制规定维度和大小相同

torch.matmul()没有强制规定维度和大小，可以用利用广播机制进行不同维度的相乘操作


作者：top_小酱油
链接：https://www.jianshu.com/p/e277f7fc67b3


## 5 交叉熵损失函数CrossEntropyLoss()详解

### 引言
在使用pytorch深度学习框架，计算损失函数的时候经常会遇到这么一个函数：
> nn.CrossEntropyLoss()


该损失函数结合了nn.LogSoftmax()和nn.NLLLoss()两个函数。它在做分类（具体几类）训练的时候是非常有用的。在训练过程中，对于每个类分配权值，可选的参数权值应该是一个1D张量。当你有一个不平衡的训练集时，这是非常有用的。那么针对这个函数，下面将做详细的介绍。

### 什么是交叉熵

**交叉熵主要是用来判定实际的输出与期望的输出的接近程度**，为什么这么说呢，举个例子：在做分类的训练的时候，如果一个样本属于第K类，那么这个类别所对应的的输出节点的输出值应该为1，而其他节点的输出都为0，即[0,0,1,0,….0,0]，这个数组也就是样本的Label，是神经网络最期望的输出结果。也就是说用它来衡量网络的输出与标签的差异，利用这种差异经过反向传播去更新网络参数。


### 交叉熵原理

在说交叉熵之前，先说一下**信息量**与**熵**。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192329833.png)

```python
import torch
import torch.nn as nn
x_input=torch.randn(3,3)#随机生成输入 
print('x_input:\n',x_input) 
y_target=torch.tensor([1,2,0])#设置输出具体值 print('y_target\n',y_target)

#计算输入softmax，此时可以看到每一行加到一起结果都是1
softmax_func=nn.Softmax(dim=1)
soft_output=softmax_func(x_input)
print('soft_output:\n',soft_output)

#在softmax的基础上取log
log_output=torch.log(soft_output)
print('log_output:\n',log_output)

#对比softmax与log的结合与nn.LogSoftmaxloss(负对数似然损失)的输出结果，发现两者是一致的。
logsoftmax_func=nn.LogSoftmax(dim=1)
logsoftmax_output=logsoftmax_func(x_input)
print('logsoftmax_output:\n',logsoftmax_output)

#pytorch中关于NLLLoss的默认参数配置为：reducetion=True、size_average=True
nllloss_func=nn.NLLLoss()
nlloss_output=nllloss_func(logsoftmax_output,y_target)
print('nlloss_output:\n',nlloss_output)

#直接使用pytorch中的loss_func=nn.CrossEntropyLoss()看与经过NLLLoss的计算是不是一样
crossentropyloss=nn.CrossEntropyLoss()
crossentropyloss_output=crossentropyloss(x_input,y_target)
print('crossentropyloss_output:\n',crossentropyloss_output)

# 输出======================
x_input:
 tensor([[ 2.8883,  0.1760,  1.0774],
        [ 1.1216, -0.0562,  0.0660],
        [-1.3939, -0.0967,  0.5853]])
y_target
 tensor([1, 2, 0])
soft_output:
 tensor([[0.8131, 0.0540, 0.1329],
        [0.6039, 0.1860, 0.2102],
        [0.0841, 0.3076, 0.6083]])
log_output:
 tensor([[-0.2069, -2.9192, -2.0178],
        [-0.5044, -1.6822, -1.5599],
        [-2.4762, -1.1790, -0.4970]])
logsoftmax_output:
 tensor([[-0.2069, -2.9192, -2.0178],
        [-0.5044, -1.6822, -1.5599],
        [-2.4762, -1.1790, -0.4970]])
nlloss_output:
 tensor(2.3185)
crossentropyloss_output:
 tensor(2.3185)
```
通过上面的结果可以看出，直接使用pytorch中的loss_func=nn.CrossEntropyLoss()计算得到的结果与softmax-log-NLLLoss计算得到的结果是一致的。


## 6 nn.Softmax(dim) 的理解
![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192329842.png)


作者：不太聪明的亚子
链接：https://www.jianshu.com/p/3d63f36cc960/

## 7 pytorch中model.parameters()函数
```python
model = ConvNet() #创建了一个实例
optimizer = optim.Adam(model.parameters(), lr=learning_rate) 
#优化函数，model.parameters()为该实例中可优化的参数，lr为参数优化的选项（学习率等）
for name, param in model.named_parameters(): #查看可优化的参数有哪些
  if param.requires_grad:
    print(name)

```

## 8 torch.optim优化算法理解之optim.Adam()

torch.optim是一个实现了多种优化算法的包，大多数通用的方法都已支持，提供了丰富的接口调用，未来更多精炼的优化算法也将整合进来。

为了使用torch.optim，需先**构造一个优化器对象Optimizer**，用来保存当前的状态，并能够根据计算得到的梯度来更新参数。

要构建一个优化器optimizer，你必须给它一个可进行迭代优化的包含了所有参数（所有的参数必须是变量s）的列表。 然后，您可以指定程序优化特定的选项，例如学习速率，权重衰减等。

```python
optimizer = optim.SGD(model.parameters(), lr = 0.01, momentum=0.9)
optimizer = optim.Adam([var1, var2], lr = 0.0001)
self.optimizer_D_B = torch.optim.Adam(self.netD_B.parameters(), lr=opt.lr, betas=(opt.beta1, 0.999))
```

Optimizer还支持指定每个参数选项。 只需传递一个可迭代的dict来替换先前可迭代的Variable。dict中的每一项都可以定义为一个单独的参数组，参数组用一个params键来包含属于它的参数列表。其他键应该与优化器接受的关键字参数相匹配，才能用作此组的优化选项。

```python
optim.SGD([
                {'params': model.base.parameters()},
                {'params': model.classifier.parameters(), 'lr': 1e-3}
            ], lr=1e-2, momentum=0.9)
```

如上，model.base.parameters()将使用1e-2的学习率，model.classifier.parameters()将使用1e-3的学习率。0.9的momentum作用于所有的parameters。

> 优化步骤

所有的优化器Optimizer都实现了step()方法来对所有的参数进行更新，它有两种调用方法：

    optimizer.step()



这是大多数优化器都支持的简化版本，使用如下的backward()方法来计算梯度的时候会调用它。

```python
for input, target in dataset:
    optimizer.zero_grad()
    output = model(input)
    loss = loss_fn(output, target)
    loss.backward()
    optimizer.step()
```

---

    optimizer.step(closure)

一些优化算法，如共轭梯度和LBFGS需要重新评估目标函数多次，所以你必须传递一个closure以重新计算模型。 closure必须清除梯度，计算并返回损失。

```python
for input, target in dataset:
    def closure():
        optimizer.zero_grad()
        output = model(input)
        loss = loss_fn(output, target)
        loss.backward()
        return loss
    optimizer.step(closure)
```

具体的关于Adam算法的部分，参考：
[torch.optim优化算法理解之optim.Adam()_KGzhang的博客-CSDN博客_torch.optim.adam](https://blog.csdn.net/kgzhang/article/details/77479737)

## 9 torch代码解析 为什么要使用optimizer.zero_grad()

optimizer.zero_grad()意思是把梯度置零，也就是把loss关于weight的导数变成0.

在学习pytorch的时候注意到，对于每个batch大都执行了这样的操作：
```python
        # zero the parameter gradients
        optimizer.zero_grad()
        # forward + backward + optimize
        outputs = net(inputs)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()
```

## 10 numpy常用函数之random.normal函数

作用：

    生成高斯分布的概率密度随机数

```js
loc：float
    此概率分布的均值（对应着整个分布的中心centre）
scale：float
    此概率分布的标准差（对应于分布的宽度，scale越大越矮胖，scale越小，越瘦高）
size：int or tuple of ints
    输出的shape，默认为None，只输出一个值
```

举例：
```python
nd1 = np.random.normal(loc=1,scale=2,size=2)#array([-0.46982446, -1.28956852])
```