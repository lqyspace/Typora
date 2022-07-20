[TOC]
# 入门 ——基本函数
***
```python
import tensorflow as tf
```
## tf.constant()

### tensorflow2.x版本

    tf.constant(value,shape,dtype=None,name=None)
    释义：生成常量

- value：值
- shape：形状
- dtype：数据类型
- name：名称

> 示例1

```python
tensor = tf.constant([1,2,3,4,5,6])
# 输出结果：tf.Tensor([1 2 3 4 5 6], shape=(6,), dtype=int32)
print(tensor)



tensor = tf.constant([1,2,3,4,5,6],shape=(2,3))
"""
输出结果：
tf.Tensor(
[[1 2 3]
 [4 5 6]], shape=(2, 3), dtype=int32)
"""
print(tensor)



tensor = tf.constant(-1.0,shape=[2,3])
"""
输出结果：
tf.Tensor(
[[-1. -1. -1.]
 [-1. -1. -1.]], shape=(2, 3), dtype=float32)
"""
print(tensor)
```

> 示例2

```python
import numpy as np 
import tensorflow as tf 

# 无论是此处的数多于6还是少于6，都会报错，必须等于6
t = tf.constant([1,2,3,4,5,6],shape=(2,3))
print(t)
print(t[:,1:])
print(t[:,:1])
print(t[...,1])
print(t[:,1])
print(t[1][2])

----输出结果------
tf.Tensor(
[[1 2 3]
 [4 5 6]], shape=(2, 3), dtype=int32)
tf.Tensor(
[[2 3]
 [5 6]], shape=(2, 2), dtype=int32)
tf.Tensor(
[[1]
 [4]], shape=(2, 1), dtype=int32)
tf.Tensor([2 5], shape=(2,), dtype=int32)
tf.Tensor([2 5], shape=(2,), dtype=int32)
tf.Tensor(6, shape=(), dtype=int32)

```

```python
#operations 
print(t+10) 
print(tf.square(t))
print(t @ tf.transpose(t)) # t * t.T 


-----输出结果------
tf.Tensor(
[[11 12 13]
 [14 15 16]], shape=(2, 3), dtype=int32)
tf.Tensor(
[[ 1  4  9]
 [16 25 36]], shape=(2, 3), dtype=int32)
tf.Tensor(
[[14 32]
 [32 77]], shape=(2, 2), dtype=int32)
 
```

```python
# numpy conversion 
print(t.numpy()) #转换为numpy的一个实例
print(np.square(t)) # 直接使用numpy运算
np_t = np.array([[1, 2, 3],[4, 5, 6]])
print(tf.constant(np_t)) #将numpy的数组变为tensorflow的张量
np_t = np.array([1,2,3,4,5,6])
print(tf.constant(np_t,shape=(2,3)))

------输出结果--------
[[1 2 3]
 [4 5 6]]
[[ 1  4  9]
 [16 25 36]]
tf.Tensor(
[[1 2 3]
 [4 5 6]], shape=(2, 3), dtype=int32)
tf.Tensor(
[[1 2 3]
 [4 5 6]], shape=(2, 3), dtype=int32)

```

```python
# Scalars (0维数组，标量)
t = tf.constant(2.146)
print(t.numpy())
print(t)
print(t.shape)

-------输出结果--------
2.146
tf.Tensor(2.146, shape=(), dtype=float32)
()

```

### tensorflow1.x 版本

**不作详细介绍，详情如下：**


