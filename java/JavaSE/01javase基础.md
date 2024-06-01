[TOC]

# 一、java基础

## 01 原来的安装【环境变量】

![image-20230219013711411](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190141579.png)



## 02 java11的环境变量

![image-20230219014411973](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190144026.png)



## 03 常见的数据类型

![image-20230219015300692](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190153774.png)



## 04 变量使用时的注意事项

![image-20230219015748154](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190157228.png)

![image-20230219015811701](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190158775.png)



## 05 标识符

### 05.1 标识符的定义规则

![image-20230219015936833](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190159917.png)



### 05.2 常见命名约定

![image-20230219020134135](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302190201204.png)



## 06 类型转换

### 06.1 自动类型转换

把一个表示数据范围小的数值或变量赋值给另一个表示数据范围大的变量



### 06.2 强制类型转换

把一个表示数据范围大的数值或者变量赋值给另一个表示数据范围小的变量



## 07 java中内存分配

![image-20230219160453132](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191606949.png)

注：局部变量是存储在栈内存中，是定义在方法中的变量，使用完毕以后，立即消失。

注：new 出来的内容（实体，对象）或数组，在初始化时会为存储空间添加默认值，每一个new出来的东西都有一个地址值。使用完毕，会在垃圾回收器空闲的时候被回收。

![image-20230219161234716](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191612757.png)



## 08 数据内存图（多个数组指向同一个数组）

![image-20230219162043883](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191620920.png)

注：当多个数组指向同一个数组时，每个数组都有相同的内存值，当一个数组中的值发生改变时，其他数组的值也会发生改变。



## 09 数组初始化

### 09.1 静态初始化

在创建数组时，直接将元素确定

> 完整版格式

```java
数据类型[] 数组名 = new 数组类型[]{元素1，元素2，...}
```

> 简化版格式

```java
数据类型[] 数组名 = {元素1,元素2,...}
```

注：new 指 为**数组**开辟一个内存空间。



### 09.2 动态初始化

数组动态初始化就是只给定数组的长度，由系统给出默认初始化值

> 基本格式

```java
数据类型[] 数组名 = new 数据类型[数组长度];
```



### 09.3 数组操作常见两个小问题

> **索引越界**

访问了数组中不存在的索引的元素，造成索引越界的问题。



> **空指针异常**

将数组赋值为**null**以后，再次使用数组，会引起空指针异常。

null：空值，**引用数据类型的默认值**，表示不指向任何有效对象。



### 09.4 数组常见的操作

> 遍历

```java
int[] arr = {1,2,3};
int a = arr[1];
```

```java
int[] arr = {...};
for (//condition){
	// code
}
```



> 数组长度

```java
int[] arr = {1,2,3};
int len = arr.length;
```



## 10 方法

### 10.1 方法的概念

方法（method）是将具有独立功能的代码块组织为一个整体，使其具有特殊功能的代码集。

- 注意
  - 方法必须先创建才可以使用，该过程称为**方法的定义**。
  - 方法创建后并不是直接可以运行的，需要手动使用后，才执行，该过程称为**方法调用**。



### 10.2 方法的定义和调用

![image-20230219164457521](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191644561.png)



### 10.3 方法调用过程图解

![image-20230219164848452](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191648497.png)





- 总结：**每个方法在调用执行的时候，都会进入栈内存，并且拥有自己独立的内存空间，方法内部代码调用完毕之后，会从栈内存中弹栈消失。**



### 10.4 带参数方法定义和调用

![image-20230219165314043](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191653075.png)

注：方法调用时，参数的数量和类型与方法中的的设置相匹配，否则程序将报错。



### 10.5 带返回值方法的定义和调用

![image-20230219165531005](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191655036.png)



注：

- 方法定义时后面的返回值与方法定义上的数据类型要匹配，否则程序将报错。
- **方法返回值通常使用变量接收，否则返回值将没有意义**。



### 10.6 方法的注意事项

- **方法不能嵌套使用**
  - 实例代码

