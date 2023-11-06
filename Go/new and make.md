# Go 语言中函数 new 和 make 的使用与区别

## 前言

本文主要给大家介绍了 Go 语言中函数 new 与 make 的使用和区别，关于 Go 语言中 new 和 make 是内建的两个函数，主要用来创建分配类型内存。在我们定义生成变量的时候，可能会觉得有点迷惑，其实他们的规则很简单，下面我们就通过一些示例说明他们的区别和使用，话不多说了，来一起看看详细的介绍吧。



## 变量的声明

```go
var i int
var s string
```

变量的声明可以通过 var 关键字，然后就可以在程序中使用。当我们不指定变量的默认值时，这些变量的默认值就是他们的零值，比如 int 类型的零值是 0，string 类型的零值是 “”，引用类型的零值是 nil。

对于例子中的两种类型的声明，我们可以直接使用，对其进行赋值输出。

但是如果我们换成引用类型呢？

```go
package main
import "fmt"
func main(){
    var  i *int
    *i = 10
    fmt.Println(*i)
}
```

这个例子会打印出什么呢？0 还是 10。

以上全错，运行的时候会输出 panic，原因如下：

```go
panic：runtime error：invalid memory address or nil pointer dereference
```

**从这个提示中我们可以看出，对于引用类型的变量，我们不光要声明它，而且还要为他分配内容空间，否则我们的值放在哪里去呢，这就是上面的错误的原因。**

对于值类型的变量声明不需要为它分配内容空间，是因为已经帮我们默认分配好了。

要分配内存，就引出了今天的 new 和 make 。



## new

对于上面的问题我们如何解决呢？既然我们已经知道了没有为其分配内存，那么就可以使用 new 为其分配一个内存。

```go
func main(){
    var i *int
    i = new(int) // 还可以这样写：var i *int = new(int)
    *i = 10
    fmt.Println(*i)
}
```

现在再运行程序，完美PASS，打印 10,。现在让我们看下 new 这个内置函数的功能。

```go
// The new built-in function allocates memory. The first argument is a type,
// not a value, and the value returned is a pointer to a newly
// allocated zero value of that type.
func new(Type) *Type
```

他只接受一个参数，这个参数是一个类型，分配好内存以后，返回一个指向该类型内存地址的指针。同时请注意它同时把分配的内存置为零，也就是类型的零值。

我们的例子中，如果没有 *i = 10，那么打印的就是 0 。这里体现不出来 new 函数这种内存置为零的好处，我们再看一个例子。

```go
func main(){
    u := new(user)
    u.lock.Lock()
    u.name = "张三"
    u.lock.Unlock()
    fmt.Println(u)
}

type user struct {
	lock sync.Mutex
    name string
    age int
}
```

示例中的 user 类型中的 lock 字段我不用初始化，直接可以拿来用，不会有无效引用异常，因为它已经被零值了。

这就是 new，它返回的永远是类型的指针，指向分配类型的内存地址。



## make

make 也适用于内存分配的，但是和 new 不同，它只用于 chan，map以及 slice切片的内存创建，而且他返回的类型就是这三个类型本身，而不是他们的指针类型，只能用于这三个类型，因为这三个类型就是引用类型，所以就没有必要返回他们的指针类型了。

**注意：**因为这三种类型是引用类型，所以必须得初始化，但是不是置为零值，这个和 new 不一样。

```go
func make(t Type, size ...IntegerType) Type
```

从函数声明中可以看出，返回的还是该类型。



make的三中不同的用法：

```go
1、make(map[string]Type)
2、make([]int, 2)
3、make([]int, 2, 4)
```

1、第一种用法，既缺少长度的参数，只传类型，这种用法**只能用在类型为 map 或 chan 的场景**，例如：**make([]int) 时会报错的**。这样**返回的空间长度都是默认为 0的**。

2、第二种类型，指定了长度，例如 **make([]int , 2) 返回的是一个长度为2的slice**。

3、第三种用法，第二个参数指定的是切片的长度，第三个参数是用来指定预留的空间长度，例如：`a := make([]int, 2, 4)` ，这里值得注意的是返回的切片 a 的总长度是 4，预留的意思并不是另外多出来的长度，其实是包含了前面2个已经切片的个数。所以举例子当你这样使用时：`a := make([]int, 4, 2)` 就会报语法错误。



