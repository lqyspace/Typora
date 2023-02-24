[TOC]

# 一、java基础

## 14 继承&修饰符

### 14.4 继承中方法重写

> **方法重写概念**

子类出现和父类一模一样的方法声明（方法名，参数列表必须一样）



> **方法重写的应用场景**

当子类需要父类的功能时，而功能主体子类要有自己特定的内容时，可以重写父类中的方法，这样，既沿袭了父类的功能，也定义了子类特有的内容。



> **Override注解**

用来检测当前的方法，是否是重写的方法，起到【校验】作用



### 14.5 方法重写的注意事项

- 私有的方法不能够被重写（父类私有成员子类是不能够继承的）

- 子类方法访问权限不能更低（public>默认>私有）

- 示例代码

  ```java
  public class MethodExtends {
      public class Fu{
          private void show(){
              System.out.println("Fu中的show（）方法被调用");
          }
  
          void method(){
              System.out.println("Fu中method（）方法被调用");
          }
  
      }
  
      public class Zi extends Fu{
          // 编译出错，子类不能重写父类的私有方法
          @Override
          private void show(){
              System.out.println("Zi中的show被调用");
          }
  
          // 编译出错，子类重新父类方法的时候，访问权限要大于等于父类
  //        @Override
  //        private void method(){
  //            System.out.println("Zi中的method被调用");
  //        }
  
          // 编译通过，子类重写父类方法，访问权限需要大于等于父类
          @Override
          public void method(){
              System.out.println("Zi中method方法被调用");
          }
  
      }
  
      public static void main(String[] args) {
          Zi z = new Zi();
      }
  }
  ```

  

### 14.6 java中继承的注意事项

- 继承只能单继承，不支持多继承
  - 错误范例：class A extends B，C{}
- java中类支持多层继承

```java
public class Granddad{
        public void drink(){
            System.out.println("yeye");
        }
    }

    public class Father extends Granddad{
        public void smoke(){
            System.out.println("baba");
        }
    }

    public class Son extends Father{
		// 此时，Son类中就同时拥有drink以及smoke方法
        @Override
        public void drink() {
//            super.drink();
            System.out.println("ziji");
        }

        @Override
        public void smoke() {
            System.out.println("zz");
        }
    }
```



### 14.7 修饰符

#### 14.7.1 package

![image-20230220133948783](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201339820.png)

#### 14.7.2 import

![image-20230220134044593](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201340625.png)

![image-20230220134058806](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201340848.png)



#### 14.7.3 权限修饰符

![image-20230220134159214](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201341252.png)

**说明：**

- **private**：被private修饰的方法，只能在**同一个类中**调用方法时才能访问，其他类均访问不到，就算是该类的子类也不能访问到父类中private修饰的方法
- **默认**：默认情况下，该方法可以被同一个类中的其他方法访问到，也可以被**同一个包中**的**该类的子类**或者**同一个包中**的**其他无关类**访问到
- **protect**：被protect修饰的方法，可以在**同一个类**中，**同一个包中的子类和无关类**，以及**不同包中的子类**访问到
- **public**：被public修饰的方法，无论是不是同一个类，其他类，还是不同的包中的子类其他类均可访问



#### 14.7.4 final修饰局部变量

- final修饰基本数据类型变量
  - final修饰指的是基本数据类型的数据值不能发生改变
- final修饰引用类型数据变量
  - final修饰指的是不用类型的地址值不能发生变化，但是地址里面的内容是可以发生改变的

![image-20230220135805162](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201358197.png)

```
final int age = 20;
// age = 100; // 报错，final修饰，age不能更改

final Student stu = new Student();// Student 类中age=20
s.age = 100;//正确
```



#### 14.7.5 static关键字

- static的概念

  - static关键字是静态的意思，可以修饰【成员方法】，【成员变量】

- static修饰特点

  1. 可以被类的所有对象共享，这也是我们判断是否使用静态关键字的条件
  2. 可以通过类名调用，也可以通过对象名调用，【推荐使用类名调用】