![image-20230219165916166](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191659201.png)



- **void表示无返回值，可以省略return，也可以单独的书写return，后面不加数据**。
  - 实例代码

![image-20230219170016204](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191700238.png)



### 10.7 方法的通用格式

- 格式

```java
public static 返回值类型 方法名(参数){
	方法体;
	return 数据;
}
```

- 解释

  - public static 修饰符
  - 返回值类型 方法操作完毕之后返回的数据的数据类型

  如果方法操作完毕，没有数据返回，这里就写void，而且方法体中一般不写return

  ```java
  方法名		调用方法时使用的标识
  参数		 由数据类型和变量名组成，多个参数之间用逗号隔开 
  方法体    	 完成功能的代码块 
  return     如果方法操作完毕，有数据返回，用于把数据返回给调用者
  ```

- 定义方法时，要做到两个明确

  - **明确返回值类型**：主要是明确方法操作完毕之后是否有数据返回，如果没有，写void；如果有，写对应的数据类型。
  - **明确参数**：明确参数的类型和数量

- 调用方法时注意

  - void方法直接调用即可
  - 非void类型的方法，推荐用变量接收调用



### 10.8 方法重载

- 方法重载的概念

  方法重载指同一个类中定义的多个方法之间的关系，满足以下条件的多个方法相互构成重载

  - **多个方法在同一个类中**
  - **多个方法具有相同的方法名**
  - **多个方法的参数不相同，类型不同或者数量不同**

- 注意：

  - 重载仅对应方法的定义，与方法的调用无关，调用方式参照标准格式
  - 重载仅针对同一个类中方法名称与参数进行识别，**与返回值无关**。换句话说不能通过返回值来判断两个方法是否构成重载。

- 正确范例：

```java
public class MethodDemo{
	public static void fn(int a){
		// 方法体
	}
	public static int fn(double a){
		// 方法体
	}
}

public class MethodDemo{
    public static float fn(int a){
        // 方法体
    }
    public static int fn(int a, int b){
        // 方法体
    }
}
```

- 错误范例

```java
public class MethodDemo{
    public static void fn(int a){
        //code
    }
    public static int fn(int a){
        //code
    }// 错误原因：重载与返回值无关
}

public class MethodDemo01{
    public static void fn(int a){
        //code
    }
}

public class MethodDemo02{
    public static int fn(double a){
        //code
    }// 错误原因：这是两个类的两个fn方法
}
```



### 10.9 方法的参数传递

- 值传递

  - **形参的改变不影响实参的改变**
  - 这是因为：**每个方法在栈内存中，都会有独立的栈空间，方法运行结束后就会弹出栈消失**

  ![image-20230219172822071](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191728136.png)

- 引用传递

  - **对于引用类型的参数，形参的改变，影响实际参数的值**
  - **引用数据类型的传参，传入的是地址值，内存中会造成两个引用指向同一个内存地址的效果，所以即使方法弹栈，堆内存中的数据也已经是改变后的结果。**

  ![image-20230219173329510](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191733552.png)



## 11 类

### 11.1 类的定义

![image-20230219175732010](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191757053.png)



### 11.2 对象的使用

![image-20230219175846395](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191758430.png)



### 11.3 对象内存图

> 单个对象的内存图

![image-20230219181505991](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191815050.png)



过程：首先，main方法进入栈内存，然后在main方法下声明了一个Student的s对象，同时在堆内存中开辟了一个空间存放`Student()`对象，地址,是001，对象的属性变量也在堆内存中。调用s.name时，先在栈内存中，找到s指向的地址001，然后通过001在堆内存中找到对象`Student()`，然后匹配name的值。当需要调用s.study()方法时，study进入栈内存中，此时调用对象是s(001)，当study方法执行完毕，study方法退出栈，当main执行完毕，main方法出栈。



> 多个对象内存图

![image-20230219182734728](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191827793.png)

- 总结：多个对象在堆内存中，都有不同的内存划分，成员变量存储在各自的内存区域中，成员方法对各对象公用一份



