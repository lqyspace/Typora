[TOC]

# Go语言常量

常量是一个简单值的标识符，在程序运行时，不会被修改的量。

常量中的数据类型只可以是布尔型，数字型（整型，浮点型和复数）和字符串型。

常量的定义格式：

```go
const indetifier [type] = value
```

你可以省略类型说明符 [type]，因为编译器可以根据变量的值来推断其类型。

- 显示类型定义：`const b string = "abc"`
- 隐式类型定义：`const b = "abc"`

多个相同类型的声明可以简写为：

```go
const c_name1, c_name2 = value1, value2
```

实例：

```go
package main 
import "fmt"
func main(){
	const LENGTH int = 10
	const WIDTH int = 5
	var area int
	const a, b, c = 1, false, "str" //多重赋值
	
	area = LENGTH * WIDTH
	fmt.Println("面积为：%d", area)
	println()
	println(a, b, c)
}
```

以上实际的运行结果：

```go
面积为：50
1 false str
```

常量还可以用于枚举：

```go
const (
	Unknown = 0
	Female = 1
	Male = 2
)
```

数字0，1，2分别代表未知性别，女性和男性。

常量可以用 `len()`，`cap()`，`unsafe.Sizeof()` 函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不通过：

实例：

```go
package main
import "unsafe"
const (
	a = "abc"
	b = len(a)
	c = unsafe.Sizeof(a)
)

func main(){
    println(a, b, c)
}
```

以上运行的实例为：

```go
abc 3 16
```



## iota

iota，特殊常量，可以认为是一个可以被编译修改的常量。

iota 在 const 关键字出现时将被重置为 0（const 内部的第一行之前），const中每新增一行常量声明将使 iota 计数一次（iota 可理解为 const 语句块中的索引）

iota可以被用作枚举值：

```go
const (
	a = iota
	b = iota
	c = iota
)
```

第一个iota等于0，每当iota在新的一行被使用时，它的值都会自动的加1；所以 a=0，b=1，c=2 可以简写如下的形式：

```go
const (
	a = iota
	b 
	c
)
```

> iota的用法

```go
package main
import "fmt"
func main(){
	const (
		a = iota // 0
		b        // 1
		c        // 2
		d = "ha" // 独立值，iota += 1
		e        // "ha"   iota += 1
		f = 100  // 100    iota += 1
		g        // 100    iota += 1
		h = iota // 7, 恢复计数
		i
	)
	fmt.Println(a, b, c, d, e, f, g, h, i)
}
```

以上实例运行的结果：

```go
0 1 2 ha ha 100 100 7 8
```



**再看一个有趣的实例：**

```go
package main
import "fmt"
const (
	i = 1<<iota
	j = 3<<iota
    k
    l
)

func main(){
	fmt.Println("i=",i)
	fmt.Println("j=",j)
	fmt.Println("k=",k)
	fmt.Println("l=",l)
}
```

以上的运行实例：

```go
i = 1
j = 6
k = 12
l = 24
```

iota 表示从 0 开始自动加 1，所以 **i=1<<0**, **j=3<<1**（**<<** 表示左移的意思），即：i=1, j=6，这没问题，关键在 k 和 l，从输出结果看 **k=3<<2**，**l=3<<3**。

简单表述:

- **i=1**：左移 0 位，不变仍为 1。
- **j=3**：左移 1 位，变为二进制 **110**，即 6。
- **k=3**：左移 2 位，变为二进制 **1100**，即 12。
- **l=3**：左移 3 位，变为二进制 **11000**，即 24。

注：**<<n==\*(2^n)**。





# Go 语言运算符

Go 语言内置的运算符有：

- 算术运算符
- 关系运算符
- 逻辑运算符
- 位运算符
- 赋值运算符
- 其他运算符



## 算数运算符

假定 A 的值为10，B 的值为20。

| 运算符 | 描述 | 实例               |
| :----- | :--- | :----------------- |
| +      | 相加 | A + B 输出结果 30  |
| -      | 相减 | A - B 输出结果 -10 |
| *      | 相乘 | A * B 输出结果 200 |
| /      | 相除 | B / A 输出结果 2   |
| %      | 求余 | B % A 输出结果 0   |
| ++     | 自增 | A++ 输出结果 11    |
| --     | 自减 | A-- 输出结果 9     |

实例：