- 示例代码

  ```java
  class Student {
  	public String name;
      public int age;
      public static String university;// 学校，可以被共享，所以设计为静态
      
      public void show(){
          System.out.println(name+","+age+","+university);
      }
  }
  
  
  public class StaticDemo{
      public static void main(String[] args){
          // 为对象的共享数据赋值
          Student.university = "传智大学";
          
          Student s1 = new Student();
          s1.name = "林青霞";
          s1.age = 30;
          s1.show();
          
          Student s2 = new Student();
          s2.name = "风清扬";
          s2.age = 33;
          s2.show();
      }
  }
  ```





#### 14.7.6 static 访问特点

- 非静态的成员方法
  - 能访问静态的成员变量
  - 能访问非静态的成员变量
  - 能访问静态的成员访问
  - 能访问非静态的成员方法
- 静态的成员方法
  - 能访问静态的成员变量
  - 能访问静态的成员方法
- 总结一句话：
  - 静态的成员方法只能访问静态成员



## 15 多态&抽象类&接口

### 15.1 多态

#### 15.1.1 多态的概念

- 什么是多态

  同一个对象，在不同时刻表现出不同形态

- 多态的前提

  **要有继承和实现关系**

  **要有方法的重写**

  **要有父类引用指向子类对象**



#### 15.1.2 多态中成员访问特点

- 成员访问特点

  - 成员变量

    编译看父类，运行看父类

  - 成员方法

    编译看父类，运行看子类

**解释：**在编译的时候主要看父类，对于成员变量而言，运行的时候也主要看父类，而对于成员方法而言，运行的时候看子类，因为子类的成员方法有重新，而成员变量没有。

**代码示例：**

Animal类：

```
public class Animal {
    public int age = 40;
    public void eat(){
        System.out.println("动物吃东西");
    }
}
```

Cat类：

```
public class Cat extends Animal{
    public int age = 20;

    public int weight = 10;

    @Override
    public void eat(){
        System.out.println("猫吃鱼");
    }

    public void playGame(){
        System.out.println("猫捉迷藏");
    }

}
```

`AnimalDemo`类：

```java
public class AnimalDemo {
    public static void main(String[] args) {
        // 有父类引用指向子类
        Animal a = new Cat();

        System.out.println(a.age);
//        System.out.println(a.weight); // 不能直接调用

        a.eat();
//        a.playGame();  // 不能直接调用
    }
}
```





#### 15.1.3 多态的好处与弊端

- 好处
  - 提高了程序的可扩展性。定义方法的时候，使用父类型作为参数，在使用的时候，是有具体的子类型参与操作
- 弊端
  - 不能使用子类的特有成员

代码示例：

```java
public class Animal {
    public void eat(){
        System.out.println("动物吃东西");
    }
}
```

```java
public class Cat extends Animal{

    @Override
    public void eat(){
        System.out.println("猫吃鱼");
    }

    public void playGame(){
        System.out.println("猫捉迷藏");
    }

}
```

```java
public class Dog extends Animal{
    int a = 10;
    @Override
    public void eat(){
        System.out.println("狗吃骨头");
    }

    public void lookDoor(){
        System.out.println("狗看门");
    }
}
```

```java
public class AnimalOperator {
    /*public void useAnimal(Cat cat) {
        cat.eat();
    }

    public void useAnimal(Dog d) {
        d.eat();
    }*/

    public void useAnimal(Animal a){
        // Animal a = new Cat();
        // Animal a = new Dog();
        // 编译看左边，运行看右边
        a.eat();


//        a.lookDoor(); // 报错，不能调用子类的特有方法，只能调用子类从父类继承过来的方法
    }
}
```

```java
public class AnimalDemo {
    public static void main(String[] args) {
        // 创建动物操作类的对象，调用方法
        AnimalOperator animalOperator = new AnimalOperator();
        Cat c = new Cat();
        animalOperator.useAnimal(c);

        Dog d = new Dog();
        animalOperator.useAnimal(d);
    }
}
```



#### 15.1.4 多态的转型

针对多态存在的弊端（不能使用子类的特有成员），特提出转型来解决这个问题。

- 向上转型

  父类引用 指向 子类对象 就是向上转型

- 向下转型

  格式：子类对象 对象名 = （子类对象）父类引用；