> 多个对象指向相同的内存图

![image-20230219184016624](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191840674.png)

- 总结：

  当多个对象的引用指向同一个内存空间（变量所记录的地址是一样的）

  只要有任何一个对象修改了内存中的数据，随后，无论使用哪一个对象进行数据获取，都是修改后的数据。



### 11.4 成员变量和局部变量

> 成员变量与局部变量的区别

- 类中位置不同：成员变量（类中方法外），局部变量（**方法内部**或者**方法的形参**）
- 内存中位置不同：成员变量（堆内存），局部变量（栈内存）
- 生命周期不同：成员变量（随着对象的存在而存在，随着对象的消失而消失），局部变量（随着方法的调用而存在，随着方法的调用完毕而消失）
- 初始化值不同：成员变量（**有默认的初始化值**），局部变量（**没有默认初始化值，必须先定义，赋值才能使用**）



### 11.5 封装

> **private关键字**

private是一个修饰符，可以用来修饰成员（成员变量，成员方法）

- 被private修饰的成员，只能在本类进行访问，针对private修饰的成员变量，如果需要被其他的类使用，提供相应的操作
  - 提供get方法，用public修饰
  - 提供set方法，用public修饰



> **this关键字**

- this修饰的变量用于指代成员变量，其主要作用是（区分局部变量和成员变量的重命名问题）
  - 方法的形参如果与成员变量同名，不带this修饰的变量指的是形参，而不是成员变量
  - 方法的形参没有与成员变量同名，不带this修饰的变量指的是成员变量

**什么时候使用this呢？**

当害怕局部变量与成员变量同名的时候，可以用this，以解决局部变量隐藏成员变量的问题



**this：代表所在类的对象引用**

- 记住：方法被哪个对象调用，this就代表哪个对象



> **this的内存原理**

![image-20230219195159061](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302191951117.png)

记住：方法被哪个对象调用，那么this就代表哪个对象



> **封装思想**

1. 封装概述：是面向对象三大特征之一（封装，继承，多态）是面向对象编程语言对客观世界的模拟，客观世界里成员变量都是隐藏在对象内部的，外界是无法直接操作的
2. 封装原则：将类的某些信息隐藏在类内部，不允许外部程序直接访问，而是通过该类提供的方法来实现对隐藏信息的操作和访问，比如成员变量private，提供对应的get/set方法
3. 封装的好处：通过方法来控制成员变量的操作，提高了代码的安全性，把代码用方法进行封装，提高了代码的复用性



### 11.6 构造方法

> **概述**

- 构造方法是一种特殊的方法

- 作用：创建对象 Student stu = new Student（）；

- 格式：

  ```java
  public class 类名{
  	修饰符 类名(参数){
  	
  	}
  }
  ```

- 功能：主要是完成对象数据的初始化



> **构造方法的注意事项**

- 构造方法的创建

  **如果没有定义构造方法，系统将给出一个默认的无参数构造方法，如果定义了构造方法，系统将不再提供默认的构造方法**

- 构造方法的重载

  如果自定义了**带参**构造方法，系统将不再提供**无参**构造方法，**还要使用无参构造方法的话，就必须再写一个无参构造方法**

- 推荐的使用方式

  无论是否使用，都手工书写无参构造方法

- 重要功能！

  可以使用带参构造，为成员变量进行初始化



## 12 常用API

### 12.1 API概述

![image-20230219201230858](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192012897.png)



### 12.2 String 类

> **String类的概述**

![image-20230219201813332](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192018369.png)



> **String类的特点**

- 字符串不可变，他们的值在创建后不可更改
- 虽然String的值是不可变的，但是它们可以被共享
- 字符串效果上相当于字符数组（char[]），但是底层原理是字节数组（byte[]）



> **String类的构造方法**

- 常用的构造方法

  ![image-20230219202545972](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192025009.png)

- 示例代码

![image-20230219202707049](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192027099.png)

输出：