```go
package main

import "fmt"

func main() {

   var a int = 21
   var b int = 10
   var c int

   c = a + b
   fmt.Printf("第一行 - c 的值为 %d\n", c )
   c = a - b
   fmt.Printf("第二行 - c 的值为 %d\n", c )
   c = a * b
   fmt.Printf("第三行 - c 的值为 %d\n", c )
   c = a / b
   fmt.Printf("第四行 - c 的值为 %d\n", c )
   c = a % b
   fmt.Printf("第五行 - c 的值为 %d\n", c )
   a++
   fmt.Printf("第六行 - a 的值为 %d\n", a )
   a=21   // 为了方便测试，a 这里重新赋值为 21
   a--
   fmt.Printf("第七行 - a 的值为 %d\n", a )
}
```

运行结果：

```go
第一行 - c 的值为 31
第二行 - c 的值为 11
第三行 - c 的值为 210
第四行 - c 的值为 2
第五行 - c 的值为 1
第六行 - a 的值为 22
第七行 - a 的值为 20
```



## 关系运算符

假定 A 的值为 10， B的值为20。

| 运算符 | 描述                                                         | 实例              |
| :----- | :----------------------------------------------------------- | :---------------- |
| ==     | 检查两个值是否相等，如果相等返回 True 否则返回 False。       | (A == B) 为 False |
| !=     | 检查两个值是否不相等，如果不相等返回 True 否则返回 False。   | (A != B) 为 True  |
| >      | 检查左边值是否大于右边值，如果是返回 True 否则返回 False。   | (A > B) 为 False  |
| <      | 检查左边值是否小于右边值，如果是返回 True 否则返回 False。   | (A < B) 为 True   |
| >=     | 检查左边值是否大于等于右边值，如果是返回 True 否则返回 False。 | (A >= B) 为 False |
| <=     | 检查左边值是否小于等于右边值，如果是返回 True 否则返回 False。 | (A <= B) 为 True  |

实例：

```go
package main
import "fmt"

func main(){
	var a int = 21
	var b int = 10
	if(a == b){
		fmt.Println("第一行 - a 等于 b")
	}else{
		fmt.Printf("第二行 - a 不等于 b\n")
	}
    if ( a < b ) {
      fmt.Printf("第二行 - a 小于 b\n" )
   } else {
      fmt.Printf("第二行 - a 不小于 b\n" )
   }
   
   if ( a > b ) {
      fmt.Printf("第三行 - a 大于 b\n" )
   } else {
      fmt.Printf("第三行 - a 不大于 b\n" )
   }
   /* Lets change value of a and b */
   a = 5
   b = 20
   if ( a <= b ) {
      fmt.Printf("第四行 - a 小于等于 b\n" )
   }
   if ( b >= a ) {
      fmt.Printf("第五行 - b 大于等于 a\n" )
   }
}
```

以上的运行结果：

```go
第一行 - a 不等于 b
第二行 - a 不小于 b
第三行 - a 大于 b
第四行 - a 小于等于 b
第五行 - b 大于等于 a
```





## 逻辑运算符

假定 A 的值为 True，B 的值为 False。

| 运算符 | 描述                                                         | 实例               |
| :----- | :----------------------------------------------------------- | :----------------- |
| &&     | 逻辑 AND 运算符。 如果两边的操作数都是 True，则条件 True，否则为 False。 | (A && B) 为 False  |
| \|\|   | 逻辑 OR 运算符。 如果两边的操作数有一个 True，则条件 True，否则为 False。 | (A \|\| B) 为 True |
| !      | 逻辑 NOT 运算符。 如果条件为 True，则逻辑 NOT 条件 False，否则为 True。 | !(A && B) 为 True  |

实例：

```go
package main
import fmt
 
func main(){
	var a bool = true
	var b bool = false
	if(a && b){
		fmt.Println("第一行 - 条件为 true")
	}
	if(a || b){
		fmt.Println("第二行 - 条件为 true")
	}
    /* 修改 a 和 b 的值 */
    a = false
    b = true
    if(a && b){
        fmt.Printf("第三行 - 条件为 true \n")
    }else{
        fmt.Printf("第三行 - 条件为 false \n")
    }
    if(!(a&&b)){
        fmt.Println("第四行 - 条件为 true")
    }
}
```

运行结果：

```go
第二行 - 条件为 true
第三行 - 条件为 false
第四行 - 条件为 true
```



## 位运算符

位运算符对整数在内存中的二进制位进行操作。

下表列出了位运算符 &、| 和 ^ 的计算。

| p    | q    | p&q  | p\|q | p^q  |
| ---- | ---- | ---- | ---- | ---- |
| 0    | 0    | 0    | 0    | 0    |
| 0    | 1    | 0    | 1    | 1    |
| 1    | 0    | 0    | 1    | 1    |
| 1    | 1    | 1    | 1    | 0    |

假定 A = 60；B = 13；其二进制数据转换为：

