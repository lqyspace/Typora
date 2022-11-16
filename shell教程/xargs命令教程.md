# xargs命令教程

`xargs`是 Unix 系统的一个很有用的命令，但是常常被忽视，很多人不了解它的用法。

本文介绍如何使用这个命令：

`xargs`（英文全拼： `eXtended ARGuments`）是给命令传递参数的一个过滤器，也是组合多个命令的一个工具。

`xargs` 可以将管道或标准输入（`stdin`）数据转换成命令行参数，也能够从文件的输出中读取数据。

`xargs` 也可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行。

`xargs` 默认的命令是 echo，这意味着通过管道传递给 `xargs` 的输入将会包含换行和空白，不过通过 `xargs` 的处理，**换行和空白将被空格取代**。

`xargs` 是一个强有力的命令，它能够捕获一个命令的输出，然后传递给另外一个命令。

之所以能用到这个命令，关键是由于很多命令不支持|管道来传递参数，而日常工作中有有这个必要，所以就有了 `xargs` 命令，例如：

```bash
find /sbin -perm +700 |ls -l       #这个命令是错误的
find /sbin -perm +700 |xargs ls -l   #这样才是正确的
```



## 标准输入与管道命令

Unix 命令都带有参数，有些命令可以接受"标准输入"（`stdin`）作为参数。

```bash
$ cat /etc/passwd | grep root
```

上面的代码使用了管道命令（`|`）。管道命令的作用，是将左侧命令（`cat /etc/passwd`）的标准输出转换为标准输入，提供给右侧命令（`grep root`）作为参数。

因为`grep`命令可以接受**标准输入**作为参数，所以上面的代码等同于下面的代码。

```bash
$ grep root /etc/passwd
```

但是，大多数命令都不接受标准输入作为参数，只能直接在命令行输入参数，这导致无法用管道命令传递参数。举例来说，`echo`命令就不接受管道传参。

```bash
$ echo "hello world" | echo
```

上面的代码不会有输出。因为管道右侧的`echo`不接受管道传来的标准输入作为参数。



## xargs 命令的作用

`xargs`命令的作用，是将**标准输入转为命令行参数**。

```bash
$ echo "hello world" | xargs echo
hello world
```

上面的代码将管道左侧的标准输入，转为命令行参数`hello world`，传给第二个`echo`命令。

`xargs`命令的格式如下。

```bash
$ somecommend | xargs [-options] [command]
```

真正执行的命令，紧跟在`xargs`后面，接受`xargs`传来的参数。

`xargs`的作用在于，大多数命令（比如`rm`、`mkdir`、`ls`）与管道一起使用时，都需要`xargs`将标准输入转为命令行参数。

```bash
$ echo "one two three" | xargs mkdir
```

上面的代码等同于`mkdir one two three`。如果不加`xargs`就会报错，提示`mkdir`缺少操作参数。



**参数：**

- `-a file` 从文件中读入作为 `stdin`
- `-e flag` ，注意有的时候可能会是`-E`，`flag`必须是一个以空格分隔的标志，当`xargs`分析到含有flag这个标志的时候就停止。
- `-p` 当每次执行一个`argument`的时候询问一次用户。
- `-n num` 后面加次数，表示命令在执行的时候一次用的`argument`的个数，默认是用所有的。
- `-t` 表示先打印命令，然后再执行。
- `-i` 或者是`-I`，这得看Linux支持了，将`xargs`的每项名称，一般是一行一行赋值给 `{}`，可以用 `{}` 代替。
- `-r no-run-if-empty` 当`xargs`的输入为空的时候则停止`xargs`，不用再去执行了。
- `-s num` 命令行的最大字符数，指的是 `xargs` 后面那个命令的最大命令行字符数。
- `-L num` 从标准输入一次读取 `num` 行送给 `command` 命令。
- `-l` 同 `-L`。
- `-d delim` 分隔符，默认的`xargs`分隔符是回车，`argument`的分隔符是空格，这里修改的是`xargs`的分隔符。
- `-x exit`的意思，主要是配合`-s`使用。。
- `-P` 修改最大的进程数，默认是1，为0时候为as many as it can ，这个例子我没有想到，应该平时都用不到的吧。



### 实例

`xargs` 用作替换工具，读取输入数据重新格式化后输出。

定义一个测试文件，内有多行文本数据：

```bash
# cat test.txt

a b c d e f g
h i j k l m n
o p q
r s t
u v w x y z
```

多行输入单行输出：

```bash
# cat test.txt | xargs
a b c d e f g h i j k l m n o p q r s t u v w x y z
```

`-n` 选项多行输出：