- 代码演示：

  - 动物类

  ```java
  public class Animal {
      public void eat(){
          System.out.println("动物吃东西");
      }
  }
  ```

  - 猫类

  ```java
  public class Cat extends Animal{
  
      @Override
      public void eat(){
          System.out.println("猫吃鱼");
      }
  
      public void playGame(){
          System.out.println("猫捉迷藏");
      }
  
  }
  ```

  - 测试类

  ```java
  public class AnimalDemo {
      public static void main(String[] args) {
  //        // 创建动物操作类的对象，调用方法
  //        AnimalOperator animalOperator = new AnimalOperator();
  //        Cat c = new Cat();
  //        animalOperator.useAnimal(c);
  //
  //        Dog d = new Dog();
  //        animalOperator.useAnimal(d);
  
  
          // 多态
          // 向上转型
          Animal a = new Cat();
          a.eat();
  //        a.playGame(); // 不能调用子类特有成员
          
          // 创建Cat类对象
  //        Cat c = new Cat();
  //        c.eat();
  //        c.playGame();
  
          // 向下转型
          Cat c = (Cat) a;
          c.eat();
          c.playGame();// 可以调用
      }
  }
  ```

![image-20230220164314212](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201643268.png)



### 15.2 抽象类

#### 15.2.1 抽象类的概述

当我们在做子类共性功能抽取时，**有些方法在父类中并没有具体的体现**，这个时候就需要抽象类了！

**在java中，一个没有方法体的方法应该定义为抽象方法，而类中如果有抽象方法，该类必须定义为抽象类。**



#### 15.2.2 抽象类的特点

- 抽象类和抽象方法必须使用abstract关键字修饰

```java
// 抽象类的定义
public abstract class 类名{}

// 抽象方法的定义
public abstract void eat();
```

注意：**抽象类中不一定有抽象方法，但是有抽象方法的类一定是抽象类**

- **抽象类不能实例化**

  抽象类如何实例化呢？参照多态的方式，**通过子类对象实例化**，这叫**抽象类多态**

- 抽象类的子类

  **要么重写抽象类中的所有抽象方法**

  **要么是抽象类**



#### 15.2.3 抽象类的成员特点

- 成员特点
  - **既可以是变量**
  - **也可以是常量**
- 构造方法：有构造方法但是不能实例化，用于子类访问父类数据的初始化
  - **有参**
  - **无参**
- 成员方法
  - **抽象方法**
  - **普通方法**

代码演示：

```java
public abstract class Animal {
    private int age = 20;
    private final String city = "北京";

    public Animal(int age) {
        this.age = age;
    }

    public Animal() {
    }

    public void show(){
        age = 40;
        System.out.println(age);
        System.out.println(city);
    }

    public abstract void eat();
}
```

```java
public class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("猫爱吃鱼");
    }
}
```

```java
public class AnimalDemo {
    public static void main(String[] args) {
        Animal a = new Cat();
        a.eat();
        a.show(); // 虽然cat中没有show方法，但是从父类继承过来了
    }
}
```



**案例演示：**

![image-20230220173850418](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201738464.png)

```java
public abstract class Animal {
    private String name;
    private int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Animal() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public abstract void eat();
}
```

```java
public class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("猫爱吃鱼");
    }

    public Cat(String name, int age) {
        super(name, age);
    }

    public Cat() {
    }
}
```

```java
public class Dog extends Animal{
    @Override
    public void eat() {
        System.out.println("狗爱吃骨头");
    }

    public Dog(String name, int age) {
        super(name, age);
    }

    public Dog() {
    }
}
```

```java
public class AnimalDemo {
    public static void main(String[] args) {
        Animal a = new Cat("加菲", 3);
        System.out.println(a.getName());
        a.eat();

        a = new Cat();
        a.setAge(4);
        a.setName("加菲");
        System.out.println(a.getName());
        a.eat();
    }
}
```



### 15.3 接口

#### 15.3.1 接口概述

接口是一个公共的规范标准，只要符合规范标准，大家都可以通用。

Java中的接口更多地体现在对行为的抽象



#### 15.3.2 接口的特点

- 接口用关键字interface修饰