> A = 0011 1100
>
> B = 0000 1101
>
> --------------------------
>
> A&B = 0000 1100
>
> A|B = 0011 1101
>
> A^B = 0011 0001

Go 语言支持的运算符如下表所示，假定 A为60，B为13。

| 运算符 | 描述                                                         | 实例                               |
| ------ | ------------------------------------------------------------ | ---------------------------------- |
| &      | 按位与运算符“&”是双目运算符。其功能是参与运算的两数各对应的二进位相与。 | (A&B)结果为12，二进制为 0000 1100  |
| \|     | 按位或运算符“\|”是双目运算符。其功能是参与运算的两数各对应的二进位相或。 | (A\|B)结果为61，二进制为 0011 1101 |
| ^      | 按位异或运算符“^”是双目运算符。其功能是参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为 1 | (A^B)结果为49，二进制为0011 0001   |
| <<     | 左移运算符“<<”是双目运算符。左移n位就是乘以2的n次方。其功能是把“<<”左边的运算数的各二进制全部左移若干位，由“<<”右边的数指定移动的位数决定，高位丢弃，低位补0 | A<<2结果为240，二进制为1111 0000   |
| >>     | 右移运算符“>>”是双目运算符。右移n位就是除以2的n次方。其功能是把“>>”左边的运算符的各二进制全部右移若干位，“>>”右边的数指定移动的位数。 | A>>2结果为15，二进制为 0000 1111   |

运行结果：

```go
第一行 - c 的值为 12
第二行 - c 的值为 61
第三行 - c 的值为 49
第四行 - c 的值为 240
第五行 - c 的值为 15
```



## 赋值运算符

| 运算符 | 描述                                           | 实例                                  |
| :----- | :--------------------------------------------- | :------------------------------------ |
| =      | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C = A + B 将 A + B 表达式结果赋值给 C |
| +=     | 相加后再赋值                                   | C += A 等于 C = C + A                 |
| -=     | 相减后再赋值                                   | C -= A 等于 C = C - A                 |
| *=     | 相乘后再赋值                                   | C *= A 等于 C = C * A                 |
| /=     | 相除后再赋值                                   | C /= A 等于 C = C / A                 |
| %=     | 求余后再赋值                                   | C %= A 等于 C = C % A                 |
| <<=    | 左移后赋值                                     | C <<= 2 等于 C = C << 2               |
| >>=    | 右移后赋值                                     | C >>= 2 等于 C = C >> 2               |
| &=     | 按位与后赋值                                   | C &= 2 等于 C = C & 2                 |
| ^=     | 按位异或后赋值                                 | C ^= 2 等于 C = C ^ 2                 |
| \|=    | 按位或后赋值                                   | C \|= 2 等于 C = C \| 2               |

实例：

```go
package main

import "fmt"

func main() {
   var a int = 21
   var c int

   c =  a
   fmt.Printf("第 1 行 - =  运算符实例，c 值为 = %d\n", c )

   c +=  a
   fmt.Printf("第 2 行 - += 运算符实例，c 值为 = %d\n", c )

   c -=  a
   fmt.Printf("第 3 行 - -= 运算符实例，c 值为 = %d\n", c )

   c *=  a
   fmt.Printf("第 4 行 - *= 运算符实例，c 值为 = %d\n", c )

   c /=  a
   fmt.Printf("第 5 行 - /= 运算符实例，c 值为 = %d\n", c )

   c  = 200;

   c <<=  2
   fmt.Printf("第 6行  - <<= 运算符实例，c 值为 = %d\n", c )

   c >>=  2
   fmt.Printf("第 7 行 - >>= 运算符实例，c 值为 = %d\n", c )

   c &=  2
   fmt.Printf("第 8 行 - &= 运算符实例，c 值为 = %d\n", c )

   c ^=  2
   fmt.Printf("第 9 行 - ^= 运算符实例，c 值为 = %d\n", c )

   c |=  2
   fmt.Printf("第 10 行 - |= 运算符实例，c 值为 = %d\n", c )

}
```

以上的运行结果：

```go
第 1 行 - =  运算符实例，c 值为 = 21
第 2 行 - += 运算符实例，c 值为 = 42
第 3 行 - -= 运算符实例，c 值为 = 21
第 4 行 - *= 运算符实例，c 值为 = 441
第 5 行 - /= 运算符实例，c 值为 = 21
第 6行  - <<= 运算符实例，c 值为 = 800
第 7 行 - >>= 运算符实例，c 值为 = 200
第 8 行 - &= 运算符实例，c 值为 = 0
第 9 行 - ^= 运算符实例，c 值为 = 2
第 10 行 - |= 运算符实例，c 值为 = 2
```



