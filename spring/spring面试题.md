[TOC]

# 1、谈谈你对spring的理解

![image-20240321130007630](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211300724.png)



# 2、Spring的优缺点

![image-20240321130636242](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211306275.png)

![image-20240321130801662](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211308706.png)

![image-20240321130837892](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211308934.png)

![image-20240321130931019](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211309048.png)

![image-20240321131025990](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211310020.png)

![image-20240321131110548](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211311590.png)

![image-20240321131217584](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211312635.png)

![image-20240321131515166](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211315196.png)



# 3、spring的特性和优势

![image-20240321130255969](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211302004.png)

# 4、spring有哪些组件

![image-20240321130359635](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211303660.png)

# 5、什么是springIOC容器？有什么作用？优点是什么？

![image-20240321131808763](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211318831.png)

![image-20240321132316186](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211323207.png)

![image-20240321132327115](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211323499.png)

# 6、Spring IOC的实现机制是什么

简单工厂+反射

![image-20240321164224557](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211643845.png)

# 7、IOC和DI的区别是什么

DI实现注入，把对象注入给IOC，目的是为了降低耦合度。DI是实现IOC重要的一环。

IOC和DI是从不同的角度描述的同一件事，IOC是从容器的角度描述，而DI是从应用程序的角度来描述，也可以这样说，IOC是依赖倒置原则的设计思想，而 DI是具体的实现方式



# 8、紧耦合和松耦合有什么区别？如何编写松耦合的代码？

![image-20240321133845133](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211338181.png)

![image-20240321134337141](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211343178.png)

最开始电脑没有单独的鼠标的，鼠标的功能单独划分出来，负责单一职责。



![image-20240321134452625](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211345420.png)

![image-20240321134639110](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211346148.png)



# 9、BeanFactory的作用

![image-20240321135340043](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211353090.png)

![image-20240321165107998](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211651033.png)

![image-20240321165527835](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211655909.png)



# 10、BeanDifinition的作用

![image-20240321165929568](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211659609.png)

![image-20240321170558207](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211705272.png)

![image-20240321170754426](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211707476.png)

概念态就是指xml文件，定义Bean的属性。

定义态：定义了封装Bean的生产指标



# 11、BeanFactory和FactoryBean有什么区别？

![image-20240321180423521](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211804576.png)



# 12、BeanFactory和ApplicationContext有什么区别

BeanFactory和ApplicationContext是Spring的两大核心接口，都可以当做Spring的容器。

其中ApplicationContext是 BeanFactory的子接口。 

依赖关系 BeanFactory：是Spring里面最顶层的接口，包含了各种Bean的定义，读取bean配置文档，管理bean的加载、实例化，控制bean的生命周期，维护bean之间的依赖关系。BeanFactory 简单粗暴，可以理解为就是个 HashMap，Key 是 BeanName，Value 是 Bean 实例。通常只提供注册（put），获取（get）这两个功能。我们可以称之为 “**低级容器**”。

ApplicationContext 可以称之为 “**高级容器**”。因为他比 BeanFactory 多了更多的功能。他继承了多个接口。因此具备了更多的功能。例如资源的获取，支持多种消息（例如 JSP tag 的支持），对 BeanFactory 多了工具级别的支持等 。所以你看他的名字，已经不是 BeanFactory 之类的工厂了，而是 “应用上下文”， 代表着整个大容器的所有功能。该接口定义了一个 **refresh** 方法，此方法是所有阅读 Spring 源码的人的最熟悉的方法，用于刷新整个容器，即重新 **加载/刷新** 所有的 bean。

ApplicationContext接口作为BeanFactory的派生，除了提供BeanFactory所具有的功能外，还提供了更完整的框架功能：

![image-20240321174508402](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211745427.png)



**共同点：**都可以作为容器

**区别：**

BeanFactory   getBean 用于生产bean

- 优点：内存占用率小，可以用于嵌入式设备

ApplicationContext 实现了BeanFactory。它不生产Bean，而是通知FactoryBean来进行生产，getBean是一个门面方法，他做的事情更多：

- 会自动帮我们把我们的配置bean注册进来
- 加载环境变量
- 支持多语言
- 实现事件监听
- 注册很多对外扩展点



# 13、说一下Spring IOC容器的加载过程

![image-20240321180555491](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403211805536.png)

**概念态——>定义态的过程：**

1、实例化一个`ApplicationContext`的对象

2、调用bean工厂后置处理器完成扫描

3、循环解析扫描出来的类信息，看是否有@Component

4、实例化一个BeanDefinition对象来存储解析出来的信息

5、把实例化好的beanDefinition对象put到beanDefinitionMap当中缓存起来，以便后面实例化bean

6、再次调用其他的bean工厂后置处理器

**从定义态到纯净态：**

7、当然spring还会干很多的事情，比如国际化，比如注册BeanPostProcessor等等，如果我们只关心如何实例化一个bean的话那么这一步就是spring调用finishBeanFactoryInitialization方法来实例化单例的bean，**实例化**之前spring要做验证，需要遍历所有扫描出来的类，依次判断这个bean是否lazy，是否prototype，是否abstract等等

8、如果验证完成，spring在实例化一个bean之前需要推断构造方法，因为spring实例化对象是通过构造方法反射，故而需要知道用到哪个构造方法