```java
public interface 接口名{}
```

- 类实现接口用implements表示

```java
public class 类名 implements 接口名{}
```

- **接口不能实例化**

  接口的实例化参照多态的方式，通过 **实现类对象** 实例化，这叫**接口多态**。

  多态的形式：具体类多态，**抽象类多态**，**接口多态**

  多态的前提：有继承或者实现关系；有方法重写；有父（类/接口)引用指向(子/实现)类对象

- 接口的子类

  要么重写接口中的**所有抽象方法**

  **要么子类也是抽象类**

代码示例：

```java
/*
* 定义了一个接口
* */
public interface Jumpping {
    public abstract void jump();
}
```

```java
public class Cat implements Jumpping{
    @Override
    public void jump() {
        System.out.println("猫可以跳");
    }
}
```

```java
public class InterfaceDemo {
    public static void main(String[] args) {
//        Cat c = new Cat();
//        c.jump(); // 正确

        Jumpping j = new Cat();
        j.jump(); // 正确
    }
}
```



#### 15.3.3 接口的成员特点

- 成员变量：接口中没有变量，你定义的所有变量都会默认在变量前加修饰符：public static final

  **只能是常量，默认修饰符：public static final**

  **接口中的常量可以使用：接口名.常量名，进行访问**

- 构造方法

  没有，因为借口主要是扩展功能的，而没有具体存在

- 成员方法

  只能是抽象方法

  默认修饰符：public abstract

  关于接口中的方法：JDK8和JDK9中有一些新特性，后面再讲。

- 代码演示

  - 接口

  ```java
  public interface Inter {
      public int num = 10;
      public final int num2 = 20;
      public static final int num3 = 30;
      int num4 = 40; // 接口里面的变量默认是：public static final
  
  //    public inter(){} // 接口没有构造方法
  //    public void show(){} // 接口里面没有普通方法
      public abstract void show(); // 接口里面只能有抽象方法
      void method(); // 默认是抽象方法,修饰符：public abstract
  }
  ```



**案例演示**

![image-20230220182423123](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302201824183.png)

```java
public abstract class Animal {
    private String name;
    private int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Animal() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public abstract void eat();
}
```

```java
public interface Jumpping {
    public abstract void jump();
}
```

```java
public class Cat extends Animal implements Jumpping{
    @Override
    public void eat() {
        System.out.println("猫爱吃鱼");
    }

    @Override
    public void jump() {
        System.out.println("猫能跳高");
    }

    public Cat(String name, int age) {
        super(name, age);
    }

    public Cat() {
    }
}
```

```java
public class Dog extends Animal implements Jumpping{
    public Dog(String name, int age) {
        super(name, age);
    }

    public Dog() {
    }

    @Override
    public void eat() {
        System.out.println("狗爱吃骨头");
    }

    @Override
    public void jump() {
        System.out.println("狗能跳高");
    }
}
```

```java
public class AnimalDemo {
    public static void main(String[] args) {
        // 接口多态的体现
        Jumpping j = new Cat();
        j.jump(); // 此时Jumpping中只有jump方法，所以不能执行Cat中的eat方法

        // 抽象类多态的体现
        Animal a = new Cat("加菲", 4);
        System.out.println(a.getName());
        a.eat(); // 此时不能调用Jummping接口中的jump方法
//        a.jump();

        // 总结：接口只能调用接口里面的方法；抽象类只能调用抽象类里面的方法

        // 使用原始的方法则可以调用这两个方法
        // 使用子类，子类里面有最多的方法
        Cat cat = new Cat("加菲",3);
        System.out.println(cat.getName());
        cat.eat();
        cat.jump();
    }
}
```

**总结：**

- 无论使用抽象类多态还是接口多态，都只能调用个字类中的方法。
- 要想调用所有方法，可以使用子类实例化



#### 15.3.4 类与接口的关系

- 类与类的关系

  继承关系，只能单继承，但是可以多层继承

- 类与接口的关系

  实现关系，可以单实现，也可以多实现，还可以在继承一个类的同时实现多个接口

- 接口与接口的关系

  继承关系，可以单继承，也可以多继承



