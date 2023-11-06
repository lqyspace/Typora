[TOC]
# python的函数注释

## 函数注释实例1
```python
def fun(name: str, age: '是一个大于零的整数值' = 52) -> '返回值为真':
    """
    这个是函数的帮助说明文档，help时会显示
    函数声明中，name:str
    name 是参数 :冒号后面  str 是参数的注释。
    如果参数有默认值，还要给注释，如下写。
    age:'是一个大于零的整数值'=52
    
    ->'返回值为真' 是函数返回值的注释。
    
    这些注释信息都是函数的元信息，保存在f.__annotations__字典中、
    
    需要注意，python对注释信息和f.__annotations__的一致性，不做检查
    不做检查，不做强制，不做验证！什么都不做。
    :param name:
    :param age:
    :return:
    """
    return True


print(fun.__annotations__)


------------打印结果--------------
{'name': <class 'str'>, 'age': '是一个大于零的整数值', 'return': '返回值为真'}

```

## 函数注释实例2
```python
def func(ham: 42, eggs: int = 'spam') -> "Nothing to see here":
    print("函数注释", func.__annotations__)
    print("参数值打印", ham, eggs)
    print(type(ham), type(eggs))


func("www")

-----------打印结果-------------
函数注释 {'ham': 42, 'eggs': <class 'int'>, 'return': 'Nothing to see here'}
参数值打印 www spam
<class 'str'> <class 'str'>

```
    解释说明：
      注释的一般规则是参数名后跟一个冒号（：），然后在跟一个expression，这个expression可以是任何形式。
    返回值的形式是 ——> int，annotation可被保存为函数的attributes。
    
      以上属于静态注释，还有一种方法叫做动态注释
      动态注释的原理，就是在函数中或者装饰器中动态的增加  删除  更改  注释内容


​    
    f.__annotations__ 是一个字典，可以使用字典的所有操作，这样就可以动态的更改注释内容了
    
    这是Python3.5中引入的Type Annotation，是一种注释，用来提示变量的类型。
      用法是：var：type = value
      var 为要定义的变量，type 为该变量期待的类型，value 为赋给该变量的值。
      本质上和 var = value 是一回事，只是加上了一个var的类型说明。
      类型注解是一种提示，并非强制性的，python解释器不会去验证value的类型是否真的是type。
      类型注解通常用在函数参数中，向函数的调用者提示参数类型：
        def func(arg:int)
      那么调用者调用函数func时就知道参数arg是一个int类型的参数。
      
    其他的注意事项：
      1、省略初始值时会使变量未初始化：a：str  ，  print（a）————抛出NameError异常
      2、注释局部变量将导致解释器始终使其成为局部变量：
        同一函数范围内注释受全局或非本地变量是不允许的
          
          def fun():
            global a
            a:str = 'python'
            print(a)
          
          fun()
          # 将抛出错误信息：SyntaxError：annotated name 'a' can't be global
          # 即是 注释变量 a 不能是全局的。
      3、类型注释还可用于在类主体和方法中注释类和实例变量
      4、注释的目标可以是任何有效的单个赋值目标：d={}，d['a']:str = 'python'——————输出d为字典{'a':'python'};
        d = [],d[0]:int = 10 ——————输出列表[10]


# python 的常用函数注释2

    def foo():
      """This is function foo"""

## Google 风格
```python
"""
This is a groups style docs.

Parameters:
  param1 - this is the first param
  param2 - this is a second param

Returns:
    This is a description of what is returned

Raises:
    KeyError - raises an exception
"""
```

## Rest 风格
```python
"""
This is a reST style.

:param param1: this is a first param
:param param2: this is a second param
:returns: this is a description of what is returned
:raises keyError: raises an exception
"""
```