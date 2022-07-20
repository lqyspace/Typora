[TOC]

# python内置函数

## all()函数
### 描述

all() 函数用于判断给定的可迭代参数 iterable 中的所有元素是否都为 TRUE，如果是返回 True，否则返回 False。

元素除了是 0、空、None、False 外都算 True。

```python
# 函数等价于
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
    
# 注：python2.5以上版本可用
```

### 语法

    all(iterable)

### 参数

- iterable -- 元组或列表


### 返回值

- 如果iterable的所有元素不为0、''、False或者iterable为空，all(iterable)返回True，否则返回False

### 注意
- 空元组、空列表返回值为True，这里要特别注意

### 实例

```python
>>> all(['a', 'b', 'c', 'd'])  # 列表list，元素都不为空或0
True
>>> all(['a', 'b', '', 'd'])   # 列表list，存在一个为空的元素
False
>>> all([0, 1，2, 3])          # 列表list，存在一个为0的元素
False
   
>>> all(('a', 'b', 'c', 'd'))  # 元组tuple，元素都不为空或0
True
>>> all(('a', 'b', '', 'd'))   # 元组tuple，存在一个为空的元素
False
>>> all((0, 1, 2, 3))          # 元组tuple，存在一个为0的元素
False
   
>>> all([])             # 空列表
True
>>> all(())             # 空元组
True
```
---
---
---

## any()函数

### 描述
any() 函数用于判断给定的可迭代参数 iterable 是否全部为 False，则返回 False，如果有一个为 True，则返回 True。

元素除了是 0、空、FALSE 外都算 TRUE

```python
# 函数等价于
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
    
# 注：python2.5以上版本可用
```

### 语法

    any(iterable)

### 参数

- iterable -- 元组或列表

### 返回值

- 如果都为空、0、false，则返回false，如果不都为空、0、false，则返回true

### 实例

```python
>>> any(['a', 'b', '', 'd'])   # 列表list，存在一个为空的元素
True
 
>>> any([0, '', False])        # 列表list,元素全为0,'',false
False
 
>>> any(('a', 'b', 'c', 'd'))  # 元组tuple，元素都不为空或0
True
 
>>> any(('a', 'b', '', 'd'))   # 元组tuple，存在一个为空的元素
True
 
>>> any((0, '', False))        # 元组tuple，元素全为0,'',false
False
  
>>> any([]) # 空列表
False
 
>>> any(()) # 空元组
False
```




---
---
---
## ascii()函数
### 描述
- ascii() 函数类似 repr() 函数, 返回一个表示对象的字符串, 但是对于字符串中的非 ASCII 字符则返回通过 repr() 函数使用 \x, \u 或 \U 编码的字符。 生成字符串类似 Python2 版本中 repr() 函数的返回值

### 语法

    ascii(object)

### 参数

- object —— 对象

### 返回值

返回字符串

### 实例

```python
>>> ascii('roonb')
"'roonb'"


>>> ascii('a')
"'a'"
>>> ascii('董付国')
"'\\u8463\\u4ed8\\u56fd'"
>>> eval(_)                        #对字符串进行求值
'董付国'

```

---
---
---

## bytes()函数
### 描述
- bytes 函数返回一个新的 bytes 对象，该对象是一个 0 <= x < 256 区间内的整数不可变序列。它是 bytearray 的不可变版本

### 语法

    class bytes([source[, encoding[, errors]]])

### 参数

- 如果 source 为整数，则返回一个长度为 source 的初始化数组；


- 如果 source 为字符串，则按照指定的 encoding 将字符串转换为字节序列；


- 如果 source 为可迭代类型，则元素必须为[0 ,255] 中的整数；


- 如果 source 为与 buffer 接口一致的对象，则此对象也可以被用于初始化 bytearray。


- 如果没有输入任何参数，默认就是初始化数组为0个元素

### 返回值
返回一个新的bytes对象

### 实例

```python
>>>a = bytes([1,2,3,4])
>>> a
b'\x01\x02\x03\x04'
>>> type(a)
<class 'bytes'>
>>>
>>> a = bytes('hello','ascii')
>>>
>>> a
b'hello'
>>> type(a)
<class 'bytes'>
>>>
```
---

## center()方法(不是内置函数)
### 描述

- Python center() 返回一个原字符串居中,并使用空格填充至长度 width 的新字符串。默认填充字符为空格。

### 语法

    str.center(width[, fillchar])

### 参数

- width -- 字符串的总宽度。
- fillchar -- 填充字符。

### 返回值

该方法返回一个原字符串居中,并使用空格填充至长度 width 的新字符串

### 实例

```python
>>>str = 'runoob'
>>> str.center(20, '*')
'*******runoob*******'
>>> str.center(20)
'       runoob       '
>>>
```

### 课后笔记

- 1、str.center(width [, char ]) 返回长度为width的字符串。
- 2、不提供char字符则默认为空格。
- 3、无法使左右字符数相等的时候，字符串字符数为奇数但width为偶数时，左侧字符会比右侧字符少1，字符串字符数为偶数但width为奇数时，左侧字符会比右侧字符多1