```java
s1:
s2:abc
s3:abc
s4:abc
```



> **创建字符串对象两种方式的区别**

- 通过构造方法创建

  通过new创建的字符串对象，每一次new都会申请一个内存空间，虽然内容相同，但是地址值不同

- 直接赋值方式创建

  以 双引号 方式给出的字符串，只要字符序列相同（顺序和大小写），无论在程序代码中出现几次，JVM都只会建立一个String对象，并在字符串池中维护



### 12.3 字符串的常见操作

> **==**的作用

- 比较基本数据类型：比较的是具体的值
- 比较引用数据类型：比较的是**对象地址值**



> equals方法的作用：比较两个字符串**内容是否相同**，区分大小写

```java
public boolean equals(String s)  比较两个字符串内容是否相同，区分大小写
```

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World!");
        char[] chs = {'a', 'b', 'c'};
        String s1 = new String(chs);
        String s2 = new String(chs);

        String s3 = "abc";
        String s4 = "abc";

        System.out.println(s1==s2);
        System.out.println(s1==s3);
        System.out.println(s3==s4);
        System.out.println("-----------");

        System.out.println(s1.equals(s2));
        System.out.println(s1.equals(s3));
        System.out.println(s3.equals(s4));
    }


}
```

输出结果为：

```
Hello World!
false
false
true
-----------
true
true
true
```

**总结**：**创建字符串对象两种方式的区别**

- 通过构造方法创建

  通过new创建的字符串对象，每一次new都会申请一个内存空间，虽然内容相同，但是地址值不同

- 直接赋值方式创建

  以 **双引号** 方式给出的字符串，只要字符序列相同（顺序和大小写），无论在程序代码中出现几次，JVM都**只会**建立一个String对象，并在字符串池中维护



应用案例：假如ch是一个字符，判断它是一个大写字母还是小写字母，还是数字

直接判断字符是否在对应的范围即可

```java
大写字母：ch>='A' && ch<='Z'
小写字母：ch>='a' && ch<='z'
数字：ch>='0' && ch<='9'
```



> 字符串String中获取某个索引的值：s.charAt(i)



> **帮助文档查看String常用方法**

![image-20230219204917002](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192049042.png)





### 12.4 StringBuilder类

#### 12.4.1 StringBuilder类的概述

StringBuilder 是一个可变的字符串类，我们可以把它看成一个容器，这里的可变指的是StringBuilder对象中的内容是可变的



#### 12.4.2 StringBuilder类与String类的区别

- String类：内容是不可变的
- StringBuilder类：内容是可变的



#### 12.4.3 StringBuilder类的构造方法

![image-20230219205305123](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192053174.png)



#### 12.4.4 StringBuilder类的添加和反转

![image-20230219205435084](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192054146.png)

实例代码：

```java
StringBuilder sb = new StringBuilder();
StringBuilder sb2 = sb.append("hello");
System.out.println(sb);
System.out.println(sb2);
System.out.println(sb==sb2);