9、推断完构造方法之后spring调用构造方法反射实例化一个对象；注意，这里指的是**对象**，这个时候对象已经实例化出来了，但是并不是一个完整的bean，最简单的体现是这个时候实例化出来的对象属性是没有注入的，所以不是一个完整的bean

**从纯净态到成熟态：**

10、spring处理合并后的beanDefinition

11、判断是否需要完成**属性注入**

12、如果需要完成属性注入，则开始注入属性

**初始化：**

13、判断bean的类型回调Aware接口

14、调用生命周期回调方法

15、如果需要代理则完成代理

**创建完成：**

16、put到单例池——bean完成——存到spring容器当中



# 14、spring IOC有哪些扩展点，在什么时候调用

![image-20240321201028806](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212010948.png)

![image-20240321202246127](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212023457.png)

![image-20240321222140509](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212221546.png)

![image-20240321222211042](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212222104.png)

![image-20240321222354671](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212223734.png)

![image-20240321222942361](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212229432.png)

![image-20240321222958042](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212229098.png)

# 15、什么是Spring Bean？JavaBean和SpringBean和对象的区别

![image-20240321223222338](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212232390.png)

![image-20240321223256844](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212232874.png)

java的类就是JavaBean，javaBean的对象是自己实例化出来的，bean的对象是由ioc实例化出来的。



# 16、配置Bean有几种方式

![image-20240321223651199](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212236248.png)



# 17、解释spring支持的几种bean的作用域

![image-20240321224454854](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212244901.png)



# 18、单例bean的优势

由于不会每次都新创建新对象所以有以下几个性能上的优势： 

1.减少了新生成实例的消耗。新生成实例消耗包括两方面，第一，spring会通过反射或者cglib来生成bean实例，这都是耗性能的操作。第二，给对象分配内存也会涉及复杂算法。 单例bean提高服务器内存的利用率 ，减少服务器内存消耗  

2.减少jvm垃圾回收由于不会给每个请求都新生成bean实例，所以自然回收的对象少了。 

3.可以快速获取到bean因为单例的获取bean操作除了第一次生成之外其余的都是从缓存里获取的所以很快



# 19、Spring实例化bean的几种方式

1、构造器方式（反射）xml+注解@Component

2、静态工厂方式；factory-method

![image-20240321235449289](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212354329.png)

![image-20240321235640292](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212356334.png)

在写xml的时候给当前的bean写一个factory-method所指定的方法，

3、实例工厂方式（@Bean）；factory-bean + factory-method

![image-20240321235721574](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403212357604.png)

在factory-method的基础上再加一个factory-bean，调用factory-bean的factory-method方法。

4、FactoryBean方式

![image-20240322000213090](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220002114.png)

让对象继承FactoryBean接口，然后重写其中的两个方法。

![image-20240322000251165](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220002191.png)



# 20、spring框架中的单例bean是线程安全的吗

不是，Spring框架中的单例bean不是线程安全的

spring中的bean默认是单例模式，spring框架并没有对单例bean进行多线程的封装处理。

实际上大部分时候spring bean无状态的（比如dao类），所以某种长度上说bean也是线程安全的，但如果bean有状态的话（比如 view model对象），那就要开发者自己去保证线程安全了，最简单的就是改变bean的作用域，把`singleton`改为`prototype`，这样请求的bean相当于new Bean()了，所以就可以保证线程安全了。

- 有状态：有数据存储的功能（比如成员变量读写）
- 无状态：不会保存数据



# 21、spring如何处理并发问题

在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作 用域，因为Spring对一些Bean中非线程安全状态采用ThreadLocal进行处理，解决线程安全问题。 ThreadLocal和线程同步机制都是为了解决多线程中相同变量的访问冲突问题。同步机制采用了“时间换空间”的方式， 仅提供一份变量，不同的线程在访问前需要获取锁，没获得锁的线程则需要排队。而ThreadLocal采用了“空间换时 间”的方式。 ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本，从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码 时，可以把不安全的变量封装进ThreadLocal。

![image-20240322000355165](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220003202.png)



# 22、什么是bean装配？什么是bean的自动装配

**装配**，或**bean 装配**是指在Spring 容器中把bean组装到一起，前提是容器需要知道**bean的依赖关系**，如何通过依赖注入来把它们装配到一起

在Spring框架中，在配置文件中设定bean的依赖关系是一个很好的机制，Spring 容器能够**自动装配相互合作的bean**， 这意味着容器不需要配置，能通过Bean工厂自动处理bean之间的协作。这意味着 Spring可以通过向Bean Factory中 注入的方式自动搞定bean之间的依赖关系。自动装配可以设置在每个bean上，也可以设定在特定的bean上。

# 23、自动装配有什么限制吗？

- 一定要声明set方法  