[TensorFlow创建常量(tf.constant)详解](https://blog.csdn.net/csdn_jiayu/article/details/82155224?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)

## tf.cast()

    tf.cast(x, dtype,name=None)
    释义：数据类型转换

- x：输入张量
- dtype：转换数据类型
- name：名称
```python
import tensorflow as tf

X = tf.constant([1,2,3,4,5], dtype=tf.int32)
Y = tf.cast(X, dtype=tf.float32)
print(X)
print(Y)

-----输出结果--------
tf.Tensor([1 2 3 4 5], shape=(5,), dtype=int32)
tf.Tensor([1. 2. 3. 4. 5.], shape=(5,), dtype=float32)

```

## tf.squeeze()

    tf.squeeze(input,axis=None,name=None)
    释义：该函数返回一个张量，这个张量是将原始input中所有维度为1的那些维度都删掉的结果

- input：原张量
- axis：可以指定要删掉的为1的维度。此处要注意指定的维度必须确保其是1，否则会报错
- name：名称

```python
>>>y = tf.squeeze(inputs, [0, 1], name='squeeze')
>>>ValueError: Can not squeeze dim[0], expected a dimension of 1, got 32 for 'squeeze' (op: 'Squeeze') with input shapes: [32,1,1,3].
```

```python
#  't' 是一个维度是[1, 2, 1, 3, 1, 1]的张量
tf.shape(tf.squeeze(t))   # [2, 3]， 默认删除所有为1的维度
 
# 't' 是一个维度[1, 2, 1, 3, 1, 1]的张量
tf.shape(tf.squeeze(t, [2, 4]))  # [1, 2, 3, 1]，标号从零开始，只删掉了2和4维的1

```

```python
t = tf.constant([[1,2,3,4,5]])
print(t)
print(tf.squeeze(t))
print(tf.squeeze(t,axis=[0]))
print(tf.shape(tf.squeeze(t)))

-------输出结果--------
tf.Tensor([[1 2 3 4 5]], shape=(1, 5), dtype=int32)
tf.Tensor([1 2 3 4 5], shape=(5,), dtype=int32)
tf.Tensor([5], shape=(1,), dtype=int32)
```

## tf.nn.softmax()

    含义：softmax简单来说就是把一个N*1的向量归一化为（0，1）之间的值，由于其中采用指数运算，使得向量中数值较大的两特征更加明显。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192330400.png)

## tf.convert_to_tensor(arg,dtype=tf.float32)

该函数将各种类型的python对象转换为张量对象，它接收张量对象，数字数组，python列表和python标量

```python
import numpy as np
 
def my_func(arg):
  arg = tf.convert_to_tensor(arg, dtype=tf.float32)
  return tf.matmul(arg, arg) + arg
 
# The following calls are equivalent.
value_1 = my_func(tf.constant([[1.0, 2.0], [3.0, 4.0]]))
value_2 = my_func([[1.0, 2.0], [3.0, 4.0]])
value_3 = my_func(np.array([[1.0, 2.0], [3.0, 4.0]], dtype=np.float32))

```

## tf.random.set_seed()
> 作用

    设置全局随机种子

具体内容参见：
[tf.random.set_seed用法](https://blog.csdn.net/qq_36201400/article/details/108512368)

## tf.GradientTape

> 作用

tf.GradientTape定义在tensorflow/python/eager/backprop.py文件中，从文件路径也可以大概看出，GradientTape是eager模式下计算梯度用的，而eager模式（eager模式的具体介绍请参考文末链接）是TensorFlow 2.0的默认模式，因此tf.GradientTape是官方大力推荐的用法.

具体用法参见：
[tf.GradientTape详解：梯度求解利器](https://blog.csdn.net/guanxs/article/details/102471843)

## tf.equal()
> 作用

    逐个元素进行扫描判断，相等就是True，不相等就是False。

由于是逐个扫描判断，所以两个张量的维度要一致

```python
a = [[1,2,3],[4,5,6]]

b = [[1,1,3],[4,4,6]]

tf.equal(a,b)

out[1]:
<tf.Tensor: shape=(2, 3), dtype=bool, numpy=
array([[ True, False,  True],
       [ True, False,  True]])>
       



tf.cast(_,dtype=tf.int32)

out[2]:
<tf.Tensor: shape=(2, 3), dtype=int32, numpy=
array([[1, 0, 1],
       [1, 0, 1]])>

```

## tf.losses.categorical_crossentropy(y_,y)

交叉熵损失函数，其中，y_是标准答案，y是预测答案

![](D:/Boostnote%E7%AC%94%E8%AE%B0/img/tensorflow/5.png)

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192330425.png)