sb2.append(" world").append(" java").append(100);
System.out.println(sb2);
sb2.reverse();
System.out.println(sb2);
```

输出：

```java
hello
hello
true
hello world java100
001avaj dlrow olleh
```



#### 12.4.5 StringBuilder和String相互转换

- StringBuilder转换为String：

  public String toString(): 通过toString（）就可以实现把StringBuilder转换为String

  还可以使用 `String(StringBuilder builder)`构造方法

- String转换为StringBuilder

  public StringBuilder(String s)：通过构造方法就可以实现把String转换为StringBuilder

#### 12.4.6 查看StringBuilder的帮助文档

![image-20230219210612851](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192106895.png)





## 13 ArrayList

### 13.1 ArrayList类的概述

- 什么是集合

  提供一种存储空间可变的存储模型，存储的数据容量可以发生改变

- ArrayList集合的特点

  底层是**数组**实现的，长度可以发生变化

- 泛型的使用

  **用于约束集合中存储元素的数据类型**

### 13.2 ArrayList类的常用方法

![image-20230219211243174](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302192112226.png)

**示例代码：**

```java
public class ArrayListDemo01 {
    public static void main(String[] args) {
        ArrayList<String> arr = new ArrayList<String>();
        arr.add("hello");
        arr.add(" world");
        arr.add(" java");

//        删除指定元素，返回删除是否成功
        System.out.println(arr.remove(" world"));
        System.out.println(arr.remove(" javaee"));

//        指定索引删除, 返回被删除的元素
        System.out.println(arr.remove(1));

//        IndexOutOfBoundsException
//        System.out.println(arr.remove(1));

//        public E set(int index, E element):修改指定索引处的元素，返回被修改前的元素
        System.out.println(arr.set(0, "javaee"));

//        IndexOutOfBoundsException
//        System.out.println(arr.set(3, "javaee"));

//        public E get(int index)：返回指定索引处的元素
        System.out.println(arr.get(0));

//        public void add(int index, E element)：在此集合的指定位置插入指定元素

//        public int size（）：返回集合中的元素个数

    }
}
```

输出：

```java
true
false
 java
hello
javaee
```



## 14 继承&修饰符

### 14.1 继承的概念

- 继承是面向对象三大特征之一，可以使的子类具有父类的属性和方法，还可以在子类中重新定义，以及追加属性和方法

- 继承的格式

  - 继承通过extends实现
  - 格式：class 子类 extends 父类{}
    - 举例：class Dog extends Animal{}

- 继承带来的好处

  - 继承可以让类与类之间产生关系，子父类关系，产生子父类后，子类可以使用父类中非私有的成员。

- 示例代码

  ![image-20230220112336513](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201123576.png)



### 14.2 继承的好处

- 继承的好处
  - 提高了代码的复用性（多个类相同的成员可以放到同一个类中）
  - 提高了代码的维护性（如果方法的代码需要修改，修改一处即可）
- 继承的弊端
  - 继承增强了类与类之间的耦合性，当父类发生变化时，子类的实现不得不跟着变化，削弱了子类的独立性
- 继承的应用场景
  - 使用继承，需要考虑类与类之间是否存在is .. a 的关系，不能盲目使用
    - is a 的关系是指：谁是谁的一种，例如：老师和学生是人的一种，那人就父类，学生和老师就是子类



### 14.3 继承中访问成员的特点

> **在子类方法中访问一个变量，采用的是就近原则**

- 子类局部范围找

- 子类成员范围找

- 父类成员范围找

- 如果都没有就报错（不考虑父类的父类）

- 示例代码

  ![image-20230220113046397](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201130444.png)



> **super**

- this&super关键字：
  - this：代表本类对象的引用
  - super：代表父类存储空间的标识（可以理解为父类对象的引用）
- this和super的使用区别：
  - 成员变量：
    - this.成员变量   -   访问本类成员变量
    - super.成员变量   -   访问父类成员变量
  - 成员方法
    - this.成员方法     -      访问本类成员方法
    - super.成员方法     -     访问父类成员方法
- 构造方法
  - this(...)   -   访问本类构造方法
  - super(...)    -   访问父类构造方法



> **继承中构造方法的访问特点**

**注意：子类中所有的构造方法默认都会访问父类中无参的构造方法**

子类会继承父类中的数据，可能还会使用父类的数据。所以，子类初始化之前，一定要先完成父类数据的初始化，原因在于，**每一个子类构造方法的第一条默认语句都是：super()**

**问题：如果父类中没有无参的构造方法，只有带参的构造方法，怎么办？**

```
1. 通过使用super关键字去显示的调用父类的带参构造方法
2. 在父类中自己提供一个无参构造方法
```

**推荐方法：**

自己给出一个无参的构造方法



> **继承中成员方法的访问特点**

**通过子类对象访问一个方法：**

- 子类成员范围找
- 父类成员范围找
- 如果都没有就报错（不考虑父亲的父亲）



> **super内存图**

![image-20230220120056789](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201200844.png)