代码示例：

类与接口的关系：单实现，多实现，同时也可以继承一个类

```java
public class InterImpl extends Object implements Inter1,Inter2,Inter3{
    
}
```

接口与接口的关系：单继承，多继承

```java
public interface Inter1 extends Inter2, Inter3{

}
```





#### 15.3.5 抽象类与接口的区别

- 成员区别

  - 抽象类

    有变量，有常量；有构造方法；有抽象方法，也有非抽象方法

  - 接口

    只有常量；只有抽象方法

- 关系区别

  - 类与类

    继承、单继承

  - 类与接口

    实现，单实现，多实现

  - 接口与接口

    继承、单继承、多继承

- 设计理念区别

  - 抽象类

    **对类抽象**，包括属性，行为

  - 接口

    **对行为抽象**，主要是行为

案例示例：

![image-20230220200237357](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202002419.png)



![image-20230220200708713](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202007786.png)

![image-20230220202313934](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202023995.png)



```java
package com.javase.interface04;

public interface SpeackEnglish {
    public abstract void speackEnglish();
}
```

```java
package com.javase.interface04;

public abstract class Human {
    private String name;
    private int age;

    public Human(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Human() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public abstract void eat();
}
```

```java
package com.javase.interface04;

public abstract class Coach extends Human{
    public Coach(String name, int age) {
        super(name, age);
    }

    public Coach() {
    }

    public abstract void teach();
}
```

```java
package com.javase.interface04;

public abstract class Player extends Human{
    public Player(String name, int age) {
        super(name, age);
    }

    public Player() {
    }

    public abstract void study();
}
```

```java
package com.javase.interface04;

public class BasketballCoach extends Coach{
    @Override
    public void eat() {
        System.out.println("篮球教练吃饭");
    }

    @Override
    public void teach() {
        System.out.println("篮球教练教人");
    }

    public BasketballCoach(String name, int age) {
        super(name, age);
    }

    public BasketballCoach() {
    }
}
```

```java
package com.javase.interface04;

public class FootballCoach extends Coach implements SpeackEnglish{
    public FootballCoach(String name, int age) {
        super(name, age);
    }

    public FootballCoach() {
    }

    @Override
    public void eat() {
        System.out.println("足球教练吃饭");
    }

    @Override
    public void teach() {
        System.out.println("足球教练教足球");
    }

    @Override
    public void speackEnglish() {
        System.out.println("足球运动员会说英语");
    }
}
```

```java
package com.javase.interface04;

public class BasketballPlayer extends Player{
    @Override
    public void eat() {
        System.out.println("篮球运动员吃饭");
    }

    @Override
    public void study() {
        System.out.println("篮球运动员学习");
    }

    public BasketballPlayer(String name, int age) {
        super(name, age);
    }

    public BasketballPlayer() {
    }
}
```

```java
package com.javase.interface04;

public class FootballPlayer extends Player implements SpeackEnglish{
    public FootballPlayer(String name, int age) {
        super(name, age);
    }

    public FootballPlayer() {
    }

    @Override
    public void eat() {
        System.out.println("足球运动员吃饭");
    }

    @Override
    public void study() {
        System.out.println("足球运动员学习");
    }

    @Override
    public void speackEnglish() {
        System.out.println("足球运动员说英语");
    }
}
```

```java
package com.javase.interface04;

public class HumanDemo {
    public static void main(String[] args) {
        FootballPlayer footballPlayer = new FootballPlayer("姚明",35);
        footballPlayer.eat();
        footballPlayer.study();
        footballPlayer.speackEnglish();
    }
}
```





## 16 形参和返回值

### 16.1 类名作为形参和返回值

- 1、类名作为方法的形参

  方法的形参是类名，其实需要的是该类的对象

  实际传递的是该对象的【地址值】

- 2、类名作为方法的返回值

  方法的返回值是类名，其实返回的是该类的对象

  实际传递的，也是该对象的【地址值】

- 示例代码

  ![image-20230220204131186](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202041253.png)



### 16.2 抽象类作为形参和返回值

- 抽象类作为形参和返回值

  - 方法的形参是抽象类名，其实需要的是该抽象类的子类对象
  - 方法的返回值是抽象类名，其实返回的是该抽象类的子类对象

