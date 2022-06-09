[TOC]

# 安装

## window安装

> 官网下载地址

[https://go.dev/dl/](https://go.dev/dl/)

> 安装

安装过程可以参考：[https://go.dev/doc/install](https://go.dev/doc/install)

同时也可以参考：[Go安装详细教程](https://www.jb51.net/article/198515.htm)

==值得注意一点==：

- 在 window 中安装完成之后可以使用 `go version` 来检测是否安装成功
- 在任意盘中新建文件夹 GoWorks，里面再新建三个文件夹：bin，src，pkg
- 把GoWorks 添加到用户变量中去

![image-20220605182557677](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202206051825694.png)



> GoLand的使用教程可以参考

[Go语言集成开发环境之Goland](https://www.cnblogs.com/randysun/p/15396074.html#%E8%BF%90%E8%A1%8C-goland-%E5%B9%B6%E6%BF%80%E6%B4%BB)



## linux安装

关于 linux的安装，可以参考自己的另一篇笔记：区块链的安装

同样也可以参考：[官网linux安装](https://go.dev/doc/install)



# Go语言结构

## Go Hello World 实例

> Go 语言 的基础组成有以下几个部分

- 包声明
- 引入包
- 函数
- 变量
- 语句 & 表达式
- 注释

> 实例

```go
package main

import "fmt"

func main(){
    /* 这是注释*/
	fmt.Println("Hello World!")
}
```

**各部分的说明：**

1、第一行代码 `package main` 定义了包名，你必须在源文件中非注释的第一行指明这个文件属于哪个包，如：package main。package main表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 main 的包。

2、import "fmt" 告诉Go编译器这个程序需要使用fmt包（的函数，或其他元素），fmt包实现了格式化IO（输入/输出）的函数。

3、func main（）是程序开始执行的函数。main函数是每一个可执行程序必须包含的，一般来说都是在启动后第一个执行的函数（如果有 init（）函数则会先执行该函数）。

4、下一行 /* */ 是注释。单注释： // 

5、fmt.Println()：可以将字符串输出到控制台，并在最后自动增加换行字符 \n。

​      fmt.Print()：则是不换行的输出。

6、当标识符（包括常量、变量、类型、函数名、结构字段等）以一个大写字母开头，如：Group1，那么这种形式的标识符就可以被外部的包所使用的（前提是这个客户端程序先导入这个包），这被称为导出（像面向对象的public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们可以在整个包的内部可见并使用（像面向对象的protected）。



> 运行程序 hello.go

```go
go run hello.go
Hello World!

// 或者

go build hello.go
ls
hello hello.go
./hello
Hello World!
```

**注意：**

`{` 不能单独放到一行，否则运行时会产生错误。



# Go语言基础语法

## Go标记

> Go 程序可以由多个标记组成，可以是关键字，标识符，常量，字符串，符号。

```go
fmt.Println("Hello World!")
```

6标记是（每行一个）：

```go
fmt
.
Println
(
"Hello World!"
)
```



## 行分隔符

在 Go 程序中，一行代表一个语句结束，每个语句不必像 C 语言那样以分号 ；结尾，因为这些工作都是由go编译器自动完成。如果你打算将多行语句写到一行，则必须使用 ；人为区分，但是实际开发中并不支持这种做法。



## 注释

```
// 单行注释
/* 多行注释 */
```



## 标识符

一个标识符通常是由一个或多个字母（含大小写），数字，下划线_ 组成的序列，但是第一个字符必须是字母或者下划线而不能是数字。



以下是无效的标识符：

- 1ab（以数字开头）
- case（Go语言的关键字）
- a+b（运算符是不允许的）



## 字符串链接

Go 语言的字符串的链接通常使用 `+` 实现。



## 关键字

下面列举了 Go 语言中常用的25个关键字或者保留字：

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

除了以上的关键字，Go语言还有36个预定义的标识符：

| append | bool    | byte    | cap     | close  | complex | complex64 | complex128 | uint16  |
| ------ | ------- | ------- | ------- | ------ | ------- | --------- | ---------- | ------- |
| copy   | false   | float32 | float64 | imag   | int     | int8      | int16      | uint32  |
| int32  | int64   | iota    | len     | make   | new     | nil       | panic      | uint64  |
| print  | println | real    | recover | string | true    | uint      | uint8      | uintptr |

程序一般由关键字，常量，变量，运算符，类型和函数组成。

程序中可能会用到的分隔符：括号（），中括号[]和大括号{}



## Go 语言的空格

1、Go 语言的声明必须使用空格隔开，如：

```go
var age int
```

```go
fruit = apples + oranges
```



## 格式化字符串

Go语言使用 `fmt.Sprintf()` 格式化字符串并赋值给新串：

```go
package main

import "fmt"

func main(){
	// %d 表示整形数字，%s表示字符串
	var stockcode = 123
	var enddate = "2022-10-23"
	var url = "Code=%d&enddate=%s"
	var target_url = fmt.Sprintf(url, stockcode, enddate)
	fmt.Println(target_url)
}
```

输出结果：

```
Code=123&enddate=2022-10-23
```

更的内容参见：

[fmt.Sprintf格式化字符串](./fmt.Sprintf格式化字符串.md)



# Go语言数据类型

在 Go 语言中，数据类型是为了声明函数和变量。

数据类型的出现是为了把数据分成所需内存大小不同的数据，编程的时候需要用到大数据时才需要申请大内存，就可以充分利用内存。



## 类型分类

Go 语言按类别有以下几种数据类型：

| 序号 | 类型和描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | **布尔型**<br />布尔型的值只可以是常量true 或者 false。一个简单的例子：var b bool = true。 |
| 2    | **数字类型**<br />整型int和浮点型float32，float64，Go语言支持整型和浮点型数字，并且支持复数，其中位的运算采用补码。 |
| 3    | **字符串类型**<br />一串固定长度的字符连接起来的字符序列。Go 的字符串是由单个字符连接起来的。Go语言的字符串使用 UTF-8 编码标识 Unicode 文本。 |
| 4    | **派生类型**：<br />包括：<br />- 指针类型（Pointer）<br />- 数组类型<br />- 结构化类型（struct）<br />- Channel 类型<br />- 函数类型<br />- 切片类型<br />- 接口类型<br />- Map类型 |



## 数字类型

### 整型

Go 语言也有基于架构的类型，例如：int，uint和uintptr。

| 序号 | 类型和描述                                                   |
| ---- | ------------------------------------------------------------ |
| 1    | uint8<br />无符号 8 位整型（0 到 255）                       |
| 2    | uint16<br />无符号 16 位整型（0 到 65535）                   |
| 3    | uint32<br />无符号 32  位整型（0 到 4294967295）             |
| 4    | uint64<br />无符号 64 位整型（0 到 18446744073709551615）    |
| 5    | int8<br />有符号 8 位整型（-128 到 127）                     |
| 6    | int16<br />有符号 16 位整型（-32768 到 32767）               |
| 7    | int32<br />有符号 32 位整型（-2147483648 到 2147483647）     |
| 8    | int64<br />有符号 64 位整型（-9223372036854775808 到 9223372036854775807） |



### 浮点型

| 序号 | 类型和描述                          |
| ---- | ----------------------------------- |
| 1    | float32<br />IEEE-754 32 位浮点型数 |
| 2    | float64<br />IEEE-754 64 位浮点型数 |
| 3    | complex64<br />32 位实数和虚数      |
| 4    | complex128<br />64位实数和虚数      |



### 其他数字类型

| 序号 | 类型和描述                                |
| ---- | ----------------------------------------- |
| 1    | byte<br />类似 uint8                      |
| 2    | rune<br />类似 int32                      |
| 3    | uint<br />32 和 64 位                     |
| 4    | uintptr<br />无符号整型，用于存放一个指针 |



# Go 语言变量

变量可以通过变量名访问。

Go 语言变量由字母，数字，下划线组成，其中有个字母不能是数字。

声明变量的一般形式使用 var 关键字：

```go
var identifier type
```

可以一次声明多个变量：

```go
var identifier1，identifier2 type
```



实例：

```go
package main
import "fmt"
func main(){
	var a string = "Runoob"
	fmt.Println(a)
	
	var b, c int = 1, 2
	fmt.Println(b, c)
}
```

输出结果：

```go
Runoob
1 2
```



## 变量声明

> 第一种，指定变量类型，如果没有初始化，则变量默认认为零值。

```go
var v_name v_type
v_name = value
```

零值就是变量没有做初始化时系统默认设置的值。

```go
package main
import "fmt"
func main(){
	// 声明一个变量并初始化
	var a = "RUNOOB"
	fmt.Println(a)
	
	// 没有初始化就为零值
	var b int
	fmt.Println(b)
	
	// bool 零值为 false
	var c bool
	fmt.Println(c)
}
```

以上的输出结果：

```go
RUNOOB
0
false
```

- 数值类型（包括complex64|128）为0
- 布尔类型为 false
- 字符串为 “” （空字符串）
- 以下几种类型为 nil：

```go
var a *int
var a []int
var a map[string] int
var a chan int
var a func(string) int
var a error // error 是接口
```

实例：

```
package main
import "fmt"
func main(){
	var i int
	var f float64
	var b bool
	var s string
	fmt.Println("%v %v %v %v", i, f, b, s)
}
```

输出结果：

```go
0 0 false ""
```



> 第二种，根据值自行判定变量类型

```go
var v_name = value
```

实例：

```go
package main
import "fmt"
func main(){
	var d = true
	fmt.Println(d)
}
```

输出结果：

```go
true
```



> 第三种，如果变量已经使用 var 声明过，再使用 := 声明变量，就会产生编译错误，格式：

```go
v_name := value
```

例如：

```go
var intVal int
intVal := 1 // 此时就会产生编译错误，因为 intVal 已经声明，不需要重新声明
```

而是直接使用下面的语句即可：

```go
intVal := 1 // 此时不会产生编译错误，因为有声明新的变量，因为 := 是一个声明语句
```

**intVal := 1 相当于：**

```go
var intVal int
intVal = 1
```

因此，可以将 `var f string = "Runoob"` 简写成：`f := Runoob` ：

实例：

```go
package main
import "fmt"
func main(){
	f := "Runoob" // var f string = "Runoob"
	fmt.Println(f)
}
```

输出结果：

```go
Runoob
```



## 多变量声明

```go
// 类型相同多个变量，非全局变量
var vname1, vname2, vname3 type
vname1, vname2, vname3 = v1, v2, v3

var vname1, vname2, vname3 = v1, v2, v3 // 和python很像，不需要显示声明类型，自动推断

vname1, vname2, vname3 := v1, v2, v3 // 出现在 := 左侧的变量不应该是已经声明过的变量，否则会提示编译错误


// 这种因式分解关键字的写法一般用于声明全局变量
var (
	vname1 v_type1
	vname2 V_type2
)
```

实例：

```go
package main

var x, y int
var ( // 这种因式分解关键字的写法一般用于声明全局变量
	a int
	b bool
)

var c, d int = 1, 2
var e, f = 123, "hello"

// 这种不带声明格式的只能在函数体中提现
// g, h := 123, "hello"

func main(){
	g, h := 123, "hello"
	println(x, y, a, b, c, d, e, f, g, h) // 打印的时候字是红色的
}
```

以上实例的输出结果是：

```go
0, 0, 0, false, 1, 2, 123, "hello", 123, "hello"
```



### 值类型和引用类型

**所有像int，float，bool和string这些基本类型都属于值类型，使用这些类型的变量直接指向存在内存中的值：**

![image-20220606174809988](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202206061748225.png)



**当使用等号 = 将一个变量的值赋值给另一个变量的时候，如：`j = i`，实际上是在内存中将 i 的值进行了拷贝：**

![image-20220606205724959](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202206062057175.png)

你可以根据 `&i` 来获取变量 i 的内存地址，例如：0xf840000040（每次的地址都可能不一样）。

值类型变量的值存储在推中。

内存地址会根据机器的不同而有所不同，甚至相同的程度在不同的机器上执行后也会有不同的内存地址。因为每台机器可能有不同的存储器布局，并且位置分配也可能不同。

更复杂的数据通常会需要使用多个字，这些数据一般需要使用引用类型保存。

一个引用类型的变量 r1 存储的是 r1 的值所在的内存地址（数字），或内存地址中第一个字所在的位置。

![image-20220607005624667](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202206070056733.png)

这个内存地址称之为指针，这个指针实际上也被存储在另外的某一个值中。

同一个引用类型的指针指向的多个字可以是在连续的内存地址中（内存布局是连续的），这也是计算效率高的一种存储形式；也可以将这些字分散存放在内存中，每个字都指向下一个字所在的内存地址。

当使用赋值语句 r2 = r1 时，只有引用（地址）被复制了。

如果 r1 的值被改变了，那么这个值的所有引用都会指向被修改后的内容，在这个例子中，r2 也会受到影响。



## 简短形式，使用 := 赋值操作符

在变量的初始化时，可以省略变量的类型而由系统自动推断，声明语句写上 var 关键字显得有些多余了，因此我们可以简写为 `a := 50` 或 `b := false`。

a 和 b 的类型可以由编译期自动推断。

这是使用变量的首选形式，但是他只能被使用在函数体内，而不可以用于全局变量的生命和赋值。使用操作符 `:=` 可以高效地创建一个新的变量，称之为初始化声明。



**注意事项：**

 如果在相同的代码块中，我们不可以再次对于相同名称的变量使用初始化声明，例如：`a := 20` ，如果 a 已经被声明过，则这条语句是不被允许的，编译器会提示错误 `no new variables on left side of :=`，但是 a = 20 是可以的，因为这是给相同的变量赋予一个新值。

如果你在声明 a 之前使用它，那么会提示错误 undefined：a。

如果你声明了一个局部变量却没有相同的代码块使用它，同样会得到编译错误，例如下面这个例子当中的变量 a：

实例：

```go
package main
import "fmt"

func main(){
	var a string = "abc"
	fmt.Println("hello, world!")
}
```

尝试编译这段代码将得到错误 a declared but not used。

此外，单纯地给 a 赋值也是不够的，这个值必须被使用，所以使用

```go
fmt.Println("hello, world!", a)
```

会移除错误。

但是全局变量是允许声明但是不使用的。同一类型的多个变量可以声明在同一行，如：

```go
var a, b, c int
```

多变量可以在同一行进行赋值，如：

```go
var a, b int
var c string
a, b, c = 5, 7, "abc"
```

上面这段假设变量a，b，c都已经被声明过，否则的话应该使用：

```go
a, b, c := 5, 7, "abc"
```

右边的这些值以相同的顺序赋值给左边的变量，所以a的值是5，b的值是7，c的值是 ”abc“。

这被称为 并行 或 同时 赋值。

如果你想交换两个变量的值，你可以使用：

```go
a, b = b, a
```

两个变量的类型必须是相同的。



空白标识符 `_` 也被用于抛弃值，如值 5 在：_，b = 5，7 中被抛弃。

`_` 实际上是一个只写变量，你不能得到它的值。这样做是因为 Go 语言中你必须使用所有被声明的变量，但有时你并不需要使用从一个函数返回的所有值。

并行赋值也被用于当一个函数返回多个值时，比如这里的 val 和 错误err 是通过Func1函数同时得到的：

```go
val, err = Func1(val1)
```

实例：

```go
package main 
import "fmt"
func main(){
	_, numb, strs = numbers() // 只获取函数返回的后两个
	fmt.Println(numb, strs)
}

func numbers()(int, int, string){
	a, b, c := 1, 2, "str"
	return a, b, c
}
```























