- 覆盖： 你仍可以用 < constructor-arg >和 < property > 配置来定义依赖，这些配置将始终覆盖自动注入。 
- 基本数据类型：不能自动装配简单的属性，如基本数据类型、字符串和类。  (手动注入还是可以注入基本数据类型的）
- 模糊特性：自动装配不如显式装配精确，如果有可能尽量使用显示装配。   

所以更推荐使用手动装配(@Autowired（根据类型、再根据名字）  ref="" 这种方式 更加灵活更加清晰 ) 

# 24、解释不同方式的自动装配，spring自动装配bean有哪些方式？

在spring中，对象无需自己查找或创建与其关联的其他对象，由容器负责把需要相互协作的对象引用赋予各个对象，使 用autowire来配置自动装载模式。 

在Spring框架xml配置中共有5种自动装配： 

- no：默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配bean。@Autowired 来进行手动指定 需要自动注入的属性 
- byName：通过bean的名称进行自动装配，如果一个bean的 property 与另一bean 的name 相同，就进行自 动装配。 
- byType：通过参数的数据类型进行自动装配。 
- constructor：利用构造函数进行装配，并且构造函数的参数通过byType进行装配。 
- autodetect：自动探测，如果有构造方法，通过 construct的方式自动装配，否则使用 byType的方式自动装 配。 （在spring3.0+弃用）

# 25、有哪些生命周期回调方法？有哪几种实现方式？

初始化：init

销毁：destroy

![image-20240322002108831](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220021872.png)

第一种方式：@PostConstruct、@PreDestroy

![image-20240322002348178](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220023213.png)

![image-20240322002424317](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220024365.png)

第二种方式：InitializingBean、DisposableBean

![image-20240322002538636](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220025677.png)

![image-20240322002610022](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220026050.png)

第三种方式：通过@Bean ：init-method、destroy-method

![image-20240322002834958](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220028996.png)

![image-20240322002900981](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220029025.png)

![image-20240322002921336](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220029376.png)

![image-20240322003107609](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220031635.png)

# 26、spring在加载过程中Bean有哪几种形态

![image-20240322003721645](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220037690.png)

# 27、解释Spring框架中bean的生命周期

Bean的生命周期：指的是bean从创建到销毁的整个过程

这个过程大致可以分为4大步骤：

- 实例化

  - 通过反射去推断构造函数进行实例化
  - 比如：通过实例工厂、静态工厂进行实例化

- 依赖注入

  - 解析自动装配（byname，bytype，constructor，no @Autowired）DI的体现

  - 属性注入的过程中可能出现**循环依赖**，但是spring已经解决这个问题

    ![image-20240322004704682](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220047719.png)

- 初始化

  - 调用XXXAware回调方法

    ![image-20240322011739082](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220117126.png)

  - 调用初始化生命周期回调方法（三种）

    ![image-20240322002108831](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220021872.png)

  - 如果bean实现了aop，创建动态代理

    - 调用BeanPostProcessor.postProcessAfterInitialization, 如果bean实现aop则会在这里创建动态代理

- 销毁

  - 在spring容器关闭的时候进行调用
  - 调用销毁生命周期回调

![image-20240322012211235](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220122950.png)

![image-20240322012347739](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220123794.png)



# 28、什么是循环依赖

参考博客：[面试必杀技，讲一讲Spring中的循环依赖](https://developer.aliyun.com/article/766880)

首先纠正几个关于循环依赖的错误的说法：

- 只有在setter方式注入的情况下，循环依赖才能被解决（**错**）
- 三级缓存的目的是为了提高效率（**错**）

**1、什么是循环依赖？**

从字面意思上说，A依赖B，B依赖A

![image-20240322005134717](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220051746.png)

体现的代码层次就是这个样子：

```java
@Component
public class A {
    // A中注入了B
    @Autowired
    private B b;
}

@Component
public class B {
    // B中也注入了A
    @Autowired
    private A a;
}
```

还有一种比较特殊的：

```java
// 自己依赖自己
@Component
public class A {
    // A中注入了A
    @Autowired
    private A a;
}
```



**2、什么情况下循环依赖可以被处理？**

在回答这个问题之前首先明确一点，spring解决循环依赖是由前置条件的

- 出现循环依赖的Bean必须要是单例
- 依赖注入的方式不能全是构造器注入的方式（很多博客上说，只能解决setter方法的循环依赖，这是错误的）

其中第一点应该很好理解，第二点：不能全是构造器注入是什么意思呢？我们还是用代码说话：

```java
@Component
public class A {
//    @Autowired
//    private B b;
    public A(B b) {

    }
}


@Component
public class B {

//    @Autowired
//    private A a;

    public B(A a){

    }
}
```

在上面的例子中，A中注入B的方式是通过构造器，B中注入A的方式也是通过构造器，这个时候循环依赖是无法被解决，如果你的项目中有两个这样相互依赖的Bean，在启动时就会报出以下错误：

```java
Caused by: org.springframework.beans.factory.BeanCurrentlyInCreationException: Error creating bean with name 'a': Requested bean is currently in creation: Is there an unresolvable circular reference?
```

![image-20240322005810595](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220058631.png)

**3、spring是如何解决循环依赖的？**

关于循环依赖的解决方式可以分为两种：

- 没有AOP的循环依赖
- 结合了AOP的循环依赖

**3.1 简单的循环依赖（没有AOP）**

```java
@Component
public class A {
    // A中注入了B
    @Autowired
    private B b;
}

@Component
public class B {
    // B中也注入了A
    @Autowired
    private A a;
}
```

通过上文我们已经知道了这种情况下的循环依赖是能够被解决的，那么具体的流程是什么呢？我们一步步分析

首先，我们要知道**Spring在创建Bean的时候默认是按照自然排序来进行创建的，所以第一步Spring会去创建A**。

与此同时，我们应该知道，Spring在创建Bean的过程中分为三步

1. 实例化，对应方法：`AbstractAutowireCapableBeanFactory`中的`createBeanInstance`方法
2. 属性注入，对应方法：`AbstractAutowireCapableBeanFactory`的`populateBean`方法
3. 初始化，对应方法：`AbstractAutowireCapableBeanFactory`的`initializeBean`

这些方法在之前源码分析的文章中都做过详细的解读了，如果你之前没看过我的文章，那么你只需要知道

1. 实例化，简单理解就是new了一个对象
2. 属性注入，为实例化中new出来的对象填充属性
3. 初始化，执行aware接口中的方法，初始化方法，完成`AOP`代理

基于上面的知识，我们开始解读整个循环依赖处理的过程，整个流程应该是以A的创建为起点，前文也说了，第一步就是创建A嘛！

![image-20200706092738559](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220105518.png)

创建A的过程实际上就是调用`getBean`方法，这个方法有两层含义

1. 创建一个新的Bean
2. 从缓存中获取到已经被创建的对象

我们现在分析的是第一层含义，因为这个时候缓存中还没有A嘛！



**3.2 调用getSingleton(beanName)**

首先调用`getSingleton(a)`方法，这个方法又会调用`getSingleton(beanName, true)`，在上图中我省略了这一步

```java
public Object getSingleton(String beanName) {
    return getSingleton(beanName, true);
}
```

`getSingleton(beanName, true)`这个方法实际上就是到缓存中尝试去获取Bean，整个缓存分为三级

1. `singletonObjects`，一级缓存，存储的是所有创建好了的单例Bean
2. `earlySingletonObjects`，完成实例化，但是还未进行属性注入及初始化的对象
3. `singletonFactories`，提前暴露的一个单例工厂，二级缓存中存储的就是从这个工厂中获取到的对象

因为A是第一次被创建，所以不管哪个缓存中必然都是没有的，因此会进入`getSingleton`的另外一个重载方法`getSingleton(beanName, singletonFactory)`。



**3.3 调用getSingleton(beanName, singletonFactory)**

这个方法就是用来创建Bean的，其源码如下：

```java
public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(beanName, "Bean name must not be null");
    synchronized (this.singletonObjects) {
        Object singletonObject = this.singletonObjects.get(beanName);
        if (singletonObject == null) {

            // ....
            // 省略异常处理及日志
            // ....

            // 在单例对象创建前先做一个标记
            // 将beanName放入到singletonsCurrentlyInCreation这个集合中
            // 标志着这个单例Bean正在创建
            // 如果同一个单例Bean多次被创建，这里会抛出异常
            beforeSingletonCreation(beanName);
            boolean newSingleton = false;
            boolean recordSuppressedExceptions = (this.suppressedExceptions == null);
            if (recordSuppressedExceptions) {
                this.suppressedExceptions = new LinkedHashSet<>();
            }
            try {
                // 上游传入的lambda在这里会被执行，调用createBean方法创建一个Bean后返回
                singletonObject = singletonFactory.getObject();
                newSingleton = true;
            }
            // ...
            // 省略catch异常处理
            // ...
            finally {
                if (recordSuppressedExceptions) {
                    this.suppressedExceptions = null;
                }
                // 创建完成后将对应的beanName从singletonsCurrentlyInCreation移除
                afterSingletonCreation(beanName);
            }
            if (newSingleton) {
                // 添加到一级缓存singletonObjects中
                addSingleton(beanName, singletonObject);
            }
        }
        return singletonObject;
    }
}
```

上面的代码我们主要抓住一点，通过`createBean`方法返回的Bean最终被放到了一级缓存，也就是单例池中。

那么到这里我们可以得出一个结论：**一级缓存中存储的是已经完全创建好了的单例Bean**



**3.4 调用addSingletonFactory方法**

如下图所示：

![image-20200706105535307](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220107870.png)

在完成Bean的实例化后，属性注入之前Spring将Bean包装成一个工厂添加进了三级缓存中，对应源码如下：

```java
// 这里传入的参数也是一个lambda表达式，() -> getEarlyBeanReference(beanName, mbd, bean)
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(singletonFactory, "Singleton factory must not be null");
    synchronized (this.singletonObjects) {
        if (!this.singletonObjects.containsKey(beanName)) {
            // 添加到三级缓存中
            this.singletonFactories.put(beanName, singletonFactory);
            this.earlySingletonObjects.remove(beanName);
            this.registeredSingletons.add(beanName);
        }
    }
}
```

这里只是添加了一个工厂，通过这个工厂（`ObjectFactory`）的`getObject`方法可以得到一个对象，而这个对象实际上就是通过`getEarlyBeanReference`这个方法创建的。那么，什么时候会去调用这个工厂的`getObject`方法呢？这个时候就要到创建B的流程了。

当A完成了实例化并添加进了三级缓存后，就要开始为A进行属性注入了，在注入时发现A依赖了B，那么这个时候Spring又会去`getBean(b)`，然后反射调用setter方法完成属性注入。

![image-20200706114501300](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220105392.png)

因为B需要注入A，所以在创建B的时候，又会去调用`getBean(a)`，这个时候就又回到之前的流程了，但是不同的是，之前的`getBean`是为了创建Bean，而此时再调用`getBean`不是为了创建了，而是要从缓存中获取，因为之前A在实例化后已经将其放入了三级缓存`singletonFactories`中，所以此时`getBean(a)`的流程就是这样子了

![image-20200706115959250](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220108264.png)

从这里我们可以看出，注入到B中的A是通过`getEarlyBeanReference`方法提前暴露出去的一个对象，还不是一个完整的Bean，那么`getEarlyBeanReference`到底干了啥了，我们看下它的源码

```java
protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
    Object exposedObject = bean;
    if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof SmartInstantiationAwareBeanPostProcessor) {
                SmartInstantiationAwareBeanPostProcessor ibp = (SmartInstantiationAwareBeanPostProcessor) bp;
                exposedObject = ibp.getEarlyBeanReference(exposedObject, beanName);
            }
        }
    }
    return exposedObject;
}
```

它实际上就是调用了后置处理器的`getEarlyBeanReference`，而真正实现了这个方法的后置处理器只有一个，就是通过`@EnableAspectJAutoProxy`注解导入的`AnnotationAwareAspectJAutoProxyCreator`。**也就是说如果在不考虑`AOP`的情况下，上面的代码等价于：**

```java
protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
    Object exposedObject = bean;
    return exposedObject;
}
```

**也就是说这个工厂啥都没干，直接将实例化阶段创建的对象返回了！所以说在不考虑`AOP`的情况下三级缓存有用嘛？讲道理，真的没什么用**，我直接将这个对象放到二级缓存中不是一点问题都没有吗？如果你说它提高了效率，那你告诉我提高的效率在哪?

![image-20200706124118108](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220108826.png)

那么三级缓存到底有什么作用呢？不要急，我们先把整个流程走完，在下文结合`AOP`分析循环依赖的时候你就能体会到三级缓存的作用！

到这里不知道小伙伴们会不会有疑问，B中提前注入了一个没有经过初始化的A类型对象不会有问题吗？

答：不会

这个时候我们需要将整个创建A这个Bean的流程走完，如下图：

![image-20200706133018669](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220105326.png)

从上图中我们可以看到，虽然在创建B时会提前给B注入了一个还未初始化的A对象，但是在创建A的流程中一直使用的是注入到B中的A对象的引用，之后会根据这个引用对A进行初始化，所以这是没有问题的。

**3.5 结合了AOP的循环依赖**

之前我们已经说过了，在普通的循环依赖的情况下，三级缓存没有任何作用。三级缓存实际上跟Spring中的`AOP`相关，我们再来看一看`getEarlyBeanReference`的代码：

```java
protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
    Object exposedObject = bean;
    if (!mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
        for (BeanPostProcessor bp : getBeanPostProcessors()) {
            if (bp instanceof SmartInstantiationAwareBeanPostProcessor) {
                SmartInstantiationAwareBeanPostProcessor ibp = (SmartInstantiationAwareBeanPostProcessor) bp;
                exposedObject = ibp.getEarlyBeanReference(exposedObject, beanName);
            }
        }
    }
    return exposedObject;
}
```

如果在开启`AOP`的情况下，那么就是调用到`AnnotationAwareAspectJAutoProxyCreator`的`getEarlyBeanReference`方法，对应的源码如下：

```java
public Object getEarlyBeanReference(Object bean, String beanName) {
    Object cacheKey = getCacheKey(bean.getClass(), beanName);
    this.earlyProxyReferences.put(cacheKey, bean);
    // 如果需要代理，返回一个代理对象，不需要代理，直接返回当前传入的这个bean对象
    return wrapIfNecessary(bean, beanName, cacheKey);
}
```

回到上面的例子，我们对A进行了`AOP`代理的话，那么此时`getEarlyBeanReference`将返回一个代理后的对象，而不是实例化阶段创建的对象，这样就意味着B中注入的A将是一个代理对象而不是A的实例化阶段创建后的对象。![image-20200706161709829](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220109972.png)

看到这个图你可能会产生下面这些疑问

1. 在给B注入的时候为什么要注入一个代理对象？

答：当我们对A进行了`AOP`代理时，说明我们希望从容器中获取到的就是A代理后的对象而不是A本身，因此把A当作依赖进行注入时也要注入它的代理对象

1. 明明初始化的时候是A对象，那么Spring是在哪里将代理对象放入到容器中的呢？

![image-20200706160542584](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220105313.png)

在完成初始化后，Spring又调用了一次`getSingleton`方法，这一次传入的参数又不一样了，false可以理解为禁用三级缓存，前面图中已经提到过了，在为B中注入A时已经将三级缓存中的工厂取出，并从工厂中获取到了一个对象放入到了二级缓存中，所以这里的这个`getSingleton`方法做的时间就是从二级缓存中获取到这个代理后的A对象。`exposedObject == bean`可以认为是必定成立的，除非你非要在初始化阶段的后置处理器中替换掉正常流程中的Bean，例如增加一个后置处理器：

```java
@Component
public class MyPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if (beanName.equals("a")) {
            return new A();
        }
        return bean;
    }
}
```

不过，请不要做这种骚操作，徒增烦恼！

1. 初始化的时候是对A对象本身进行初始化，而容器中以及注入到B中的都是代理对象，这样不会有问题吗？

答：不会，这是因为不管是`cglib`代理还是`jdk`动态代理生成的代理类，内部都持有一个目标类的引用，当调用代理对象的方法时，实际会去调用目标对象的方法，A完成初始化相当于代理对象自身也完成了初始化

1. 三级缓存为什么要使用工厂而不是直接使用引用？换而言之，为什么需要这个三级缓存，直接通过二级缓存暴露一个引用不行吗？

答：**这个工厂的目的在于延迟对实例化阶段生成的对象的代理，只有真正发生循环依赖的时候，才去提前生成代理对象，否则只会创建一个工厂并将其放入到三级缓存中，但是不会去通过这个工厂去真正创建对象**

我们思考一种简单的情况，就以单独创建A为例，假设AB之间现在没有依赖关系，但是A被代理了，这个时候当A完成实例化后还是会进入下面这段代码：

```java
// A是单例的，mbd.isSingleton()条件满足
// allowCircularReferences：这个变量代表是否允许循环依赖，默认是开启的，条件也满足
// isSingletonCurrentlyInCreation：正在在创建A，也满足
// 所以earlySingletonExposure=true
boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences &&
                                  isSingletonCurrentlyInCreation(beanName));
// 还是会进入到这段代码中
if (earlySingletonExposure) {
    // 还是会通过三级缓存提前暴露一个工厂对象
    addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
}
```

看到了吧，即使没有循环依赖，也会将其添加到三级缓存中，而且是不得不添加到三级缓存中，因为到目前为止Spring也不能确定这个Bean有没有跟别的Bean出现循环依赖。

假设我们在这里直接使用二级缓存的话，那么意味着所有的Bean在这一步都要完成`AOP`代理。这样做有必要吗？

不仅没有必要，而且违背了Spring在结合`AOP`跟Bean的生命周期的设计！Spring结合`AOP`跟Bean的生命周期本身就是通过`AnnotationAwareAspectJAutoProxyCreator`这个后置处理器来完成的，在这个后置处理的`postProcessAfterInitialization`方法中对初始化后的Bean完成`AOP`代理。如果出现了循环依赖，那没有办法，只有给Bean先创建代理，但是没有出现循环依赖的情况下，设计之初就是让Bean在生命周期的最后一步完成代理而不是在实例化后就立马完成代理。

**3.6 三级缓存真的提高了效率了吗？**

现在我们已经知道了三级缓存的真正作用，但是这个答案可能还无法说服你，所以我们再最后总结分析一波，三级缓存真的提高了效率了吗？分为两点讨论：

1. 没有进行`AOP`的Bean间的循环依赖

从上文分析可以看出，这种情况下三级缓存根本没用！所以不会存在什么提高了效率的说法

1. 进行了`AOP`的Bean间的循环依赖

就以我们上的A、B为例，其中A被`AOP`代理，我们先分析下使用了三级缓存的情况下，A、B的创建流程

![image-20200706171514327](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220105349.png)

假设不使用三级缓存，直接在二级缓存中

![image-20200706172523258](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220110362.png)

上面两个流程的唯一区别在于为A对象创建代理的时机不同，在使用了三级缓存的情况下为A创建代理的时机是在B中需要注入A的时候，而不使用三级缓存的话在A实例化后就需要马上为A创建代理然后放入到二级缓存中去。对于整个A、B的创建过程而言，消耗的时间是一样的

综上，不管是哪种情况，三级缓存提高了效率这种说法都是错误的！

**4 总结**

面试官：”Spring是如何解决的循环依赖？“

答：Spring通过三级缓存解决了循环依赖，其中一级缓存为单例池（`singletonObjects`）,二级缓存为早期曝光对象`earlySingletonObjects`，三级缓存为早期曝光对象工厂（`singletonFactories`）。当A、B两个类发生循环引用时，在A完成实例化后，就使用实例化后的对象去创建一个对象工厂，并添加到三级缓存中，如果A被AOP代理，那么通过这个工厂获取到的就是A代理后的对象，如果A没有被AOP代理，那么这个工厂获取到的就是A实例化的对象。当A进行属性注入时，会去创建B，同时B又依赖了A，所以创建B的同时又会去调用getBean(a)来获取需要的依赖，此时的getBean(a)会从缓存中获取，第一步，先获取到三级缓存中的工厂；第二步，调用对象工工厂的getObject方法来获取到对应的对象，得到这个对象后将其注入到B中。紧接着B会走完它的生命周期流程，包括初始化、后置处理器等。当B创建完后，会将B再注入到A中，此时A再完成它的整个生命周期。至此，循环依赖结束！

面试官：”为什么要使用三级缓存呢？二级缓存能解决循环依赖吗？“

答：如果要使用二级缓存解决循环依赖，意味着所有Bean在实例化后就要完成AOP代理，这样违背了Spring设计的原则，Spring在设计之初就是通过`AnnotationAwareAspectJAutoProxyCreator`这个后置处理器来在Bean生命周期的最后一步来完成AOP代理，而不是在实例化后就立马进行AOP代理。

**5 一道思考题**

为什么在下表中的第三种情况的循环依赖能被解决，而第四种情况不能被解决呢？

提示：Spring在创建Bean时默认会根据自然排序进行创建，所以A会先于B进行创建

| 依赖情况               | 依赖注入方式                                       | 循环依赖是否被解决 |
| ---------------------- | -------------------------------------------------- | ------------------ |
| AB相互依赖（循环依赖） | 均采用setter方法注入                               | 是                 |
| AB相互依赖（循环依赖） | 均采用构造器注入                                   | 否                 |
| AB相互依赖（循环依赖） | A中注入B的方式为setter方法，B中注入A的方式为构造器 | 是                 |
| AB相互依赖（循环依赖） | B中注入A的方式为setter方法，A中注入B的方式为构造器 | 否                 |

## 无法解决的循环依赖的情形

上述循环依赖的情况是两个Bean在通过属性注入时产生的，而针对构造器注入的循环依赖Spring是无法解决的。
原因很简单，Spring之所以能解决循环依赖是因为它将Bean的创建过程分成了实例化和初始化两个阶段。实例化完成后Spring就通过三级缓存允许其他Bean先获取到该Bean的缓存。
而针对构造函数注入产生的循环依赖而言，由于在创建阶段就依赖了其他Bean，因此无法先创建实例供其他Bean引用。

# 29、spring是如何解决循环依赖的

spring采用三级缓存解决的，采用三个Map。关键：**一定要有一个缓存保存它的早期的对象来作为循环的出口**

- 一级缓存：存储完整的Bean
- 二级缓存：避免多重循环依赖的情况，重复创建动态代理；并且能够解决并发下获取完整的bean问题——利用双重检查锁+二级缓存
- 三级缓存：
  - 缓存的是函数接口：通过lambda把方法传进去（把Bean的实例和Bean名字传进去（aop创建））
  - 不会立即调：（如果实例化后立即调用的话，所有的aop，不管bean是否循环依赖，都会在实例化后创建proxy，其中正常的Bean应该遵循生命周期在初始化的时候创建动态代理。只有出现循环依赖的时候才会在属性赋值的时候创建动态代理）
  - 会在ABA（第二次getBean(A)才会去调用三级缓存（如果实现了aop才会创建动态代理，如果没有实现依然返回的是Bean的实例））
  - 将三级缓存创建的对象放入二级缓存（目的是避免重复创建）



![image-20240322013501775](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220135918.png)

![image-20240322014311135](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202403220143186.png)

**夺命连环问**：

1、二级缓存能不能解决循环依赖的问题？

- 如果只是解决死循环的问题，使用一级缓存就可以解决，因为可以在实例化后立马创建一个动态代理存入到一级缓存，解决了死循环出口的问题。但是无法避免在并发情况下获取不完整的Bean
- 二级缓存也是可以解决循环依赖，只不过如果出现重复循环依赖会多次创建AOP

2、spring有没有解决多礼bean的循环依赖？

- 多例不会使用缓存进行存储（多例Bean每次使用都需要重新创建）
- 不缓存早期对象就无法解决循环

3、spring有没有解决构造函数参数Bean的循环依赖

- 构造函数的循环依赖也是会报错的
- 可以通过人工解决：@Lazy（**在构造方法上加@Lazy**）
  - 就不会立即创建依赖的bean
  - 而是等到用的时候才通过动态代理进行创建

4、正常的bean都在初始化以后创建动态代理，只有在出现循环依赖的时候，才会在实例化的时候创建动态代理



# 30、Spring事务失效的原因

> 使用Spring的@Transactional注解控制事务有哪些不生效的场景

**1、数据库引擎不支持事务**

**2、没有被spring容器管理**：Spring的事务管理需要在Spring容器中配置的Bean上才能生效。如果目标类没有被配置为Spring Bean，那么事务将无法被应用。解决方法是确保目标类被正确配置为Spring Bean。

**3、方法不是public**，Spring的事务代理通常是通过Java动态代理或CGLIB动态代理生成的，这些代理要求目标方法是公开可访问的（public）。私有方法无法被代理，因此事务将无效。解决方法是将目标方法改为public或protected。

**4、自己捕获了异常**，**解决：不要捕获处理：** Spring事务管理通常依赖于抛出**未捕获的运行时异常**来触发事务回滚。如果您在方法内部捕获了异常并处理了它，事务将不会回滚。解决方法是让异常在方法内部被抛出，以触发事务回滚

**5、数据源没有配置事务管理器**

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource) {
    return new DataSourceTransactionManager(dataSource);
}
```

如上面所示，当前数据源若没有配置事务管理器，那也是白搭！

**6、不支持事务：**

来看下面这个例子：

```java
@Service
public class OrderServiceImpl implements OrderService {

    @Transactional
    public void update(Order order) {
        updateOrder(order);
    }

    @Transactional(propagation = Propagation.NOT_SUPPORTED)
    public void updateOrder(Order order) {
        // update order
    }

}
```

**Propagation.NOT_SUPPORTED：** 表示不以事务运行，当前若存在事务则挂起，详细的可以参考《[事务隔离级别和传播机制](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/RTEMPBB6AFmmdj0uw1SDsg)》这篇文章。

都主动不支持以事务方式运行了，那事务生效也是白搭！

**7、自身调用问题**

来看两个示例：

```text
@Service
public class OrderServiceImpl implements OrderService {

    public void update(Order order) {
        updateOrder(order);
    }

    @Transactional
    public void updateOrder(Order order) {
        // update order
    }

}
```

update方法上面没有加 `@Transactional` 注解，调用有 `@Transactional` 注解的 updateOrder 方法，updateOrder 方法上的事务管用吗？

再来看下面这个例子：

```text
@Service
public class OrderServiceImpl implements OrderService {

    @Transactional
    public void update(Order order) {
        updateOrder(order);
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void updateOrder(Order order) {
        // update order
    }

}
```

这次在 update 方法上加了 `@Transactional`，updateOrder 加了 `REQUIRES_NEW` 新开启一个事务，那么新开的事务管用么？

这两个例子的答案是：不管用！

因为它们发生了自身调用，就调该类自己的方法，而没有经过 Spring 的代理类，默认只有在外部调用事务才会生效，这也是老生常谈的经典问题了。

这个的解决方案之一就是在的类中注入自己，用注入的对象再调用另外一个方法，这个不太优雅，另外一个可行的方案可以参考《[Spring 如何在一个事务中开启另一个事务？](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/1TEBnmWynN4nwc6Q-oZfvw)》这篇文章。

**8、异常被吃了**

这个也是出现比较多的场景：

```java
// @Service
public class OrderServiceImpl implements OrderService {

    @Transactional
    public void updateOrder(Order order) {
        try {
            // update order
        } catch {

        }
    }

}
```

把异常吃了，然后又不抛出来，事务怎么回滚吧！

**9、异常类型错误**

上面的例子再抛出一个异常：

```java
// @Service
public class OrderServiceImpl implements OrderService {

    @Transactional
    public void updateOrder(Order order) {
        try {
            // update order
        } catch {
            throw new Exception("更新错误");
        }
    }

}
```

这样事务也是不生效的，因为默认回滚的是：RuntimeException，如果你想触发其他异常的回滚，需要在注解上配置一下，如：

```java
@Transactional(rollbackFor = Exception.class)
```

这个配置仅限于 `Throwable` 异常类及其子类。

**10、使用CGLIB动态代理，但@Transactional声明在接口上：** 默认情况下，Spring的事务代理使用基于接口的JDK动态代理。如果您将**@Transactional**注解声明在接口上，而目标类是使用CGLIB代理的，事务将不会生效。解决方法是将**@Transactional**注解移到目标类的方法上，或者配置Spring以使用CGLIB代理接口。

**11、跨越多个线程的事务管理,解决：使用编程式事务或分布式事务：** 如果您的应用程序在多个线程之间共享数据库连接和事务上下文，事务可能会失效，除非适当地配置事务传播属性。

**12、事务传播属性或捕获异常等属性设置不正确：** 事务传播属性定义了事务如何传播到嵌套方法或外部方法。如果事务传播属性设置不正确，可能会导致事务失效或不符合预期的行为。



# 31、什么情况下AOP会失效，怎么解决

**1、内部方法调用：**

如果在同一个类中的一个方法调用另一个方法，AOP通知可能不会触发，因为AOP通常是通过代理对象拦截外部方法调用的。解决方式是注入本类对象进行调用， 或者设置暴露当前代理对象到本地线程， 可以通过`AopContext.currentProxy()` 拿到当前正在调用的动态代理对象。

细节可参考：https://cloud.tencent.com/developer/article/1098903

**2、静态方法：**

AOP通常无法拦截静态方法的调用，因为静态方法不是通过对象调用的。解决方法是将静态方法调用替换为实例方法调用，或者考虑其他技术来实现横切关注点。

**3、AOP配置问题：**

错误的AOP配置可能导致通知不正确地应用于目标方法，或者在不希望的情况下应用。解决方法是仔细检查AOP配置，确保切点表达式和通知类型正确配置。

**4、代理问题：**

如果代理对象不正确地创建或配置，AOP通知可能无法生效。解决方法是调试底层源码确保代理对象正确创建，并且AOP通知能够拦截代理对象的方法调用。

# 32、JDK动态代理和CGLIB动态代理的区别

**1、从性能上**

JDK动态代理要求目标对象必须实现至少一个接口，因为它基于接口生成代理类。

CGLIB不依赖于目标对象是否实现接口，可以代理没有实现接口的类，它通过继承或者代理目标对象的父类来实现代理

**2、从创建代理的性能对比**

JDK动态代理通常比CGLIB动态代理创建速度快，因为它不需要创建字节码文件；而CGLIB创建动态代理比较慢，它需要生成字节码文件；

JDK代理生成的代理类较小，占用较少的内存，而CGLIB生成的代理类通常比较大，占用更多的内存。

**3、从调用时的性能**

JDK动态代理在方法调用时需要通过反射机制来调用对象，而CGLIB不需要反射直接调用对象，通常具有更高的方法调用性能，同时CGLIB无需进行类型转换

选择使用JDK动态代理还是CGLIB动态代理取决于具体需求。如果目标对象已经实现了接口，并且您更关注创建性能和内存占用，那么JDK动态代理可能是一个不错的选择。如果目标对象没有实现接口，或者您更关注方法调用性能，那么CGLIB动态代理可能更合适。综上所述，这两种代理方式各有优势，根据实际情况进行选择是明智的，  Spring默认情况如果目标类实现了接口用JDK代理否则用CGLIB。  而SpringBoot默认用CGLIB，所以用哪个问题都不大。



# 33、事务传播机制

多个务方法相互调用时，务如何在这些鲂法间传播，方法A是-个务的方法，方法A执行过程中调用了方法B,那么方法B有无务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，同时方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。
1. REQUIRED(Spring默认的事务传播类型):如果当前没有事务，则自己新建一个务,如果当前存在事务,则加入这个事务
2. SUPPORTS:当前存在事務,则加入当前事务，如果当前没有事务,就以非事务方法执行
3. MANDATORY:当前存在事务，则加入当前事务,如果当前事务不存在，则抛出异常。
4. REQUIRES_ _NEW:创建一个新事务, 如果存在当前事務，则挂起该事务。
5. NOT_ _SUPPORTED:以非事务方式执行，如果当前存在務,则挂起当前事务
6. NEVER: 不使用事务,如果当前事务存在，则抛出异常
7. NESTED: 如果当前事务存在，则在嵌套事务中执行，否则REQUIRED的操作-样(开启一个事務)

详细参考：https://www.yuque.com/tulingzhouyu/sfx8p0/rbdxb6yzqvx59hoi#469447da