- 示例代码

  ![image-20230220205004735](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202050803.png)



### 16.3 接口作为形参和返回值

- 接口作为形参和返回值

  - 方法的形参是接口名，其实需要的是该接口的实现类对象
  - 方法的返回值是接口名，其实返回的是该接口的实现类对象

- 示例代码

  ![image-20230220210143711](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202101782.png)



## 17 内部类

### 17.1 内部类的基本使用

- 内部类概念

  在一个类中定义一个类，举例：在一个类A中定义一个类B，类B就被称为内部类。

- 内部类定义&格式

  ![image-20230220210424130](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302202104188.png)

- 内部类的访问特点
  - **内部类可以直接访问外部类的成员，包括私有**
  - **外部类要访问内部类的成员，必须创建对象**

- 示例代码：

  ```java
  /*
  * 内部类访问特点：
  *   内部类可以直接访问外部类的成员，包括私有
  *   外部类要访问内部类的成员，必须创建对象
  * */
  public class Outer {
      private int num = 10;
      public class Inner{
          public void show(){
              System.out.println(num);
          }
      }
  
      public void method(){
          Inner in = new Inner();
          in.show();
      }
  }
  ```



### 17.2 成员内部类的理解

- 成员内部类的定义位置

  - 在类中方法，跟成员变量是一个位置

- 外界创建成员内部类格式

  - 格式：外部类名.内部类名 对象名 = 外部类对象.内部类对象；
  - 举例：Outer.Inner oi = new Outer().new Inner();

- **成员内部类的推荐使用方法**

  - 将一个类设计为一个内部类的目的，大多数是不想让外界去访问，所以内部类的定义应该私有化。私有化之后，再提供一个可以让外界调用的方法，方法内部创建内部类对象并调用。

- 示例代码：

  ```java
  class Outer{
      private int num = 10;
      private class Inner{
          public void show(){
              System.out.println(num);
          }
      }
      public void method(){
          Inner in = new Inner();
          in.show();
      }
  }
  
  public class InnerDemo{
      public static void main(String[] args){
          // Outer.Inner oi = new Outer().new Inner();
          // oi,show();
          Outer o = new Outer();
          o.method();
      }
  }
  ```

  

### 17.3 局部内部类的理解

- 局部内部类定义的位置
  - 局部内部类是在方法中定义的类
- 局部内部类方法方式
  - 局部内部类，外界是无法使用的，需要在方法内部创建对象并使用
  - 该类可以直接访问外部类的成员，也可以访问方法内的局部变量
- 示例代码：

```java
class Outer {
    private int num = 10;
    public void method(){
        int num2 = 20;
        class Inner {
            public void show(){
                System.out.println(num);
                Symtem.out.println(num2);
            }
        }
        Inner i = new Inner();
        i.show();
    }
}

public class OuterDemo{
    public static void main(String[] args){
        Outer o = new Outer();
        o.method();
    }
}
```



### 17.4 匿名内部类

- 匿名内部类的前提

  - 存在一个类或者一个接口，这里的类可以是具体的类或者是抽象类

- 匿名内部类的格式

  - 格式：new 类名(){重写方法}  new 接口名(){重写方法}

  - 举例

    ```java
    new Inter(){
    	@Override
        public void method(){}
    }
    ```

- 匿名内部类的本质

  - 本质：是一个继承了该类或者实现了该接口的子类匿名对象

- 匿名内部类的细节

  - 匿名内部类可以通过多态的形式接受

    ```java
    Inert i = new Inter(){
        @Override
        public void method(){
            
        }
    }
    ```

  - 匿名内部类直接调用方法

    ```java
    interface Inter{
        void method();
    }
    
    class Test{
        public static void main(String[] args){
            new Inter(){
                @Override
                public void method(){
                    System.out.println("我是匿名内部类");
                }
            }.method();// 直接调用方法
        }
    }
    ```



### 17.5 匿名内部类在开发中的使用

- 匿名内部类在开发中的使用

  - 当发现某个方法需要  接口或抽象类的子类对象，我们就可以传递一个匿名内部类过去，来简化传统的代码