```bash
# cat test.txt | xargs -n3

a b c
d e f
g h i
j k l
m n o
p q r
s t u
v w x
y z
```

`-d` 选项可以自定义一个定界符：

```
# echo "nameXnameXnameXname" | xargs -dX

name name name name
```

结合 `-n` 选项使用：

```
# echo "nameXnameXnameXname" | xargs -dX -n2

name name
name name
```

读取 `stdin`，将格式化后的参数传递给命令

假设一个命令为 `sk.sh` 和一个保存参数的文件 `arg.txt`：

```
#!/bin/bash
#sk.sh命令内容，打印出所有参数。

echo $*
```

`arg.txt`文件内容：

```
# cat arg.txt

aaa
bbb
ccc
```

`xargs` 的一个选项 `-I`，使用 `-I` 指定一个替换字符串 `{}`，这个字符串在 `xargs` 扩展时会被替换掉，当 `-I` 与 `xargs` 结合使用，每一个参数命令都会被执行一次：

```
# cat arg.txt | xargs -I {} ./sk.sh -p {} -l

-p aaa -l
-p bbb -l
-p ccc -l
```

复制所有图片文件到 /data/images 目录下：

```
ls *.jpg | xargs -n1 -I {} cp {} /data/images
```

`xargs` 结合 `find` 使用。

用 rm 删除太多的文件时候，可能得到一个错误信息：**/bin/rm Argument list too long.** 用 xargs 去避免这个问题：

```
find . -type f -name "*.log" -print0 | xargs -0 rm -f
```

xargs -0 将 \0 作为定界符。

统计一个源代码目录中所有 php 文件的行数：

```
find . -type f -name "*.php" -print0 | xargs -0 wc -l
```

查找所有的 jpg 文件，并且压缩它们：

```
find . -type f -name "*.jpg" -print | xargs tar -czvf images.tar.gz
```

xargs 其他应用

假如你有一个文件包含了很多你希望下载的 URL，你能够使用 xargs下载所有链接：

```
# cat url-list.txt | xargs wget -c
```



## xargs 的单独使用

`xargs`后面的命令默认是`echo`。

```bash
$ xargs
# 等同于
$ xargs echo
```

大多数时候，`xargs`命令都是跟管道一起使用的。但是，它也可以单独使用。

输入`xargs`按下回车以后，命令行就会等待用户输入，作为标准输入。你可以输入任意内容，然后按下`Ctrl d`，表示输入结束，这时`echo`命令就会把前面的输入打印出来。

```bash
$ xargs
hello (Ctrl + d)
hello
```

再看一个例子。

```bash
$ xargs find -name
"*.txt"
./foo.txt
./hello.txt
```

上面的例子输入`xargs find -name`以后，命令行会等待用户输入所要搜索的文件。用户输入`"*.txt"`，表示搜索当前目录下的所有 TXT 文件，然后按下`Ctrl d`，表示输入结束。这时就相当执行`find -name *.txt`。



## -d 参数与分隔符

默认情况下，`xargs`将换行符和空格作为分隔符，把标准输入分解成一个个命令行参数。

```bash
$ echo "one two three" | xargs mkdir
```

上面代码中，`mkdir`会新建三个子目录，因为`xargs`将`one two three`分解成三个命令行参数，执行`mkdir one two three`。

`-d`参数可以更改分隔符。

```bash
$ echo -e "a\tb\tc" | xargs -d "\t" echo
a b c
```

上面的命令指定制表符`\t`作为分隔符，所以`a\tb\tc`就转换成了三个命令行参数。`echo`命令的`-e`参数表示解释转义字符。



## -p 参数，-t 参数

使用`xargs`命令以后，由于存在转换参数过程，有时需要确认一下到底执行的是什么命令。

`-p`参数打印出要执行的命令，询问用户是否要执行。

```bash
$ echo 'one two three' | xargs -p touch
touch one two three ?...
```

上面的命令执行以后，会打印出最终要执行的命令，让用户确认。用户输入`y`以后（大小写皆可），才会真正执行。

`-t`参数则是打印出最终要执行的命令，然后直接执行，不需要用户确认。

```bash
$ echo 'one two three' | xargs -t rm
rm one two three
```



#### 打印出执行的命令

结合 `-t` 选项可以打印出 `xargs` 执行的命令

```
ls | xargs -t -I{} echo {}
```

会输出当前目录下的文件列表和执行的 echo 命令

#### 使用 -p 选项确认执行的命令

`-p` 选项会在执行每一个命令时弹出确认，当你需要非常准确的确认每一次操作时可以使用 `-p` 参数，比如，查找当前目录下 `.log` 文件，每一次删除都需要确认：

```
find . -maxdepth 1 -name "*.log" | xargs -p -I{} rm {}
```





