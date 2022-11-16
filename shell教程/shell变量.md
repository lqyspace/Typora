[TOC]

> 实例

```shell
#!/bin/bash

# 变量名和等号之间不能有空格
your_name="qinxji"
# 使用变量：在变量前加一个$
echo $your_name
# 或者变量外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界
echo ${your_name}

for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Scipt."
done

myUrl="https://www.google.com"
# 将变量设置为只读属性readonly
readonly myUrl
myUrl="https://www.baidu.com"

# 删除变量
myurl="https://www.baidu.com"
unset myurl
echo $myurl

# Shell 字符串
# 字符串是shell编程中最常用的最有用的数据类型（除了数字和字符串，也没啥其他的类型好用了），字符串可以用单引号，也可以用双引号，也可以不用引号

## 单引号
str='this  is a string.\n12'
echo -e $str
# 单引号的限制
## 单引号里任何字符都会原样输出，单引号字符串中的变量是无效的
## 单引号字符串中不能出现单独一个单引号（对单引号使用转义符也不行），但可以成对出现，作为字符拼接使用

# 双引号
your_name="runoob"
str="Hello, I know you are \"$your_name\"! \n"
str1="Hello, I know you are $your_name \n"
echo -e $str1
echo -e $str # -e 打开反斜杠ESC转义；-n 不要在最后自动换行；-E 取消反斜杠ESC转义（默认）

# 双引号的优点：
## 双引号里可以有变量
## 双引号里面可以出现转义字符

# 拼接字符串
your_name="runoob"
## 双引号拼接
greeting="hello, "$your_name"!"
greeting1="hello, ${your_name} !"
echo $greeting $greeting1

## 单引号拼接
greeting2='hello,'$your_name' !'
greeting3='hello, ${your_name} !'
echo $greeting2, $greeting3 # 单引号内字符串变量是无效的


# 输出字符串长度
string="abcd"
echo ${#string}
echo ${#string[0]} # 变量为数组时，${#string} ${#string[0]}等价


# 提取字符子串
string="runoob is a great site."
echo ${string:1:4}
```



# shell变量



## 变量名的规则



定义变量时，变量名不加美元符号，`$`：

```shell
your_name="runoob"
```

`注意`：变量名和等号之间不能有空格。同时，变量名的命名必须遵守下面的规则：

- 命名只能使用英文字母，数字和下划线，首字母不能以数字开头。
- 中间不能有空格，可以使用下划线 `_`。
- 不能使用标点符号。
- 不能使用base里的关键字（可用help命令查看保留的关键字）

有效的shell变量名示例如下：

```shell
RUNOOB
LD_LIBRARY_PATH
_var
var2
```

无效的变量名：

```shell
?var=123
user*name=runoob
```

除了显式的直接赋值，还可以用语句给变量直接赋值，如：

```shell
for file in `ls /etc`
或
for file in $(ls /etc)
```

以上语句将 `/etc` 下目录的文件名循环出来。



## 使用变量

使用一个定义过的变量，只要在变量名前加美元符号即可，如：

```shell
your_name="runoob"
echo $your_name
echo ${your_name}
```

**注意：**变量名外面的花括号是可加可不加的，加花括号是为了帮助解释器识别变量的边界，如：

```shell
for skill in Ada Coffe Action Java; do
	echo "I am good at ${skill}Script."
done
```

如果不给skill加花括号，写成 `echo "I am good at $skillScript."`，解释器就会把 `$skillScript` 当成一个变量（其值为空），代码执行就不是我们期待的样子了。

**推荐给所有的变量加上花括号，这是一个编程的好习惯。**

**已定义的变量，可以被重新定义。**

```shell
your_name="runoob"
echo $your_name
your_name="what are you doing?"
echo $your_name
```

这样写是 **合法的**，但注意，第二次赋值的时候不能写成 `$your_name="what are you doing?"`，使用变量的时候才加美元符号 `$`。



## 只读变量

使用 `readonly` 命令可以将变量定义为只读变量，只读变量不能被改变。

```shell
#!/bin/bash

myUrl="https://www.google.com"
readonly myUrl
myUrl="https://www.baidu.com"
```

运行脚本，执行结果如下：

```shell
/bin/bash: myUrl 是一个只读变量。
```



## 删除变量

使用 `unset` 命令可以删除变量：

```shell
#!/bin/bash

myUrl="https://www.baidu.com"
unset myUrl
echo $myUrl
```

以上实例执行将没有任何输出。

**注：** `unset`命令不能删除只读命令。



## 变量类型

运行shell时，会同时存在三种变量：

- 1）**局部变量** 局部变量在脚本或命令中定义仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- 2）**环境变量** 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常的运行。必要的时候shell脚本也可以定义环境变量。
- 3）**shell变量** shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行



## Shell字符串

**字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其他的类型好用），字符串可以使用单引号，也可以使用双引号，也可以不用引号。**



### 单引号

```shell
str='this is a string.'
```

单引号字符串的限制：

- 单引号里的任何字符都会原样输出，单引号字符里的变量也是无效的。
- 单引号字符串中不能出现单独一个的单引号（对单引号使用转义符后也不行，即使使用了 `-e`进行转义，也只能对 `\n`， `\r`, `\t` 等字符进行转义，而不能进行 `\"` 的转义），但可以成对出现，作为字符串的拼接使用。



### 双引号

```shell
your_name="runoob"
str="Hello, I know you are \"$your_name\" !\n" # 双引号可以自动对 \" 进行转义，但是不能自动转义\n, \n 的转义需要在输出时使用 -e 进行转义
echo -e $str
```

输出结果：

```shell
Hello，I know you are "runoob" !
```

双引号的优点：

- 双引号里可以有变量，并且变量值生效。
- 双引号里可以出现转义字符：`\"` 等，并且自动进行转义。
- 双引号里的 `\n`, `\r`, `\t` 等字符需要在输出时使用 `-e` 打开转义的功能。



### 拼接字符串

实例

```shell
your_name="runoob"
# 使用双引号进行拼接
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"
echo $greeting $greeting_1

# 使用单引号进行拼接
greeting_2='hello, '$your_name' !'
greeting_3='hello, ${your_name} !'
echo $greeting_2 $greeting_3
```

输出结果：

```shell
hello, runoob! hello, runoob!
hello, runoob! hello, ${your_name} !
```

总结：

- 无论使用双引号还是单引号拼接，都可以正常输出变量值。
- 双引号里面的变量值可以正常输出，单引号里面的变量值不能正常输出 。



### 获取字符串的长度

实例

```shell
string="abcd"
echo ${#string} # 输出 4
```

变量为数组时，`${#string}`等价于 `${#string[0]}`：

```shell
string="abcd"
echo ${#string[0]} # 输出 4
```



#### 在shell中求长度的几种方式

> **使用$#直接求解**

echo ${#变量名}

```shell
[root@localhost SHELL]# TE=muyingshiguang
[root@localhost SHELL]# echo ${#TE}
14
[root@localhost SHELL]# 
```

> **使用管道符与wc的组合求解**

echo $变量名 | wc -L

```shell
[root@localhost SHELL]# echo $TE | wc -L
14
[root@localhost SHELL]# 
```

> **使用expr命令求解**

expr length "$变量名"

```shell
[root@localhost SHELL]# expr length "$TE"
14
[root@localhost SHELL]#
```

> **使用awk求解**

echo $TE | awk '{print length($0)}'

```shell
[root@localhost SHELL]# echo $TE | awk '{print length($0)}'
14
[root@localhost SHELL]# 
```

