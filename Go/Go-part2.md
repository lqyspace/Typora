[TOC]

# Go**语言常量**

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





# Go **语言运算符**

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



# **Go 语言条件语句**

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



## if 嵌套语句

实例：

```go
package main

import "fmt"

func main() {
   /* 定义局部变量 */
   var a int = 100
   var b int = 200
 
   /* 判断条件 */
   if a == 100 {
       /* if 条件语句为 true 执行 */
       if b == 200 {
          /* if 条件语句为 true 执行 */
          fmt.Printf("a 的值为 100 ， b 的值为 200\n" );
       }
   }
   fmt.Printf("a 值为 : %d\n", a );
   fmt.Printf("b 值为 : %d\n", b );
}
```



## switch语句

1、switch语句用于基于不同条件执行不同的动作，每个case分值都是唯一的，从上到下逐一测试直到匹配为止。

2、switch语句执行的过程是从上到下，直到找到匹配项，匹配项后面也不需要再加 break；

3、switch默认情况下case最后自带break语句，匹配成功后就不会执行其他的 case，如果我们需要执行后面的 case，可以使用 `fallthrough` 。

4、default 语句是在没有case匹配上的时候执行。

**语法格式：**

```go
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
```

变量 `var1` 可以是任何类型，而 `var1` 和 `var2` 则可以是同类型的任意值。类型不被局限于常量或整数，但必须是**相同的类型**；或者最终结果为相同类型的表达式。

您可以同时测试多个可能符合条件的值，使用逗号分割他们，例如：case val1，val2，val3。

流程图：

![image-20220609233831040](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202206092338924.png)

实例：

```go
package main

import "fmt"

func main(){
    /* 定义局部变量*/
    var grade string = "B"
    var marks int = 90
    
    switch marks {
        case 90: grade = "A"
        case 80: grade = "B"
        case 50,60,70: grade = "C"
        default: grade = "D"
    }
    
    switch {
        case grade == "A":
        	fmt.Printf("优秀！\n")
        case grade == "B", grade == "C":
        	fmt.Printf("良好！\n")
    	case grade == "D": fmt.Printf("及格!\n")
    	case grade == "F": fmt.Printf("不及格！\n")
        default:
        	fmt.Printf("差!\n")
    }
    
    fmt.Printf("你的等级是 %s\n", grade)
}
```

结果：

```go
优秀!
你的等级是 A
```



### Type Switch

switch 语句还可以被用于 type-switch 来判断某个 interface 变量中实际存储的变量类型。

Type Switch语法格式：

```go
switch x.(type){
    case type: statement(s)
    case type: statement(s)
    ....
    default:
    	statement(s)
}
```

实例：

```go
package main

import "fmt"

func main(){
    var x interface{}
    
    switch i := x.(type) {
        case nil:
        	fmt.Printf("x 的类型：%T", i)
        case int:  
        	fmt.Printf("x 是 int 型")                     
        case float64:
            fmt.Printf("x 是 float64 型")          
         case func(int) float64:
            fmt.Printf("x 是 func(int) 型")               
         case bool, string:
            fmt.Printf("x 是 bool 或 string 型" )      
         default:
            fmt.Printf("未知型") 
    }
}
```

运行结果：

```go
x 的类型：<nil>
```



### fallthrough

使用 `fallthrough` 会强制执行后面的case，而 fallthrough 不会去判断下一条 case 的表达式的结果是否为 true。

实例：

```go
package main

import "fmt"

func main(){
    switch {
        case false:
            fmt.Println("1、case 条件语句为 false")
            fallthrough
        case true:
            fmt.Println("2、case 条件语句为 true")
            fallthrough
        case false:
            fmt.Println("3、case 条件语句为 false")
            fallthrough
        case true:
            fmt.Println("4、case 条件语句为 true")
    	case false:
            fmt.Println("5、case 条件语句为 false")
            fallthrough
    	default:
            fmt.Println("6、默认 case")
    }
}
```

结果：

```go
2、case 条件语句为 true
3、case 条件语句为 false
4、case 条件语句为 true
```

从以上代码输出的结果可以看出：switch 从第一个判断表达式为 true 的 case 开始执行，如果 case 带有 fallthrough，程序会继续执行下一条 case，且它不会去判断下一个 case 的表达式是否为 true。

### 总结

1. 支持多条件匹配
2. 不同的 case 之间不使用 break 分隔，默认只会执行一个 case。
3.  如果想要执行多个 case，需要使用 fallthrough 关键字，也可用 break 终止。

> ```go
> switch{
>     case 1:
>     ...
>     if(...){
>         break
>     }
> 
>     fallthrough // 此时switch(1)会执行case1和case2，但是如果满足if条件，则只执行case1
> 
>     case 2:
>     ...
>     case 3:
> }
> ```