- 示例代码：

  ```java
  interface Jumpping{
  	void jump();
  }
  
  class Cat implements Jumpping{
      @Override
      public void jump(){
          System.out.println("猫可以跳高");
      }
  }
  
  class Dog implements Jumpping{
      @Override
      public void jump(){
          System.out.println("狗可以跳高");
      }
  }
  
  class JumppingOperator{
      public void method(Jumpping j){
          j.jump();
      }
  }
  
  class JumppingDemo{
      public static void main(String[] args){
          JumppingOperator jo = new JumppingOperator();
          Jumpping cat = new Cat();
          jo.method(cat);
          
          Jumpping dog = new Dog();
          jo.method(dog);
          
          // 匿名内部类
          jo.method(
          	new Jumpping(){
                  @Override
                  public void jump(){
                      System.out.println("猫可以跳高");
                  }
              }
          );
          
          jo.method(
          	new Jumpping(){
                  @Override
                  public void jump(){
                      System.out.println("狗可以跳高");
                  }
              }
          );
      }
  }
  ```

  

## 18 常用API

### 18.1 Math

![image-20230222140352523](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221403651.png)



### 18.2 System

![image-20230222140539082](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221405134.png)

![image-20230222140549628](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221405687.png)



### 18.3 Object类的toString方法

![image-20230222140807526](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221408583.png)

```java
package com.javase01.innerclass01;

public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Student() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

```

```java
package com.javase01.innerclass01;

public class ObjectDemo {
    public static void main(String[] args) {
        Student stu = new Student();

        stu.setAge(32);
        stu.setName("林青霞");
        System.out.println(stu.toString());
        System.out.println(stu);
    }
}
```

运行结果：

```java
Student{name='林青霞', age=32}
Student{name='林青霞', age=32}
```



### 18.4 Object类的equals方法

![image-20230222141342417](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221413475.png)

```java
package com.javase01.innerclass01;

public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Student() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }



    @Override
    public boolean equals(Object o) {
        /*
        * this --- stu
        * o    --- stu1
        * */
        // 比较地址是否相同，如果相同，直接返回true
        if (this == o) return true;

        //判断参数是否为null
        //判断两个对象是否来自同一个类
        if (o == null || getClass() != o.getClass()) return false;

        // 向下转型
        Student student = (Student) o;

        // 比较年龄是否相同，不带this，表示age代表本实例的age
        if (age != student.age) return false;
        // 比较name的内容是否相同，name是String类型，String的equals方法是比较两个字符串的内容是否相同，区分大小写
        return name != null ? name.equals(student.name) : student.name == null;
    }
}
```

```java
package com.javase01.innerclass01;

public class ObjectDemo {
    public static void main(String[] args) {
        Student stu = new Student();

        stu.setAge(32);
        stu.setName("林青霞");
        System.out.println(stu.toString());
        System.out.println(stu);

        Student stu1 = new Student("林青霞", 32);

        // 需求，比较两个对象的内容是否相同
//        System.out.println(stu==stu1); // false

//        System.out.println(stu.equals(stu1)); // false
        /*
        * equals()源码：
        *   public boolean equals(Object obj) {
        *       // this --- stu
        *       // obj --- stu1
        *       // 所以返回false
                return (this == obj);
            }
            * 所以重写equals()
        * */
        System.out.println(stu.equals(stu1));

        System.out.println(stu.getClass());// class com.javase01.innerclass01.Student
        System.out.println(stu1.getClass()); // class com.javase01.innerclass01.Student


    }
}
```



### 18.5 Arrays应用

![image-20230222180831874](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221808943.png)

- 像Math、Arrays、System统称为工具类
- 它们的构造方法用private修饰，是为了避免外界创建对象
- 成员用public static 修饰是为了让他们的方法能够直接通过类名调用。

总结：在帮助文档中，并没有给出Math、System、Arrays的构造方法，其实并不是他们没有构造方法，而是在源码里默认把他们的构造方法私有化，因此不能被外界继承。但是，他们类中的方法都使用了静态修饰符，可以直接通过类名调用，这是他们的设计特点。