## 其他运算符

| **&** | **返回变量存储地址** | **&a; 将给出变量的实际地址。** |
| ----- | -------------------- | ------------------------------ |
| ***** | **指针变量。**       | ***a; 是一个指针变量**         |

实例：

```go
package mian
import "fmt"

func main(){
    var a int = 4
    var b int32
    var c float32
    var ptr *int
    
    // 运算实例
    fmt.Printf("第一行 - a 变量类型为 = %T\n", a)
    fmt.Printf("第二行 - b 变量类型为 = %T\n", b)
    fmt.Printf("第三行 - c 变量类型为 = %T\n", c)
    
    ptr = &a  // 'ptr' 包含了 'a' 的运算地址
    fmt.Println("a 的值为 %d", a)
    fmt.Println("*ptr 的值为 %d\n", *ptr)    
}
```



## **运算符的优先级**

有些运算符拥有较高的优先级，二元运算符的运算方向均是从左到右。下表列出了所有运算符以及他们的优先级，由上到下代表优先级由高到低。

| 优先级 | 运算符           |
| :----- | :--------------- |
| 5      | * / % << >> & &^ |
| 4      | + - \| ^         |
| 3      | == != < <= > >=  |
| 2      | &&               |
| 1      | \|\|             |

当然也可以使用括号来临时提升某个表达式的整体运算优先级。

实例：

```go
package main
import "fmt"

func main(){
    var a int = 20
    var b int = 10
    var c int = 15
    var d int = 5
    var e int
    
    e = (a + b) * c / d;      // ( 30 * 15 ) / 5
    fmt.Printf("(a + b) * c / d 的值为 : %d\n",  e );

    e = ((a + b) * c) / d;    // (30 * 15 ) / 5
    fmt.Printf("((a + b) * c) / d 的值为  : %d\n" ,  e );

    e = (a + b) * (c / d);   // (30) * (15/5)
    fmt.Printf("(a + b) * (c / d) 的值为  : %d\n",  e );

    e = a + (b * c) / d;     //  20 + (150/5)
    fmt.Printf("a + (b * c) / d 的值为  : %d\n" ,  e ); 
}
```

以上结果的运算实例：

```go
(a + b) * c / d 的值为 : 90
((a + b) * c) / d 的值为  : 90
(a + b) * (c / d) 的值为  : 90
a + (b * c) / d 的值为  : 50
```



# Go 语言条件语句

> Go语言提供了以下几种条件判断语句

## if 语句

```go
if 布尔表达式 {
	/* 布尔表达式为true时执行*/
}
```

实例：

```go
package main

import "fmt"

func main() {
   /* 定义局部变量 */
   var a int = 10
 
   /* 使用 if 语句判断布尔表达式 */
    if (a < 20) {
       /* 如果条件为 true 则执行以下语句 */
       fmt.Printf("a 小于 20\n" )
   }
   fmt.Printf("a 的值为 : %d\n", a)
}
```

执行结果：

```go
a 小于 20
a 的值为 : 10
```



## if…else语句

```go
if 布尔表达式 {
	/*布尔表达式为true时执行*/
}else{
    /*布尔表达式为false时执行*/
}
```

实例：

```go
package main

import "fmt"

func main() {
   /* 局部变量定义 */
   var a int = 100;
 
   /* 判断布尔表达式 */
   if a < 20 {
       /* 如果条件为 true 则执行以下语句 */
       fmt.Printf("a 小于 20\n" );
   } else {
       /* 如果条件为 false 则执行以下语句 */
       fmt.Printf("a 不小于 20\n" );
   }
   fmt.Printf("a 的值为 : %d\n", a);

}
```

结果：

```go
a 不小于 20
a 的值为 : 100
```



### 2篇笔记

> 寻找100以内的素数

```go
package main
import "fmt"

func main(){ // 大括号不能另起一行，否则会报错
    // var  count, c int // 定义变量不使用也会报错
    var count int
    var flag bool
    count = 1
    // while(count<100){// go没有 while语句}
    for count<100{
        count++
        flag = true
        // 注意tmp变量 :=
        for tmp:=2; tmp<count; tmp++ {
            if count%tmp==0 {
                flag = true
            }
        }
        
        // 每一个if else 都需要加入大括号，同时 else 位置不能在新的一行
        if flag == true {
            fmt.Println(count,"素数")
        }else{
            continue
        }
    }
}

```



> if…else if…else 实例

```go
package main
import "fmt"

func main(){
    var age int = 23
    if age == 25 {
        fmt.Println("true")
    }else if age <25 {
        fmt.Println("too small")
    }else{
        fmt.Println("too big")
    }
}
```



















































































