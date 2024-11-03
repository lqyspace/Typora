[toc]

## 1、是什么

Apache Shiro是一个功能强大且易于使用的Java安全权限框架。shiro可以完成：认证、授权、加密、会话管理、与Web集成、缓存等。借助shiro可以轻松地保护任何应用程序。

包括最小的移动应用程序到最大的Web和企业应用程序。



## 2、特点

- 易于使用
- 全面：满足安全需要的”一站式服务“
- 功能强大
- 灵活：可以在Web、EJB和Ioc环境中工作，但不需要依赖他们，Shiro没有任何规范、甚至没有依赖项
- 强力支持Web，支持各种协议
- 兼容性很强：Shiro的设计模式使其易于与其他的框架和应用程序继承，可以和Spring、Grails、Wicket、Tapestry、Mule、Apache Camel、Vaadin等框架无缝衔接
- 社区支持：Shiro是apache软件基金会的一个开源项目，有完备的社区支持。



## 3、Shiro与SpringSecurity的对比

1. Spring Security 是基于Spring开发的，项目如使用Spring作为基础，配合Spring Security做权限管理更加方便，而Shiro需要和Spring进行整合开发。
2. Spring Security功能比Shiro更加丰富，例如安全维护方面
3. Spring Security社区资源相比较Shiro更加丰富
4. Shiro的配置和使用比较简单，SpringSecurity上手比较复杂
5. Shiro的依赖性低，不需要任何框架和容器，可以独立运行；Spring Security依赖Spring容器。
6. shiro不仅仅可以使用在web中，它可以工作在任何应用环境中。在集群会话时Shiro最重要的一个好处就是他的会话是独立于容器的。



## 4、基本功能

![image-20241104003923589](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411040039650.png)

验证、授权、会话管理、密码学



## 5、Shiro架构（从外部角度看）

![image-20241104004652376](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411040046414.png)

Application Code：应用程序

Subject：**应用代码直接交互的对象就是 Subject**，也就是说Shiro的对外Api核心就是Subject。Subject代表了当前用户，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是subject，如网络爬虫、机器人等；与subject的所有交互都会委托给Securitymanager：Subject其实是一个门面，Securitymanager才是实际的执行者。

Shiro SecurityManager：安全管理器，即所有与安全有关的操作都会与SecurityManager交互，且其管理着所有的Subject；可以看出它是Shiro的核心，它负责与Shiro的其他组件进行交互，它相当于SpringMvc中的DispatcherServlet的角色。

Realm：Shiro从Realm获取安全数据（如用户，角色，权限等），就是说SecurityManager要验证用户身份，那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也就是从Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以把Realm看成DataSource。



## 6、Shiro架构（从内部角度看）

![image-20241104005708068](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411040057145.png)

1. Authenticator：负责 Subject认证，是一个扩展点，可以自定义实现；可以使用认证策略（Authenticator Strategy），即什么情况下认为用户验证通过了。
2. Authorizer：授权器，即访问控制器，用来决定主体是否有权限进行相应的操作；即控制用户能访问应用中的哪些功能；
3. Realm：可以有一个或多个Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是JDBC实现，也可以是内存实现等；由用户提供，所以一般在应用中都需要实现自己的Realm。
4. SessionManager：管理Session生命周期的组件；而Shiro并不仅仅可以用在Web环境，也可以用在如普通的JavaSe环境
5. CacheManager：缓存管理器，来管理如用户，角色，权限等的缓存；因为这些数据基本上很少改变。放到缓存中可以提高访问的性能。
6. Cryptography：密码模块，Shiro提高了一些常见的加密组件用于如密码加密/解密。

