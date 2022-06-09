# fmt.Sprintf 格式化字符串

[Go-getStarting](./getStarting.md)

## 基本格式

```go
fmt.Sprintf(格式化样式，参数列表)
```

- **格式化样式**：字符串形式，格式化符号必须以 % 开头，%s表示字符串格式，%d表示十进制的整数格式。
- **参数列表**：多个参数以逗号分隔，个数必须以格式化样式中的个数一一对应，否则运行时会报错。



```
package main

import (
	"fmt"
	"io"
	"os"
)

func main(){
	// go 中格式化字符串比赋值给新串，使用 fmt.Sprintf
	// %s 表示字符串
	var stockcode = "000987"
	var enddate = "2020-10-12"
	var url = "Code=%s&enddate=%s"
	var target_url = fmt.Sprintf(url, stockcode, enddate)
	fmt.Println(target_url)

	// 另外一个实例，%d 表示整型
	const name, age = "Kim", 12
	s := fmt.Sprintf("%s is %d years old.\n", name, age)
	io.WriteString(os.Stdout, s)
}
```

输出结果：

```go
Code=000987&enddate=2020-10-12
Kim is 12 years old.
```



## Go 字符串格式化符号

| 格式 | 描述                                       |
| :--- | :----------------------------------------- |
| %v   | 按值的本来值输出                           |
| %+v  | 在 %v 的基础上，对结构体字段名和值进行展开 |
| %#v  | 输出 Go 语言语法格式的类型和值             |
| %T   | 输出 Go 语言语法格式的类型                 |
| %%   | 输出 % 的本体                              |
| %b   | 整型以二进制方式显示                       |
| %o   | 整型以八进制方式显示                       |
| %d   | 整型以十进制方式显示                       |
| %x   | 整型以十六进制方式显示                     |
| %X   | 整型以十六进制，字母大写的方式显示         |
| %U   | Unicode字符                                |
| %f   | 浮点数                                     |
| %p   | 指针，十六进制方式显示                     |
| %s   | 以字符串的形式显示                         |



## 实例

```go
package main

import (
	"fmt"
	"os"
)

type point struct {
	x, y int
}
func main() {
	p := point{1, 2}
	fmt.Printf("%v\n", p)

	fmt.Printf("%+v\n", p)

	fmt.Printf("%#v\n", p)

	fmt.Printf("%T\n", p)

	fmt.Printf("%%\n")

	fmt.Printf("%T\n", true)
	fmt.Printf("%t\n", true)

	fmt.Printf("%T\n", 123)
	fmt.Printf("%d\n", 12)

	fmt.Printf("%b\n", 14)
	fmt.Printf("%c\n", 33)
	fmt.Printf("%c\n", 0)

	fmt.Printf("%x\n", 456)
	fmt.Printf("%X\n", 456)

	fmt.Printf("%f\n", 78.9)
	fmt.Printf("%F\n", 78.9)

	fmt.Printf("%e\n", 123400000.0)
	fmt.Printf("%E\n", 123400000.0)

	fmt.Printf("%s\n", "\" strong \"")
	fmt.Printf("%S\n", "\" strong \"")

	fmt.Printf("%q\n", "\" string \"")
	fmt.Printf("%Q\n", "\" string \"")

	fmt.Printf("%x\n", "hex this")
	fmt.Printf("%X\n", "hex this")

	fmt.Printf("%p\n", &p)
	fmt.Printf("%P\n", &p)

	fmt.Printf("|%6d|%6d|\n", 12, 345)
	fmt.Printf("|%6D|%6D|\n", 12, 345)

	fmt.Printf("|%6.2f|%6.2f|\n", 1.2, 3.45)
	fmt.Printf("|%6.2F|%6.2F|\n", 1.2, 3.45)

	fmt.Printf("|%-6.2f|%-6.2f|\n", 1.2, 3.45)
	fmt.Printf("|%-6.2F|%-6.2F|\n", 1.2, 3.45)

	fmt.Printf("|%6s|%6s|\n", "foo", "b")
	fmt.Printf("|%6S|%6S|\n", "foo", "b")

	fmt.Printf("|%-6s|%-6s|\n", "foo", "b")
	fmt.Printf("|%-6S|%-6S|\n", "foo", "b")

	s := fmt.Sprintf("a %s", "string")
	fmt.Println(s)

	fmt.Fprintf(os.Stderr, "an %s\n", "error")
}
```

输出结果：

```go
{1 2}
{x:1 y:2}
main.point{x:1, y:2}
main.point
%
bool
true
int
12
1110
!
 
1c8
1C8
78.900000
78.900000
1.234000e+08
1.234000E+08
" strong "
%!S(string=" strong ")
"\" string \""
%!Q(string=" string ")
6865782074686973
6865782074686973
0xc00000a0b0
&{%!P(int=1) %!P(int=2)}
|    12|   345|
|%!D(int=    12)|%!D(int=   345)|
|  1.20|  3.45|
|  1.20|  3.45|
|1.20  |3.45  |
|1.20  |3.45  |
|   foo|     b|
|%!S(string=   foo)|%!S(string=     b)|
|foo   |b     |
|%!S(string=foo   )|%!S(string=b     )|
a string
an error

Process finished with the exit code 0
```