## -0 参数与 find 命令

由于`xargs`默认将空格作为分隔符，所以不太适合处理文件名，因为文件名可能包含空格。

`find`命令有一个特别的参数`-print0`，指定输出的文件列表以`null`分隔。然后，`xargs`命令的`-0`参数表示用`null`当作分隔符。

```bash
$ find /path -type f -print0 | xargs -0 rm
```

上面命令删除`/path`路径下的所有文件。由于分隔符是`null`，所以处理包含空格的文件名，也不会报错。

还有一个原因，使得`xargs`特别适合`find`命令。有些命令（比如`rm`）一旦参数过多会报错"参数列表过长"，而无法执行，改用`xargs`就没有这个问题，因为它对每个参数执行一次命令。

```bash
$ find . -name "*.txt" | xargs grep "abc"
```

上面命令找出所有 TXT 文件以后，对每个文件搜索一次是否包含字符串`abc`。



**xargs 结合 find 使用**

用 rm 删除太多的文件时候，可能得到一个错误信息：`/bin/rm Argument list too long`. 用 `xargs` 去避免这个问题：

```shell
find . -type f -name "*.log" -print0 | xargs -0 rm -f
```

xargs -0 将 `\0` 作为定界符。

统计一个源代码目录中所有 php 文件的行数：

```shell
find . -type f -name "*.php" -print0 | xargs -0 wc -l
```

查找所有的 jpg 文件，并且压缩它们：

```shell
find . -type f -name "*.jpg" -print | xargs tar -czvf images.tar.gz
```





## -L 参数

如果标准输入包含多行，`-L`参数指定多少行作为一个命令行参数。

```bash
$ xargs find -name
"*.txt"   
"*.md"
find: paths must precede expression: `*.md'
```

上面命令同时将`"*.txt"`和`*.md`两行作为命令行参数，传给`find`命令导致报错。

使用`-L`参数，指定每行作为一个命令行参数，就不会报错。

```bash
$ xargs -L 1 find -name
"*.txt"
./foo.txt
./hello.txt
"*.md"
./README.md
```

上面命令指定了每一行（`-L 1`）作为命令行参数，分别运行一次命令（`find -name`）。

下面是另一个例子。

```bash
$ echo -e "a\nb\nc" | xargs -L 1 echo
a
b
c
```

上面代码指定每行运行一次`echo`命令，所以`echo`命令执行了三次，输出了三行。



## -n 参数

`-L`参数虽然解决了多行的问题，但是有时用户会在同一行输入多项。

```bash
$ xargs find -name
"*.txt" "*.md"
find: paths must precede expression: `*.md'
```

上面的命令将同一行的两项作为命令行参数，导致报错。

`-n`参数指定每次将多少项，作为命令行参数。

```bash
$ xargs -n 1 find -name
```

上面命令指定将每一项（`-n 1`）标准输入作为命令行参数，分别执行一次命令（`find -name`）。

下面是另一个例子。

```bash
$ echo {0..9} | xargs -n 2 echo
0 1
2 3
4 5
6 7
8 9
```

上面命令指定，每两个参数运行一次`echo`命令。所以，10个阿拉伯数字运行了五次`echo`命令，输出了五行。



## -I 参数

如果`xargs`要将命令行参数传给多个命令，可以使用`-I`参数。

`-I`指定每一项命令行参数的替代字符串。

```bash
$ cat foo.txt
one
two
three

$ cat foo.txt | xargs -I file sh -c 'echo file; mkdir file'
one 
two
three

$ ls 
one two three
```

上面代码中，`foo.txt`是一个三行的文本文件。我们希望对每一项命令行参数，执行两个命令（`echo`和`mkdir`），使用`-I file`表示`file`是命令行参数的替代字符串。执行命令时，具体的参数会替代掉`echo file; mkdir file`里面的两个`file`。



## --max-procs 参数

`xargs`默认只用一个进程执行命令。如果命令要执行多次，必须等上一次执行完，才能执行下一次。

`--max-procs`参数指定同时用多少个进程并行执行命令。`--max-procs 2`表示同时最多使用两个进程，`--max-procs 0`表示不限制进程数。

```bash
$ docker ps -q | xargs -n 1 --max-procs 0 docker kill
```

上面命令表示，同时关闭尽可能多的 Docker 容器，这样运行速度会快很多。



原链接：[https://www.ruanyifeng.com/blog/2019/08/xargs-tutorial.html](https://www.ruanyifeng.com/blog/2019/08/xargs-tutorial.html)

[https://wangchujiang.com/linux-command/c/xargs.html](https://wangchujiang.com/linux-command/c/xargs.html)