```python
>>> a = 'hello'
>>> a.center(20)
'       hello        '
>>> a.center(19, '-')
'-------hello-------'
>>> a.center(3)
'hello'
>>> a.center(8, '-')
'-hello--'
>>> b='runoob'
>>> b.center(9, '-')
'--runoob-'
>>> 
```

> 自己领悟：

      补奇原则：
        当字符串为奇数width为偶数时，假设给字符串多补一个空字符，此时字符串是偶数个，
        那么左右两边的空位应该是(width-len(str)-1)/2,当补完左右两边的空位后，发现其
        实还多出一个空位(字符串被多补的一个空位),那么剩下的一个空位就被补到右边,所以
        会导致左边比右边少一个空位
        
        当字符串为偶数，而width为奇数时，则假设将width+1，则此时变成偶数，那么补到左
        右两边的空位应该是(width+1-len(str)),当补完左右两边的空位发现应该少一个字符,
        则就会在右边减少一个字符，所以左边会比右边多一个空位
        
        同奇同偶的算法是一样的

---
## divmod()函数

### 描述
- Python divmod() 函数接收两个数字类型（非复数）参数，返回一个包含商和余数的元组(a // b, a % b)。

- 在 python 3.x 版本该函数不支持复数。

### 语法

    divmod(a,b)

### 参数

- a: 数字，非复数。
- b: 数字，非复数

如果参数 a 与 参数 b 都是整数，函数返回的结果相当于 (a // b, a % b)。

如果其中一个参数为浮点数时，函数返回的结果相当于 (q, a % b)，q 通常是 math.floor(a / b)，但也有可能是 1 ，比小，不过 q * b + a % b 的值会非常接近 a。

如果 a % b 的求余结果不为 0 ，则余数的正负符号跟参数 b 是一样的，若 b 是正数，余数为正数，若 b 为负数，余数也为负数，并且 0 <= abs(a % b) < abs(b)

### 实例
```python
>>> divmod(7, 2)
(3, 1)
>>> divmod(8, 2)
(4, 0)
>>> divmod(8, -2)
(-4, 0)
>>> divmod(3, 1.3)
(2.0, 0.3999999999999999)
```
---
---
---

## format()格式化函数(不是内置函数)

Python2.6 开始，新增了一种格式化字符串的函数 str.format()，它增强了字符串格式化的功能。

基本语法是通过 {} 和 : 来代替以前的 % 。

format 函数可以接受不限个参数，位置可以不按顺序。

```python

>>>"{} {}".format("hello", "world")    # 不设置指定位置，按默认顺序
'hello world'
 
>>> "{0} {1}".format("hello", "world")  # 设置指定位置
'hello world'
 
>>> "{1} {0} {1}".format("hello", "world")  # 设置指定位置
'world hello world'

```
也可以设置参数：

```python
print("网站名：{name}, 地址 {url}".format(name="菜鸟教程", url="www.runoob.com"))
 
# 通过字典设置参数
site = {"name": "菜鸟教程", "url": "www.runoob.com"}
print("网站名：{name}, 地址 {url}".format(**site))
 
# 通过列表索引设置参数
my_list = ['菜鸟教程', 'www.runoob.com']
print("网站名：{0[0]}, 地址 {0[1]}".format(my_list))  # "0" 是必须的


网站名：菜鸟教程, 地址 www.runoob.com
网站名：菜鸟教程, 地址 www.runoob.com
网站名：菜鸟教程, 地址 www.runoob.com
```
也可以向 str.format() 传入对象
```python
class AssignValue(object):
    def __init__(self, value):
        self.value = value
my_value = AssignValue(6)
print('value 为: {0.value}'.format(my_value))  # "0" 是可选的

value 为: 6
```

![](D:/Boostnote%E7%AC%94%E8%AE%B0/img/64.png)




---
---
---
## iter()函数

### 描述
- iter() 函数用来生成迭代器。

### 语法

    iter(object[, sentinel])

### 参数

-  object -- 支持迭代的集合对象。


- sentinel -- 如果传递了第二个参数，则参数 object 必须是一个可调用的对象（如，函数），此时，iter 创建了一个迭代器对象，每次调用这个迭代器对象的__next__()方法时，都会调用 object。 

### 返回值

- 迭代器对象

### 实例
```python

>>>lst = [1, 2, 3]
>>> for i in iter(lst):
...     print(i)
... 
1
2
3

```


---
---
---

## 字典items()方法
### 描述
- Python 字典 items() 方法以列表返回可遍历的(键, 值) 元组数组

### 语法

    dict.items()

### 参数
- 无

### 返回值
- 返回可遍历的(键, 值) 元组数组。

### 实例
```python
dict = {'Name': 'Runoob', 'Age': 7}
 
print ("Value : %s" %  dict.items())


Value : dict_items([('Age', 7), ('Name', 'Runoob')])
```

### 课后笔记
***遍历***

```python
dict = {'Name': 'Runoob', 'Age': 7}
for i,j in dict.items():
    print(i, ":\t", j)
    
    
Name :   Runoob
Age :    7    
```

---

***将字典的 key和 value 组成一个新的列表***

```python
d={1:"a",2:"b",3:"c"}
result=[]
for k,v in d.items():
    result.append(k)
    result.append(v)

print(result)


[1, 'a', 2, 'b', 3, 'c']
```


---
---
---
## map()函数
---
### 描述

map() 会根据提供的函数对指定序列做映射。

第一个参数 function 以参数序列中的**每一个元素**调用 function 函数，返回包含每次 function 函数返回值的新列表。

### 语法

    map(function, iterable, ...)

### 参数

- function -- 函数
- iterable -- 一个或多个序列

### 返回值

Python 2.x 返回列表。

Python 3.x 返回迭代器。

### 实例

```python
>>>def square(x) :            # 计算平方数
...     return x ** 2
... 
>>> map(square, [1,2,3,4,5])   # 计算列表各个元素的平方
[1, 4, 9, 16, 25]
>>> map(lambda x: x ** 2, [1, 2, 3, 4, 5])  # 使用 lambda 匿名函数
[1, 4, 9, 16, 25]
 
# 提供了两个列表，对相同位置的列表数据进行相加
>>> map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
[3, 7, 11, 15, 19]



>>> list(map(str, range(5)))  #把列表中元素转换为字符串
['0', '1', '2', '3', '4']
>>> def add5(v):              #单参数函数
    return v+5
>>> list(map(add5, range(10)))#把单参数函数映射到一个序列的所有元素
[5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
>>> def add(x, y):            #可以接收2个参数的函数
    return x+y
>>> list(map(add, range(5), range(5,10)))
                              #把双参数函数映射到两个序列上
[5, 7, 9, 11, 13]

```
---
---
---

## zip()函数
---

### 描述

zip() 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的对象，这样做的好处是节约了不少的内存。

我们可以使用 list() 转换来输出列表。

如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同。

利用 * 号操作符，可以将元组解压为列表。

    zip 方法在 Python 2 和 Python 3 中的不同：在 Python 2.x zip() 返回的是一个列表。

### 语法

    zip([iterable, ...])
    
    参数描述：
        iterabl -- 一个或多个迭代器;

### 返回值

    返回一个对象

### 实例

```python
>>>a = [1,2,3]
>>> b = [4,5,6]
>>> c = [4,5,6,7,8]
>>> zipped = zip(a,b)     # 返回一个对象
>>> zipped
<zip object at 0x103abc288>
>>> list(zipped)  # list() 转换为列表
[(1, 4), (2, 5), (3, 6)]
>>> list(zip(a,c))              # 元素个数与最短的列表一致
[(1, 4), (2, 5), (3, 6)]
 
>>> a1, a2 = zip(*zip(a,b))          # 与 zip 相反，zip(*) 可理解为解压，返回二维矩阵式
>>> list(a1)
[1, 2, 3]
>>> list(a2)
[4, 5, 6]
>>>


>>> list(zip(range(1,4)))     #zip()函数也可以用于一个序列或迭代对象
[(1,), (2,), (3,)]

```
---
---
---

## enumerate()函数

### 描述

- enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

### 语法

    enumerate(sequence, [start=0])

### 参数

- sequence -- 一个序列、迭代器或其他支持迭代对象。
- start -- 下标起始位置。

### 返回值

- 返回 enumerate(枚举) 对象。

### 实例

> enumerate()用法

```python
>>>seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>>list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>>list(enumerate(seasons, start=1))       # 下标从 1 开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

> 普通的for循环用法

```python
>>>i = 0
>>>seq = ['one', 'two', 'three']
>>>for element in seq:
...    print(i, seq[i])
...    i += 1
... 
0 one
1 two
2 three
```

> for循环使用enumerate 

```python
>>>seq = ['one', 'two', 'three']
>>>for i, element in enumerate(seq):
...    print(i, seq[i])
... 
0 one
1 two
2 three
>>>
```
---

## round()函数(不是内置)
### 描述
- round() 方法返回浮点数x的四舍五入值。

### 语法

    round( x [, n]  )

### 参数

-  x -- 数值表达式。
- n -- 数值表达式。(保留的小数位数)

### 返回值
- 返回浮点数x的四舍五入值。 

### 实例

```python
print "round(80.23456, 2) : ", round(80.23456, 2)
print "round(100.000056, 3) : ", round(100.000056, 3)
print "round(-100.000056, 3) : ", round(-100.000056, 3)


round(80.23456, 2) :  80.23
round(100.000056, 3) :  100.0
round(-100.000056, 3) :  -100.0
```

**注：详细请参考      
[http://www.runoob.com/w3cnote/python-round-func-note.html](http://www.runoob.com/w3cnote/python-round-func-note.html)**

---

## random()函数(不是内置函数)
### 描述

- random() 方法返回随机生成的一个实数，它在[0,1)范围内

### 语法

    import random
    
    random.random()

**注意：** random()是不能直接访问的，需要导入 random 模块，然后通过 random 静态对象调用该方法

### 参数

无

### 返回值

返回随机生成的一个实数，它在[0,1)范围内

### 实例

```python
import random
 
# 生成第一个随机数
print "random() : ", random.random()
 
# 生成第二个随机数
print "random() : ", random.random()

random() :  0.281954791393
random() :  0.309090465205


# 多个字符中生成指定数量的随机字符
print random.sample('zyxwvutsrqponmlkjihgfedcba',5)

print( random.randint(1,10) )        # 产生 1 到 10 的一个整数型随机数，包含 1 和 10  
print( random.random() )             # 产生 0 到 1 之间的随机浮点数
print( random.uniform(1.1,5.4) )     # 产生  1.1 到 5.4 之间的随机浮点数，区间可以不是整数
print( random.choice('tomorrow') )   # 从序列中随机选取一个元素

print( random.randrange(1,100,2) )   # 生成从1到100的间隔为2的随机整数（含首不含尾，起始值和步长可省略）
27  

a=[1,3,5,6,7]               
random.shuffle(a)    # 将序列a中的元素顺序打乱
print(a)

```
---
---
---
## reversed()函数
### 描述
- reversed 函数返回一个反转的迭代器。

### 语法

    reversed(seq)

### 参数
- seq -- 要转换的序列，可以是 tuple, string, list 或 range

### 返回值
- 返回一个反转的迭代器

### 实例
```python
# 字符串
seqString = 'Runoob'
print(list(reversed(seqString)))
 
# 元组
seqTuple = ('R', 'u', 'n', 'o', 'o', 'b')
print(list(reversed(seqTuple)))
 
# range
seqRange = range(5, 9)
print(list(reversed(seqRange)))
 
# 列表
seqList = [1, 2, 4, 3, 5]
print(list(reversed(seqList)))


['b', 'o', 'o', 'n', 'u', 'R']
['b', 'o', 'o', 'n', 'u', 'R']
[8, 7, 6, 5]
[5, 3, 4, 2, 1]
```
---

## reverse()
### 描述
reverse() 函数用于反向列表中元素。

### 语法

    list.reverse()

### 参数
无

### 返回值
无，对原列表自身进行翻转

### 实例

```python
list1 = ['Google', 'Runoob', 'Taobao', 'Baidu']
list1.reverse()
print ("列表反转后: ", list1)


列表反转后:  ['Baidu', 'Taobao', 'Runoob', 'Google']
```

---
---
## split()函数
### 描述
- Python split() 通过指定分隔符对字符串进行切片，如果参数 num 有指定值，则分隔 num+1 个子字符串

### 语法

    str.split(str="", num=string.count(str))

### 参数
- str -- 分隔符，默认为所有的空字符，包括空格、换行(\n)、制表符(\t)等。
- num -- 分割次数。默认为 -1, 即分隔所有。

### 返回值

- 返回分割后的字符串列表。

### 实例

```python
str = "Line1-abcdef \nLine2-abc \nLine4-abcd";
print str.split( );       # 以空格为分隔符，包含 \n
print str.split(' ', 1 ); # 以空格为分隔符，分隔成两个

['Line1-abcdef', 'Line2-abc', 'Line4-abcd']
['Line1-abcdef', '\nLine2-abc \nLine4-abcd']




txt = "Google#Runoob#Taobao#Facebook"
 
# 第二个参数为 1，返回两个参数列表
x = txt.split("#", 1)
 
print x



['Google', 'Runoob#Taobao#Facebook']
```

---
---
---
## sorted()函数

### 描述
- 对所有可迭代的对象进行排序操作


    sort 与  sorted 区别：
    
    sort 是应用在 list 上的方法，sorted 可以对所有可迭代的对象进行排序操作。
    
    list 的 sort 方法返回的是对已经存在的列表进行操作，而内建函数 sorted 方法返回的是一个新的 list，而不是在原来的基础上进行的操作
    
    list的sort方法是对原来的列表自身进行操作，例如：a是一个列表，你使用 a.sort() 方法，会对 a 进行排序，此时你再 print(a)，输入的结果将是排序后的 a ，
    但是 a.sort() 这个方法本身的返回值是 None，所以当你使用 print(a.sort())时，将会输出一个 None 值

### 语法

    sorted(iterable, key=None, reverse=False) 

### 参数说明

- **iterable -- 可迭代对象**。


- key -- 主要是用来进行比较的元素，只有一个参数，**具体的函数的参数就是取自于可迭代对象中**，**指定可迭代对象中的一个元素来进行排序**。


- reverse -- 排序规则，reverse = True 降序 ， reverse = False 升序（默认）。

### 返回值

返回重新排序的列表

### 实例

```python
# 以下实例展示了 sorted 的最简单的使用方法
>>>sorted([5, 2, 3, 1, 4])
[1, 2, 3, 4, 5]                      # 默认为升序

# 你也可以使用 list 的 list.sort() 方法。这个方法会修改原始的 list（返回值为None）。
# 通常这个方法不如sorted()方便-如果你不需要原始的 list，list.sort()方法效率会稍微高一些。
>>>a=[5,2,3,1,4]
>>> a.sort()
>>> a
[1,2,3,4,5]

# 另一个区别在于list.sort() 方法只为 list 定义。
# 而 sorted() 函数可以接收任何的 iterable。
>>>sorted({1: 'D', 2: 'B', 3: 'B', 4: 'E', 5: 'A'})
[1, 2, 3, 4, 5]

# 利用key进行倒序排序
>>>example_list = [5, 0, 6, 1, 2, 7, 3, 4]
>>> result_list = sorted(example_list, key=lambda x: x*-1)
>>> print(result_list)
[7, 6, 5, 4, 3, 2, 1, 0]
>>>


# 要进行反向排序，也通过传入第三个参数 reverse=True
>>>example_list = [5, 0, 6, 1, 2, 7, 3, 4]
>>> sorted(example_list, reverse=True)
[7, 6, 5, 4, 3, 2, 1, 0]

# sorted 的应用，也可以通过 key 的值来进行数组/字典的排序，比如：
array = [{"age":20,"name":"a"},{"age":25,"name":"b"},{"age":10,"name":"c"}]
array = sorted(array,key=lambda x:x["age"])
print(array)

[{'age': 10, 'name': 'c'}, {'age': 20, 'name': 'a'}, {'age': 25, 'name': 'b'}]


# 先按照成绩降序排序，相同成绩的按照名字升序排序：
d1 = [{'name':'alice', 'score':38}, {'name':'bob', 'score':18}, {'name':'darl', 'score':28}, {'name':'christ', 'score':28}]
l = sorted(d1, key=lambda x:(-x['score'], x['name']))
print(l)

[{'name': 'alice', 'score': 38}, {'name': 'christ', 'score': 28},{'name': 'darl', 'score': 28}, {'name': 'bob', 'score': 18}]

```
---

```python
>>> x = list(range(11))                       #包含11个整数的列表
>>> import random
>>> random.shuffle(x)                         #把列表x中的元素随机乱序
>>> x
[6, 0, 1, 7, 4, 3, 2, 8, 5, 10, 9]
>>> x.sort(key=lambda item:len(str(item)), reverse=True)  #按转换成字符串以后的长度，降序排列
>>> x
[10, 6, 0, 1, 7, 4, 3, 2, 8, 5, 9]
>>> x.sort(key=str)                           #按转换为字符串后的大小，升序排序
>>> x
[0, 1, 10, 2, 3, 4, 5, 6, 7, 8, 9]
>>> x.sort()                                  #按默认规则排序
>>> x
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> x.reverse()                               #把所有元素翻转或逆序
>>> x
[10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```
---
---
---

## strip()函数

### 描述
Python strip() 方法用于移除字符串头尾指定的字符（默认为空格或换行符）或字符序列。

**注意**：该方法只能删除开头或是结尾的字符，不能删除中间部分的字符。

### 语法

    str.strip([chars])

### 参数

- chars -- 移除字符串头尾指定的字符序列。

### 返回值

- 返回移除字符串头尾指定的字符生成的新字符串。

### 实例

```python
str = "00000003210Runoob01230000000"; 
print str.strip( '0' );  # 去除首尾字符 0
 
 
str2 = "   Runoob      ";   # 去除首尾空格
print str2.strip();

3210Runoob0123
Runoob
```
从结果上看，可以注意到中间部分的字符并未删除。

以上实例演示了只要头尾包含有指定字符序列中的字符就删除:

```python
str = "123abcrunoob321"
print (str.strip( '12' ))  # 字符序列为 12

3abcrunoob3

注：删除的字符，与字符的顺序无关
```


---
---
---

## eval()函数

### 描述

- eval() 函数用来执行一个字符串表达式，并返回表达式的值。

### 语法

    eval(expression[, globals[, locals]])

### 参数

- expression -- 表达式。
- globals -- 变量作用域，全局命名空间，如果被提供，则必须是一个字典对象。
- locals -- 变量作用域，局部命名空间，如果被提供，可以是任何映射对象。

### 返回值

- 返回表达式计算结果。

### 实例

```python
>>>x = 7
>>> eval( '3 * x' )
21
>>> eval('pow(2,2)')
4
>>> eval('2 + 2')
4
>>> n=81
>>> eval("n + 4")
85
```
---
---
---

## filter()函数

### 描述
- filter() 函数用于过滤序列，过滤掉不符合条件的元素，返回一个迭代器对象，如果要转换为列表，可以使用 list() 来转换。

- 该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判，然后返回 True 或 False，最后将返回 True 的元素放到新列表中


### 语法

    filter(function, iterable)

### 参数

-  function -- 判断函数。
- iterable -- 可迭代对象

### 返回值

- 返回一个迭代器对象

### 实例

```python
# 过滤出列表中的所有奇数
def is_odd(n):
    return n % 2 == 1
 
tmplist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
newlist = list(tmplist)
print(newlist)

[1, 3, 5, 7, 9]
```

```python
# 过滤出1~100中平方根是整数的数
import math
def is_sqr(x):
    return math.sqrt(x) % 1 == 0
 
tmplist = filter(is_sqr, range(1, 101))
newlist = list(tmplist)
print(newlist)

[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

```python
# 内置函数filter()将一个单参数函数作用到一个序列上，
# 返回该序列中使得该函数返回值为True的那些元素组成的filter对象，
# 如果指定函数为None，则返回序列中等价于True的元素
>>> seq = ['foo', 'x41', '?!', '***']
>>> def func(x):
    return x.isalnum()                  #测试是否为字母或数字

>>> filter(func, seq)                   #返回filter对象
<filter object at 0x000000000305D898>
>>> list(filter(func, seq))             #把filter对象转换为列表
['foo', 'x41']

```




---
---
---

## frozenset()函数

### 描述

- frozenset() 返回一个冻结的集合，冻结后集合不能再添加或删除任何元素。

### 语法

    class frozenset([iterable])

### 参数

- iterable -- 可迭代的对象，比如列表、字典、元组等等。

### 返回值

- 返回新的 frozenset 对象，如果不提供任何参数，默认会生成空集合。

### 实例

```python
>>>a = frozenset(range(10))     # 生成一个新的不可变集合
>>> a
frozenset([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> b = frozenset('runoob') 
>>> b
frozenset(['b', 'r', 'u', 'o', 'n'])   # 创建不可变集合
>>>
```
---
---
---






---
---
---


## ord()函数与chr()函数

### 描述

- ord() 函数是 chr() 函数（对于8位的ASCII字符串）或 unichr() 函数（对于Unicode对象）的配对函数，**它以一个字符（长度为1的字符串）作为参数**，返回对应的 ASCII 数值，或者 Unicode 数值，如果所给的 Unicode 字符超出了你的 Python 定义范围，则会引发一个 TypeError 的异常。
- chr() 用一个范围在 range（256）内的（就是0～255）整数作参数，返回一个对应的字符。

### 语法
- 以下是ord()方法的语法


      ord(c)

*返回值是对应的十进制数*


- 以下是chr()方法的语法


      chr(i)

*返回值是当前整数对应的 ASCII 字符。*
### 参数
- c -- 字符。
- i -- 可以是10进制也可以是16进制的形式的数字。

### 实例

```python
>>>ord('a')
97
>>> ord('b')
98
>>> ord('c')
99



>>>print chr(0x30), chr(0x31), chr(0x61)   # 十六进制
0 1 a
>>> print chr(48), chr(49), chr(97)         # 十进制
0 1 a

```
---
---
---

## int()函数

### 描述

- 用于将一个字符串或数字转换为整型

### 语法

    class int(x, base=10)

### 参数

- x -- 字符串或数字。
- base -- 进制数，默认十进制。

***base的取值应为  0  或  2-36 之间的整数，其中 0表示按数字字符串隐含的进制进行转换 。***
### 返回值

- 返回整型数据

### 实例

```python
>>>int()               # 不传入参数时，得到结果0
0
>>> int(3)
3
>>> int(3.6)
3
>>> int('12',16)        # 如果是带参数base的话，12要以字符串的形式进行输入，12 为 16进制
18
>>> int('0xa',16)  
10  
>>> int('10',8)  
8


>>> int(-3.2)                       #把实数转换为整数
-3
>>> from fractions import Fraction, Decimal
>>> x = Fraction(7, 3)
>>> x
Fraction(7, 3)
>>> int(x)                          #把分数转换为整数
2
>>> x = Decimal(10/3)
>>> x
Decimal('3.333333333333333481363069950020872056484222412109375')
>>> int(x)                          #把高精度实数转换为整数
3


>>> int('0x22b', 16)                 #把十六进制数转换为十进制数
555
>>> int('22b', 16)                   #与上一行代码等价
555
>>> int(bin(54321), 2)               #二进制与十进制之间的转换
54321
>>> int('0b111')                     #非十进制字符串进，必须指定第二个参数
ValueError: invalid literal for int() with base 10: '0b111'
>>> int('0b111', 0)                  #第二个参数0表示使用字符串隐含的进制
7
>>> int('0b111', 6)                  #第二个参数应与隐含的进制一致
ValueError: invalid literal for int() with base 6: '0b111'
>>> int('0b111', 2)
7
>>> int('111', 6)                    #字符串没有隐含进制
                                     #第二个参数可以为2-36之间的数字
43


```

### 课后笔记

> 参数 x 有两种情况：int/str

***1、若x为纯数字，则不能有base参数，否则会报错***

```python
>>> int(3.1415926)
3

>>> int(-11.123)
-11

>>> int(2.5,10)
#报错
>>> int(2.5)
2
```

***2、若 x为 str，则 base可略可有。base存在时，视 x为 base类型的数字，并将其转换为十进制数字。若 x不符合 base规范，则会报错。***

```python
>>>int("9",2)  #报错，因为2进制无9
>>> int("9")
9 
#默认10进制

>>> int("3.14",8)
>>> int("1.2")
#均报错，str须为整数
>>>int("1001",2)
9
# "1001"才是2进制格式，并转化为十进制数字9
>>> int("0xa",16)
10
# ≥16进制才会允许入参为a,b,c...
>>> int("b",8) #报错
>>> int("123",8)
83
#视123为8进制数字，对应的10进制为83
```
---
---
---

## operator()函数

### 描述

operator 模块提供了一套与Python的内置运算符对应的高效率函数 ，许多函数名与特殊方法名相同，只是没有双下划线。为了向后兼容性，也保留了许多包含双下划线的函数。函数包含的种类有：对象的比较运算、逻辑运算、数学运算以及序列运算。

这个模块提供了很多和Python 一样的操作符, 这里 只是封装一个函数 进行调用

举个例子：
```python
 import operator
 
 b = operator.add(3,5)
 print(b)  # 8 
 
 

>>> seq = list(range(1, 10))

>>> import operator                         #标准库operator提供了大量运算
>>> operator.add(3,5)                       #可以像普通函数一样直接调用
8
>>> reduce(operator.add, seq)               #使用add运算
45
>>> reduce(operator.mul, seq)               #乘法运算
362880
>>> reduce(operator.mul, range(1, 6))       #5的阶乘
120
>>> reduce(operator.add, map(str, seq))     #转换成字符串再累加
'123456789'
>>> reduce(operator.add, [[1, 2], [3]], []) #这个操作占用空间较大，慎用
[1, 2, 3]

```

### 概述

operator 模块 封装了很多操作相关的函数, 比如 加,减 乘除 ,比较运算 ,逻辑运算 ,矩阵相乘. 还有一些封装好的类, 用起来 效率 比较高效. 本文根据个人的使用经验简单介绍一下, 这些类的常用用法。

### 将运算映射到函数

表显示抽象操作如何对应于Python语法中的操作符号以及operator模块中的函数。

![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205192353059.png)








---
---
---

## range()函数

### 描述
- Python3 range() 函数返回的是一个可迭代对象（类型是对象），而不是列表类型， 所以打印的时候不会打印列表。


- Python3 list() 函数是对象迭代器，可以把range()返回的可迭代对象转为一个列表，返回的变量类型为列表


- Python2 range() 函数返回的是列表


### 函数语法

    range(stop)
    
    range(start, stop[, step])

### 参数说明

- start: 计数从 start 开始。默认是从 0 开始。例如range（5）等价于range（0， 5）;


- stop: 计数到 stop 结束，但不包括 stop。例如：range（0， 5） 是[0, 1, 2, 3, 4]没有5


- step：步长，默认为1。例如：range（0， 5） 等价于 range(0, 5, 1)

```python
>>>range(5)
range(0, 5)
>>> for i in range(5):
...     print(i)
... 
0
1
2
3
4
>>> list(range(5))
[0, 1, 2, 3, 4]
>>> list(range(0))
[]
>>>


>>> type(range(1,0))
<class 'range'>
>>>
>>> range(1,10)
range(1,10)
>>>

>>>list(range(0, 30, 5))
[0, 5, 10, 15, 20, 25]
>>> list(range(0, 10, 2))
[0, 2, 4, 6, 8]
>>> list(range(0, -10, -1))
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> list(range(1, 0))
[]
>>>
>>>

```
---
---
---

## reduce()函数
### 描述

reduce() 函数会对参数序列中元素进行累积。

函数将一个数据集合（链表，元组等）中的所有数据进行下列操作：用传给 reduce 中的函数 function（有两个参数）先对集合中的第 1、2 个元素进行操作，得到的结果再与第三个数据用 function 函数运算，最后得到一个结果

### 语法

    reduce(function, iterable[, initializer])

### 参数

- function -- 函数，有两个参数
- iterable -- 可迭代对象
- initializer -- 可选，初始参数

### 返回值

- 返回函数计算结果

### 实例

```python
>>>def add(x, y) :            # 两数相加
...     return x + y
... 
>>> reduce(add, [1,2,3,4,5])   # 计算列表和：1+2+3+4+5
15
>>> reduce(lambda x, y: x+y, [1,2,3,4,5])  # 使用 lambda 匿名函数
15



>>> from functools import reduce
>>> seq = list(range(1, 10))
>>> reduce(lambda x, y: x+y, seq)
45

```


---
---
---
## str()函数
### 描述
- str() 函数将对象转化为适于人阅读的形式

### 语法

    class str(object='')

### 参数

- object——对象

### 返回值

- 返回一个对象的string格式

### 实例

```python
>>> str(1234)                      #直接变成字符串
'1234'
>>> str([1,2,3])
'[1, 2, 3]'
>>> str((1,2,3))
'(1, 2, 3)'
>>> str({1,2,3})
'{1, 2, 3}'
```


---
---
---
## bin()与oct()与hex()函数

### 描述

- 内置函数bin()、oct()、hex()用来将整数转换为二进制、八进制和十六进制形式，这三个函数都要求**参数必须为整数**

```python
>>> bin(555)                      #把数字转换为二进制串
'0b1000101011'
>>> oct(555)                      #转换为八进制串
'0o1053'
>>> hex(555)                       #转换为十六进制串
'0x22b'
```
---
---
---

## 其他内置函数

### float(x)、complex( [real [, imag]])

- x 的参数类型为整数或字符串,返回一个浮点数
- real、imag 的参数类型，返回一个复数
  - real -- int, long, float或字符串；
  - imag -- int, long, float；
```python
>>> float(3)                       #把整数转换为实数
3.0
>>> float('3.5')                   #把数字字符串转换为实数
3.5
>>> float(-123.6)
-123.6
>>> float('inf')                   #无穷大，其中inf不区分大小写
inf
>>> complex(3)                     #指定实部
(3+0j)
>>> complex(3, 5)                  #指定实部和虚部
(3+5j)
>>> complex('inf')                 #无穷大
(inf+0j)
```
---

### str(x)

- x  参数为对象
```python
>>> str(1234)                      #直接变成字符串
'1234'
>>> str([1,2,3])
'[1, 2, 3]'
>>> str((1,2,3))
'(1, 2, 3)'
>>> str({1,2,3})
'{1, 2, 3}'
```
---

### ascii(x)

- 内置类ascii可以把对象转换为ASCII码表示形式，必要的时候使用转义字符来表示特定的字符。

```python
>>> ascii('a')
"'a'"
>>> ascii('董付国')
"'\\u8463\\u4ed8\\u56fd'"
>>> eval(_)                        #对字符串进行求值
'董付国'
```
---

### bytes()、encode()

- 内置类bytes用来生成字节串，或者把指定对象转换为特定编码的字节串
- bytes 函数返回一个新的 bytes 对象，该对象是一个 0 <= x < 256 区间内的整数不可变序列。它是 bytearray 的不可变版本。


    class bytes([source[, encoding[, errors]]])


```python
>>> bytes()                        #生成空字节串
b''
>>> bytes(3)                       #生成长度为3的字节串
b'\x00\x00\x00'
>>> bytes('董付国','utf8')         #把字符串转换为字节串
b'\xe8\x91\xa3\xe4\xbb\x98\xe5\x9b\xbd'
>>> bytes('董付国','gbk')          #可以指定不同的编码格式
b'\xb6\xad\xb8\xb6\xb9\xfa'
>>> str(_, 'gbk')                 #使用同样的编码格式进行解码
'董付国'
>>> '董付国'.encode('gbk')         #等价于使用btyes()进行转换
b'\xb6\xad\xb8\xb6\xb9\xfa'


>>>a = bytes([1,2,3,4])
>>> a
b'\x01\x02\x03\x04'
>>> type(a)
<class 'bytes'>
>>>
>>> a = bytes('hello','ascii')
>>>
>>> a
b'hello'
>>> type(a)
<class 'bytes'>
>>>
```

## 最值与求和

- max()、min()、sum()这三个内置函数分别用于计算列表、元组或其他包含有限个元素的可迭代对象中所有元素最大值、最小值以及所有元素之和。
- sum()默认（可以通过start参数来改变）支持包含数值型元素的序列或可迭代对象，max()和min()则要求序列或可迭代对象中的元素之间可比较大小。

```python
>>> from random import randint
>>> a = [randint(1,100) for i in range(10)]  #包含10个[1,100]之间随机数的列表
>>> print(max(a), min(a), sum(a))            #最大值、最小值、所有元素之和
>>> sum(a) / len(a)
```
- 函数max()和min()还支持default参数和key参数，其中default参数用来指定可迭代对象为空时默认返回的最大值或最小值，而key参数用来指定比较大小的依据或规则，可以是函数或lambda表达式。函数sum()还支持start参数，用来控制求和的初始值。

```python
>>> max(['2', '111'])               #不指定排序规则
'2'
>>> max(['2', '111'], key=len)      #返回最长的字符串
'111'
>>> print(max([], default=None))    #对空列表求最大值，返回空值None
None
```

## 基本的输入输出

- input()和print()是Python的基本输入输出函数，前者用来接收用户的键盘输入，后者用来把数据以指定的格式输出到标准控制台或指定的文件对象。
- 不论用户输入什么内容，input()**一律返回字符串对待**，必要的时候可以使用内置函数int()，float()或eval()对用户输入的内容进行类型转换。

```python
>>> x = input('Please input: ')
Please input: 345
>>> x
'345'
>>> type(x)                     #把用户的输入作为字符串对待
<class 'str'>
>>> int(x)                      #转换为整数
345
>>> eval(x)                     #对字符串求值，或类型转换
345
>>> x = input('Please input: ')
Please input: [1, 2, 3]
>>> x
'[1, 2, 3]'
>>> type(x)
<class 'str'>
>>> eval(x)
[1, 2, 3]
```
---

- 内置函数print()用于输出信息到标准控制台或指定文件，语法格式为:


      print(value1, value2, ..., sep=' ', end='\n', file=sys.stdout, flush=False)


- sep参数之前为需要输出的内容（可以有多个）；


- sep参数用于指定数据之间的分隔符，默认为空格；


- end参数用于指定输出完数据之后再输出什么字符；


- file参数用于指定输出位置，默认为标准控制台，也可以重定向输出到文件


```python
>>> print(1, 3, 5, 7, sep='\t')       #修改默认分隔符
1	3	5	7
>>> for i in range(10):               #修改end参数，每个输出之后不换行
    print(i, end=' ')
0 1 2 3 4 5 6 7 8 9 
>>> with open('test.txt', 'a+') as fp:
    print('Hello world!', file=fp)    #重定向，将内容输出到文件中
```

```python
print('c=', 4)

c= 4
```