4. switch 的 default 与位置无关。



## select 语句

**select 是 Go 中的一个控制结构，类似于用于通信的 switch 语句。每个 case 必须是一个通信操作，要么是发送要么是接收。**



**select 随机执行一个可运行的 case，如果没有 case 可运行，它将阻塞，直到有 case 可运行。一个default的子句应该总是可以运行的。**



### 语法

语法格式如下：

```go
select {
    case communication clause: statement(s)
    case communication clause: statement(s)
    ...
    default: /* 可选 */
    statement(s)
}
```

关于 select 的描述：

- 每个 case 都必须是一个通信

- 所有的 channel 表达式都会被求值

- 所有被发送的表达式都会被求值

- 如果任意某个通信可以进行，它就执行，其他的被忽略。

- 如果有多个 case 都可以运行，select 会随机公平地选出一个执行，其他的不会执行。

  - 否则：

    - 1、如果有default子句，则执行该子句。
    - 2、如果没有default子句，select将被阻塞，直到某个通信可以运行，Go不会重新对channel或值进行求值。

    

实例：

```go
package main

import "fmt"

func main(){
    var c1, c2, c3 chan int
    var i1, i2 int
    select {
        case i1 = <-c1:
        	fmt.Printf("received ", i1, " from c1\n")
        case c2 <- i2:
        	fmt.Println("sent ", i2, " to c2\n")
        case i3, ok := (<-c3): // same as: i3, ok := <-c3
        	if ok {
            	fmt.Printf("received ", i3, " from c3\n")
         	} else {
            	fmt.Printf("c3 is closed\n")
         	}
      	default:
         	fmt.Printf("no communication\n")
    }
}
```

以上代码的执行结果：

> ```go
> no communication
> ```



**笔记**：

> select 是随机执行的不是循环检测，是为了避免饥饿问题

```go
package mian

import (
    "fmt"
    "time"
)

func Chann(ch chan int, stopCh chan bool){
    for j:=0; j<10; j++ {
        ch <- j
        time.Sleep(time.Second)
    }
    stopCh <- true;
}

func main(){
    ch := make(chan int)
    c := 0
    stopCh := make(chan int)
    
    go Chann(ch, stopCh)
    
    for {
        select {
            case c = <- ch:
            fmt.Println("Receive C", c)
            case s := <- ch:
            fmt.Prontln("Receive S", s)
            case _ = <-stopCh:
            goto end
        }
    }
    end:
}
```





# Go 语言循环语句

## 循环类型