因此，当我们为 slice 分配内存的时候，应当尽量预估到 slice 可能的最大长度，通过给 make 传第三个参数的方式给slice预留好内存空间，这样可以避免二次分配内存带来的开销，大大的提高程序的性能。



而事实上，我们很难预估切片的可能的最大长度，这种情况下，当我们调用 append 为 slice 追加元素的时候，golang为了尽可能的减少二次分配内存，并不是每一次都只增加一个单元的内存空间，而是遵循这样的扩容机制：

> 当有预留的未使用的空间时，直接对未使用的空间进行切片追加，当预留的空间全部使用完毕时，扩容的空间将会是当前的 slice 长度的一倍，例如当前slice的长度为4，进行一次append操作之后，cap（a）返回的长度将会是8。

```go
package main

import "fmt"

func main(){
    a := make([]int, 0)
    n := 20
    for i:=0; i<n; i++ {
        a = append(a, i)
        fmt.Printf("len=%d cap=%d\n", len(a), cap(a))
    }
}
```

output：

```go
len=1 cap=1  // 第一次扩容
len=2 cap=2 // 第二次扩容
len=3 cap=4 // 第三次扩容
len=4 cap=4
len=5 cap=8 // 第四次扩容
len=6 cap=8
len=7 cap=8
len=8 cap=8
len=9 cap=16 // 第五次扩容
len=10 cap=16
len=11 cap=16
len=12 cap=16
len=13 cap=16
len=14 cap=16
len=15 cap=16
len=16 cap=16
len=17 cap=32 // 第六次扩容
len=18 cap=32
len=19 cap=32
len=20 cap=32
```

以上测试结果表明，每次扩容后，内存空间长度会变为原来的两倍。

好奇的我想试一下，如果一直这样扩展下去的话，理论上会呈指数扩展，然而事实真的会这样吗，我继续进行append操作，后续的输出是这样的:

```go
0 0
1 1
2 2
4 4
8 8
16 16
32 32
64 64
128 128
256 256
512 512
1024 1024
1312 1312    // 288
1696 1696    // 384
2208 2208    // 512
3072 3072    // 864
4096 4096    // 1024
5120 5120    // 1024
7168 7168    // 2048
9216 9216    // 2048
```

上面的输出忽略掉了中间没有扩容的情况。可以看到，前11次扩容确实是每次扩展一倍的长度，不过第12次扩容，明显没有按照预期扩展到2048。



接下来我们来看一些具体的实例：

> slice

```go
var slice_ []int = make([]int,5,10)
fmt.Println(slice_)
 
var slice_1 []int = make([]int,5)
fmt.Println(slice_1)
 
var slice_2 []int = []int{1,2}
fmt.Println(slice_2)
 
打印结果：
[0 0 0 0 0]
[0 0 0 0 0]
[1,2]
```



> map

```go
var m_ map[string]int = make(map[string]int)
m_["one"] = 1
fmt.Println(m_)
 
var m map[string]int = map[string]int{"1":1} // 初始化
m["2"] = 2
fmt.Println(m)
 
打印结果：
map[one:1]
map[1:1 2:2]
```

根据 size 大小来初始化分配内存，不过分配后的 map 长度为 0，如果 size 被忽略了，那么会在初始化分配内存时分配一个小尺寸的内存



> channel

管道缓冲区依据缓冲区容量被初始化。如果容量为 0 或者忽略容量，管道是没有缓冲区的。











## 二者异同

所以中这里我们可以看得很明白，二者都是内存上的分配（堆上），但是 **make只用于 slice、map以及 channel的初始化（非零值）；而 new 用于类型的内存分配，并且内存置为零。** 



**make 返回的还是这三个引用类型本身；而 new 返回的是指向类型的指针。**



其实 **new 不常用**，new 这个内置函数，可以给我们分配一块内存让我们使用，但是现实的代码中，它是不常用的。我们通常都是采用短语句声明以及结构体的字面量达到我们的目的，比如：

```go
i := 0
u := user{}
```

这样更简洁方便，而且也不涉及到指针这么复杂的操作。

**make 函数是无可替代的，我们现在使用 slice、map以及 channel 的时候，还是要使用 make 进行初始化，然后才可以对他们进行操作。**



————————————————
原文作者：DukeAnn
转自链接：https://learnku.com/articles/23533