| 循环类型                                                   | 描述                                 |
| :--------------------------------------------------------- | :----------------------------------- |
| [for 循环](https://www.runoob.com/go/go-for-loop.html)     | 重复执行语句块                       |
| [循环嵌套](https://www.runoob.com/go/go-nested-loops.html) | 在 for 循环中嵌套一个或多个 for 循环 |

### for循环

> 语法

Go 语言的 For 循环有 3 种形式，只有其中的一种使用分号。

和 C 语言的 for 一样：

```go
for init; condition; post { }
```

和 C 的 while 一样：

```go
for condition { }
```

和 C 的 for(;;) 一样：

```go
for { }
```

- init： 一般为赋值表达式，给控制变量赋初值；
- condition： 关系表达式或逻辑表达式，循环控制条件；
- post： 一般为赋值表达式，给控制变量增量或减量。



for语句执行过程如下：

- 1、先对表达式 1 赋初值；
- 2、判别赋值表达式 init 是否满足给定条件，若其值为真，满足循环条件，则执行循环体内语句，然后执行 post，进入第二次循环，再判别 condition；否则判断 condition 的值为假，不满足条件，就终止for循环，执行循环体外语句。

for 循环的 range 格式可以对 slice、map、数组、字符串等进行迭代循环。

range 之后得到的第一个值为键key或者索引index，第二个值为值value。格式如下：

```go
for key, value := range oldMap {
    newMap[key] = value
}
```

以上代码中的 key 和 value 是可以省略。

如果只想读取 key，格式如下：

```go
for key := range oldMap
```

或者这样：

```go
for key, _ := range oldMap
```

如果只想读取 value，格式如下：

```go
for _, value := range oldMap
```

只有这一种方式，如果使用 `for value := range oldMap` 的话，输出的是Go语言语法的类型值(例如：`%!f(int=2)`)。



实例：

```go
package main

import "fmt"

func main() {
        sum := 0
        for i := 0; i <= 10; i++ {
                sum += i
        }
        fmt.Println(sum)
}
```

结果：

```go
55
```

init 和 post 参数是可选的，我们可以直接省略它，类似 While 语句。

以下实例在 sum 小于 10 的时候计算 sum 自相加后的值：

实例：

```go
package main

import "fmt"

func main() {
        sum := 1
        for ; sum <= 10; {
                sum += sum
        }
        fmt.Println(sum)

        // 这样写也可以，更像 While 语句形式
        for sum <= 10{
                sum += sum
        }
        fmt.Println(sum)
}
```

输出结果：

```go
16
16
```

无限循环：

```go
package main

import "fmt"

func main() {
        sum := 0
        for {
            sum++ // 无限循环下去
        }
        fmt.Println(sum) // 无法输出
}
```

要停止无限循环，可以在命令窗口按下**ctrl-c** 。

**For-each range 循环**

这种格式的循环可以对字符串、数组、切片等进行迭代输出元素。

range 之后得到的第一个值为键key或者索引index，第二个值为值value。

```go
package main
import "fmt"

func main() {
        strings := []string{"google", "runoob"}
        for i, s := range strings {
                fmt.Println(i, s)
        }


        numbers := [6]int{1, 2, 3, 5}
        for i,x:= range numbers {
                fmt.Printf("第 %d 位 x 的值 = %d\n", i,x)
        }  
}
```

运行结果：

```go
0 google
1 runoob
第 0 位 x 的值 = 1
第 1 位 x 的值 = 2
第 2 位 x 的值 = 3
第 3 位 x 的值 = 5
第 4 位 x 的值 = 0
第 5 位 x 的值 = 0
```

for 循环的 range 格式可以省略 key 和 value，如下实例：

```go
package main

import "fmt"

func main(){
    map1 :=  make(map[int]float32)
    map1[1] = 1.0
    map1[2] = 2.0
    map1[3] = 3.0
    map1[4] = 4.0
    
    // 读取 key 和 value
    // 变量名称不固定，可以替换为其他的变量名
    // 只是第一个位置为键名，第二个位置为值名
    for key, value := range map1 {
        fmt.Printf("key is: %d - value is: %f\n", key, value)
    }
    
    // 读取 key 
    for key := range map1{
        fmt.Printf("key is: %d\n", key)
    }
    
    // 读取 value
    // 只有这一个方式，若是采用 value := range map1
    // 则会输出：%!f(int=2)
    for _, value := range map1 {
        fmt.Printf("value is: %f\n", value)
    }
    
}
```

以上运算输出结果：

```go
key is: 4 - value is: 4.000000
key is: 1 - value is: 1.000000
key is: 2 - value is: 2.000000
key is: 3 - value is: 3.000000
key is: 1
key is: 2
key is: 3
key is: 4
value is: 1.000000
value is: 2.000000
value is: 3.000000
value is: 4.000000
```



### Go语言循环嵌套

> 语法格式

```go
for [condition | (init; condition; increment) | Range]{
    for [condition | (init; condition; increment) | Range]{
        statement(s)
    }
    statement(s)
}
```

实例：

```go
package main

import "fmt"

func main(){
    /* 定义局部变量 */
    var i, j int
    
    for i=2; i<100; i++ {
        for j=2; j<=(i/j)l j++ {
            if(i%j==0) {
                break; // 如果发现因子，则不是素数。
            }
        }
        if(j > (i/j)) {
            fmt.Printf("%d 是素数\n", i)
        }
    }
}
```

结果：

```go
2  是素数
3  是素数
5  是素数
7  是素数
11  是素数
13  是素数
17  是素数
19  是素数
23  是素数
29  是素数
31  是素数
37  是素数
41  是素数
43  是素数
47  是素数
53  是素数
59  是素数
61  是素数
67  是素数
71  是素数
73  是素数
79  是素数
83  是素数
89  是素数
97  是素数
```

> 九九乘法表

```go
package main 

import "fmt"

func main() {
    for m := 1; m < 10; m++ {
    /*    fmt.Printf("第%d次：\n",m) */
        for n := 1; n <= m; n++ {
            fmt.Printf("%dx%d=%d ",n,m,m*n)
        }
        fmt.Println("")
    }
}
```





## 循环控制语句

循环控制语句可以控制循环体内语句的执行过程。

GO 语言支持以下几种循环控制语句：

| 控制语句                                                     | 描述                                                |
| :----------------------------------------------------------- | :-------------------------------------------------- |
| [break 语句](https://www.runoob.com/go/go-break-statement.html) | 经常用于中断**当前 for 循环**或**跳出 switch 语句** |
| [continue 语句](https://www.runoob.com/go/go-continue-statement.html) | 跳过当前循环的剩余语句，然后继续进行下一轮循环。    |
| [goto 语句](https://www.runoob.com/go/go-goto-statement.html) | 将控制转移到被标记的语句。                          |



