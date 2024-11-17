[toc]

## 1、框架整合

### 1.1、引入依赖

```maven
<!--shiro-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.9.0</version>
</dependency>
<!--mybatis-plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### 1.2、配置

```yaml
mybatis-plus:
  configuration: # 配置日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  mapper-locations: classpath:mapper/*.xml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?characterEncoding=utf-8&useSSL=false
    username: root
    password: admin
    driver-class-name: com.mysql.jdbc.Driver
    type: com.zaxxer.hikari.HikariDataSource
  jackson:
    date-format: yyyy-MM-dd HH:mm:ss
    time-zone: GMT+8
  main:
    allow-bean-definition-overriding: true

shiro:
  # 指定登录url的地址
  loginUrl: /myController/login
```

### 1.3、启动类

```java
@SpringBootApplication
@MapperScan("com.lqy.shirologin.shiro_springboot.mapper")
public class ShiroSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(ShiroSpringbootApplication.class, args);
    }

}
```



## 2、登录认证实现

### 2.1、数据查询Mapper

> 实体类**User**

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User implements Serializable {
    private static final long serialVersionUID = 876535807104587608L;
    private Integer id;
    private String name;
    private String pwd;
    private Integer rid;
}
```

> mapper——UserMapper

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
// 注意添加注解
}
```

> service——UserDemoService

```java
public interface UserDemoService {
    User getUserByName(String name);
}
```

> 实现类——UserDemoServiceImpl

```java
@Service // 注意添加注解
public class UserDemoServiceImpl implements UserDemoService {
    @Autowired
    private UserMapper userMapper;
    @Override
    public User getUserByName(String name) {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("name", name);
        User user = userMapper.selectOne(queryWrapper);
        return user;
    }
}
```

### 2.2、自定义Realm

```java
@Component
public class MyRealm extends AuthorizingRealm {
    @Autowired
    private UserDemoService userDemoService;
    // 自定义授权方法
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    // 自定义登录认证方法
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        // 获取用户身份信息
        String userName = authenticationToken.getPrincipal().toString();
        // 业务层获取用户信息，从数据库当中存的用户信息
        User userByName = userDemoService.getUserByName(userName);
        // 判断数据库中是否有这个用户信息，非空判断，将数据封装返回
        if (userName != null) {
            AuthenticationInfo info = new SimpleAuthenticationInfo(
                    authenticationToken.getPrincipal(),
                    userByName.getPwd(),
                    ByteSource.Util.bytes("abc"),
                    authenticationToken.getPrincipal().toString()
            );
            return info;
        }
        return null;
    }
}
```

注意：这里继承的是**AuthorizingRealm**，而不是**AuthenticatingRealm**，这是因为**AuthorizingRealm**已经继承了**AuthenticatingRealm**，并且还实现了**Authorizer**，**Initializable**，**PermissionResolverAware**，**RolePermissionResolverAware**。



### 2.3、添加ShiroConfig配置

首先，在pom文件中引入Shiro的依赖：

```xml
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.9.0</version>
</dependency>
```

其次，添加ShiroConfig配置（实际上是配置SecurityManager，安全管理器），并加上注解。

```java
@Configuration
public class ShiroConfig {
    @Autowired
    private MyRealm myRealm;

    // 配置SecurityManager
    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager(){
        // 1、创建defaultWebSecurityManager 对象
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        // 2、创建加密对象，设置相关属性
        HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
        // 2.1、采用md5加密
        hashedCredentialsMatcher.setHashAlgorithmName("md5");
        // 2.3、迭代加密次数
        hashedCredentialsMatcher.setHashIterations(3);
        // 3、将加密对象存储到MyRealm当中
        myRealm.setCredentialsMatcher(hashedCredentialsMatcher);
        // 4、将myRealm相关对象存入到defaultSecurityManager 对象
        manager.setRealm(myRealm);
        // 5、返回
        return manager;
    }

    // 配置Shiro内置过滤器的拦截范围
    @Bean
    DefaultShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();
        // 设置不认证可以访问的资源
        chainDefinition.addPathDefinition("/myController/userLogin", "anon");
        chainDefinition.addPathDefinition("/login", "anon");
        // 设置需要进行登录认证的拦截范围
        chainDefinition.addPathDefinition("/**", "authc");
        return chainDefinition;
    }
}
```



### 2.4、创建一个登录接口处理用户登录逻辑

```java
@Slf4j
@RestController
@RequestMapping("myController")
public class UserController {

    @GetMapping("userLogin")
    public String userLogin(String username, String password) {
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        SecurityUtils.setSecurityManager(securityManager);
        // 1、获取subject对象
        Subject subject = SecurityUtils.getSubject();
        // 2、封装请求数据到token
        AuthenticationToken token = new UsernamePasswordToken(username, password);
        try {
            // 3、调用login方法登录认证
            subject.login(token);
            return "登陆成功";
        } catch (AuthenticationException e) {
            e.printStackTrace();
            return "登陆失败";
        }
    }
}
```



## 3、多个realm的认证策略设置

> **多个realm的实现原理**

当应用程序配置多个Realm时，例如：用户名密码校验、手机号验证码校验等等。Shiro的ModularRealmAuthenticator会使用内部的AuthenticationStrategy组件判断认证是成功还是失败。

AuthenticationStrategy是一个无状态的组件，它在身份验证尝试中被询问4次（这4次交互所需要的任何必要的状态将被作为方法参数）：

（1）在所有realm被调用之前

（2）在调用Realm的getAuthenticationInfo方法之前

（3）在调用Realm的getAuthenticationInfo方法之后

（4）在所有Realm被调用之后

认证策略的另一项工作就是聚会所有Realm的结果信息封装至一个AuthenticationInfo实例中，并将此信息返回，以此作为Subject的身份信息。

Shiro中定义了3种认证策略的实现：

| AuthenticationStrategy class | 描述                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| AtLeastOneSuccessfulStrategy | 只要有一个（或更多）的Realm验证成功，那么认证将视为成功      |
| FirstSuccessfulStrategy      | 第一个Realm验证成功，整体认证将视为成功，且后续的Realm就会被忽略 |
| AllSuccessfulStrategy        | 所有的Realm成功，认证才会被人为成功                          |

ModularRealmAuthenticator内置的认证策略默认实现是AtLeastOneSuccessfulStrategy方式。可以通过配置修改策略。

```java
@Configuration
public class MultiShiroConfig {
    @Autowired
    private MyRealm myRealm;

    @Autowired
    private MyRealm myRealm1; // 注意：这里使用了同一个类来模拟两个不同的类，这里并不是规定它两是相同的类。

    public DefaultWebSecurityManager securityManager() {
        // 1、创建defaultWebSecurityManager对象
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        // 2、创建认证对象，并设置认证策略
        ModularRealmAuthenticator authenticator = new ModularRealmAuthenticator();
        authenticator.setAuthenticationStrategy(new AllSuccessfulStrategy());
        securityManager.setAuthenticator(authenticator);

        // 3、封装myRealm集合
        List<Realm> realms = new ArrayList<Realm>();
        realms.add(myRealm);
        realms.add(myRealm1);

        securityManager.setRealms(realms);

        return securityManager;
    }
}
```

这个逻辑和上面的单个realm的逻辑是一样的，注意理解。



## 4、remember me功能

比如访问一些网站，关闭了浏览器，下次再打开时还能知道你是谁，下次访问无需再次登录即可访问。

> **基本流程**

1、首先在登录页面选中Remenber me然后登陆成功；如果是浏览器登陆，一般会把Remenber Me的Cookie写到客户端并保存下来。

2、关闭浏览器再重新打开，会发现浏览器还是会记住你。

3、访问一般的网页服务器，仍然可以知道你是谁，且能正常访问。

4、但是如果我们访问电商平台时，如果我们要查会计我们的订单或者进行支付时，此时还是需要再进行身份认证的，以确保当前的用户还是你



> **代码实现**
>
> 注意：这里的代码在之前的代码`ShiroConfig`基础上添加了“设置rememberMe”的功能
>
> 添加了“创建Cookie”和“创建Shiro的cookie管理对象” 两个函数

```java
@Configuration
public class ShiroConfig {
    @Autowired
    private MyRealm myRealm;

    // 配置SecurityManager
    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager(){
        // 1、创建defaultWebSecurityManager 对象
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        // 2、创建加密对象，设置相关属性
        HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
        // 2.1、采用md5加密
        hashedCredentialsMatcher.setHashAlgorithmName("md5");
        // 2.3、迭代加密次数
        hashedCredentialsMatcher.setHashIterations(3);
        // 3、将加密对象存储到MyRealm当中
        myRealm.setCredentialsMatcher(hashedCredentialsMatcher);
        // 4、将myRealm相关对象存入到defaultSecurityManager 对象
        manager.setRealm(myRealm);
        // 4.5 设置rememberMe
        manager.setRememberMeManager(rememberMeManager());
        // 5、返回
        return manager;
    }

    // 配置Shiro内置过滤器的拦截范围
    @Bean
    DefaultShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();
        // 设置不认证可以访问的资源，anon表示所有url都可以匿名访问
        chainDefinition.addPathDefinition("/myController/userLogin", "anon");
        chainDefinition.addPathDefinition("/myController/login", "anon");
        // 设置登陆过滤器，logout表示登出
        chainDefinition.addPathDefinition("/logout", "logout");
        // 添加存在用户的过滤器(rememberMe)，user表示rememberMe之后就可以访问
        chainDefinition.addPathDefinition("/myController/userLoginRm", "user");
        // 设置需要进行登录认证的拦截范围，authc表示所有的url都必须通过认证才能访问
        chainDefinition.addPathDefinition("/**", "authc");
        return chainDefinition;
    }

    // Cookie设置
    public SimpleCookie rememberMe() {
        // 创建一个名为rememberMe的Cookie
        SimpleCookie cookie = new SimpleCookie("rememberMe");
        // 设置跨域：如果启用，允许此Cookie在特定的域下共享
        // cookie.setDomain(domain);
        // 设置Cookie的路径为根路径/，使其在该域下的所有路径都可用
        cookie.setPath("/");
        // 将Cookie设置为httpOnly，增强了安全性，防止JavaScript访问此Cookie，防范跨站脚本攻击。
        cookie.setHttpOnly(true);
        // 设置Cookie的最大存活时间，单位秒
        cookie.setMaxAge(30 * 24 * 60 * 60);
        return cookie;
    }

    // 创建Shiro的cookie管理对象
    public CookieRememberMeManager rememberMeManager() {
        CookieRememberMeManager manager = new CookieRememberMeManager();
        manager.setCookie(rememberMe());
        // 设置加密密钥CipherKey，用于加密“记住我”数据（注意这里的密钥过于简单，实际中应使用更复杂的密钥以确保安全性）
//        manager.setCipherKey("12345678987654321".getBytes());// 这个容易出现ASE加密出错，使用下面的方式
        manager.setCipherKey(Base64.encode("12345678987654321".getBytes()));
        return manager;
    }
}
```

> [!TIP]
>
> 这里对cookie.setHttpOnly(true)进行解释：
>
> 它是一种安全措施，它将Cookie标记为仅限HTTP请求访问，这就意味着：
>
> - **JavaScript无法访问该Cookie：**一旦Cookie设置为HttpOnly，客户端脚本（如JavaScript）将无法通过`document.cookie`访问该cookie。这就防止了恶意脚本窃取Cookie中存储的敏感信息，比如用户的登陆状态或“记住我”信息。
> - **防范跨站脚本攻击（XSS）：**XSS攻击通常利用受信网站中的漏洞，注入恶意JavaScript代码并执行，以窃取用户Cookie。然而，设置了`HttpOnly`标志的Cookie即使存在XSS漏洞，攻击者的脚本也无法读取Cookie信息，从而保护敏感数据。
> - **仅限服务器传递Cookie：**`HttpOnly`Cookie只能通过服务端的Http请求和响应传递。换句话说，只有服务器可以读取和修改该Cookie，客户端无法直接修改它。这增强了Cookie的安全性和完整性。
>
> 总结：`HttpOnly`属性通过阻止客户端脚本访问Cookie，显著提高了Cookie的安全性，是防范XSS攻击的一种常用手段，有助于保护用户隐私和敏感信息。

> [!warning]
>
> 在ShiroConfig的过滤链中，根据过滤链的定义，是从上向下执行，所以一般情况下，把 `/**` 放在最下面，这是一个坑，一不小心代码就会出错。同时尽量不要使用相同的url使用不同的过滤规则
>
> - anon表示所有url都可以匿名访问
> - authc表示所有的url都必须通过认证才能访问
> - user表示rememberMe之后就可以访问
> - logout表示登出的url可以访问

下面是controller：

```java
@Slf4j
@Controller
@RequestMapping("myController")
public class UserController {
    // 跳转登陆页面
    @GetMapping("login")
    public String login() {
        return "login";
    }

    @GetMapping("userLogin")
    public String userLogin(String username, String password, @RequestParam(defaultValue = "false") boolean rememberMe, HttpSession session) {
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        SecurityUtils.setSecurityManager(securityManager);
        // 1、获取subject对象
        Subject subject = SecurityUtils.getSubject();
        // 2、封装请求数据到token
        AuthenticationToken token = new UsernamePasswordToken(username, password, rememberMe);
        try {
            // 3、调用login方法登录认证
            subject.login(token);
            session.setAttribute("user", token.getPrincipal().toString());
            return "main";
        } catch (AuthenticationException e) {
            e.printStackTrace();
            System.out.println("登陆失败！");
            return "登陆失败";
        }
    }

    //登录验证rememberMe
    @GetMapping("userLoginRm")
    public String userLoginRm(HttpSession session) {
        session.setAttribute("user", "rememberMe");
        return "main";
    }
}
```

login页面：

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
    <title>Document</title>
</head>
<body>
<h1>Shiro登陆页面</h1>
<br>
<form action="/myController/userLogin">
    <div> 用户名：<input type="text" name="username" value=""></div>
    <div> 密码：<input type="password" name="password" value=""></div>
    <div> 记住我：<input type="checkbox" name="rememberMe" value="true"></div>
    <div> <input type="submit" value="登录"></div>
</form>
</body>
</html>
```

main页面：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录成功</title>
</head>
<body>
<h1>这是登陆成功的页面</h1>
<br>
登录用户为：<span th:text="${session.user}"></span>
<br>
<a href="/logout"> 登出 </a>
</body>
</html>
```

**注意：**

要使用th标签一定要引入依赖：

```maven
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

同时在配置文件application.yml里确保shiro的登录页面的url地址：

```yaml
shiro:
  # 指定登录url的地址
  loginUrl: /myController/login
```



## 5、授权、角色认证

### 5.1、授权

用户登录以后，需要验证是否具有指定角色指定权限。Shiro也提供了方便的工具进行判断。

这个工具就是Realm的doGetAuthenticationInfo方法进行判断。触发权限判断有两种方式：

（1）在页面中通过shiro:****属性判断

（2）在接口服务中通过注解@Requires****进行判断



### 5.2、后端接口服务注解

通过给接口服务方法添加注解可以实现权限校验，可以加载控制器方法上，也可以加在业务方法上，一般加在控制器方法上。常见注解如下：

（1）@RequiresAuthentication

验证用户是否登录，等同于方法subject.isAuthenticated()

（2）@RequiresUser

验证用户是否被记忆

登录认证成功subject.isAuthenticated()为true

登陆后被记忆subject.isRememberd()为true

（3）@RequiresGuest

验证是否是一个guest的请求，是否是游客的请求

此时subject.getPrincipal()为null

（4）@RequiresRoles

验证subject是否有相应的角色，有角色访问方法，没有则抛出异常AuthroizationException。

例如：@RequiresRoles("aRoleName")

​		void someMethod();

只有subject有aRoleName角色才能访问方法someMethod()

（5）@RequiresPermissions

验证subject是否有相应的权限，有权限则访问方法，没有则抛出异常Authorizationexception.

例如：@RequiresPermissions("file:read", "write:aFile.txt")

即subject必须同时具有file:read和write:aFile.txt权限才能访问方法someMethod()



### 5.3、授权验证—没有角色无法访问

> 建库建表

```mysql
/*
 Navicat Premium Data Transfer

 Source Server         : 本地
 Source Server Type    : MySQL
 Source Server Version : 50736
 Source Host           : localhost:3306
 Source Schema         : shirodb

 Target Server Type    : MySQL
 Target Server Version : 50736
 File Encoding         : 65001

 Date: 16/11/2024 20:06:02
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for role
-- ----------------------------
DROP TABLE IF EXISTS `role`;
CREATE TABLE `role`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `desc` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  `realname` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 3 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of role
-- ----------------------------
INSERT INTO `role` VALUES (1, 'admin', '所有权限', '管理员');
INSERT INTO `role` VALUES (2, 'userMag', '用户管理权限', '用户管理');

-- ----------------------------
-- Table structure for role_user
-- ----------------------------
DROP TABLE IF EXISTS `role_user`;
CREATE TABLE `role_user`  (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `uid` bigint(20) NULL DEFAULT NULL,
  `rid` bigint(20) NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 4 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of role_user
-- ----------------------------
INSERT INTO `role_user` VALUES (1, 1, 1);
INSERT INTO `role_user` VALUES (2, 1, 2);
INSERT INTO `role_user` VALUES (3, 2, 2);

-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user`  (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '编号',
  `name` varchar(30) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '用户名',
  `pwd` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '密码',
  `rid` bigint(20) NULL DEFAULT NULL COMMENT '角色编号',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 3 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of user
-- ----------------------------
INSERT INTO `user` VALUES (1, '张三', 'da22e1674b0cfc8eccf66e7414a1080f', NULL);
INSERT INTO `user` VALUES (2, '李四', 'da22e1674b0cfc8eccf66e7414a1080f', NULL);

SET FOREIGN_KEY_CHECKS = 1;
```

> 在mapper中添加sql语句

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
    @Select("select name from role where id in (select rid from role_user where uid=(select id from user where name=#{principal}))")
    List<String> getRolesInfoByUsername(@Param("principal") String principal);
}
```

> 在业务层service中添加接口

```java
public interface UserDemoService {
    User getUserByName(String name);
    // 根据用户查询角色信息
    List<String> getUserRolesInfo(String principal);
}
```

写实现方法：

```java
@Service
public class UserDemoServiceImpl implements UserDemoService {
    @Autowired
    private UserMapper userMapper;
    @Override
    public User getUserByName(String name) {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("name", name);
        User user = userMapper.selectOne(queryWrapper);
        return user;
    }

    @Override
    public List<String> getUserRolesInfo(String principal) {
        return userMapper.getRolesInfoByUsername(principal);
    }
}
```

> 完善自定义的realm中的授权方法

```java
@Component
public class MyRealm extends AuthorizingRealm {
    @Autowired
    private UserDemoService userDemoService;

    // 自定义授权方法:获取当前登陆用户的角色、权限信息，返回给shiro用来进行授权认证
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("自定义授权方法。");
        // 1、获取用户身份信息
        String username = (String) principalCollection.getPrimaryPrincipal();
        // 2、调用业务层获取用户的角色信息
        List<String> roles = userDemoService.getUserRolesInfo(username);
        System.out.println("当前用户角色信息： "+ roles);
        // 3、创建对象，封装当前登录用户的角色和权限信息
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        // 4、存储角色
//        simpleAuthorizationInfo.addRole("admin");
        simpleAuthorizationInfo.addRoles(roles);
        return simpleAuthorizationInfo;
    }

    // 自定义登录认证方法
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        // 获取用户身份信息
        String userName = authenticationToken.getPrincipal().toString();
        // 业务层获取用户信息，从数据库当中存的用户信息
        User user = userDemoService.getUserByName(userName);
        System.out.println("pwd: "+user.getPwd());
        // 判断数据库中是否有这个用户信息，非空判断，将数据封装返回
        if (user != null) {
            AuthenticationInfo info = new SimpleAuthenticationInfo(
                    authenticationToken.getPrincipal(),
                    user.getPwd(),
                    ByteSource.Util.bytes("abc"),
                    authenticationToken.getPrincipal().toString()
            );
            return info;
        }
        return null;
    }
}
```

### 5.3、授权认证—获取权限

> 建表

```mysql
create table if not exists `permission`(
	id bigint AUTO_INCREMENT primary key,
	name varchar(30),
	`info` varchar(30),
	`desc` varchar(50)
);
create table if not exists role_ps(
	id bigint not null AUTO_INCREMENT PRIMARY key,
	pid bigint,
	rid bigint
);

select info from permission where id in (select pid from role_ps where rid in (select id from role where name in ('admin', 'userMag')));
```

> 这个语句在mapper中的写法是这样的：

```java
@Repository
public interface UserMapper extends BaseMapper<User> {
    @Select("select name from role where id in (select rid from role_user where uid=(select id from user where name=#{principal}))")
    List<String> getRolesInfoByUsername(@Param("principal") String principal);

    @Select({
            "<script>",
            "select info from permission where id in ",
            "(select pid from role_ps where rid in (",
            "select id from role where name in ",
            "<foreach collection='roles' item='name' open='(' separator=',' close=')'>",
            "#{name}",
            "</foreach>",
            "))",
            "</script>"
    })
//    @Select("select info from permission where id in (1,2,3)")
    List<String> getUserPermissionInfoByRoles(@Param("roles") List<String> roles);
}
```

> 在业务层service中

```java
public interface UserDemoService {
    User getUserByName(String name);
    // 根据用户查询角色信息
    List<String> getUserRolesInfo(String principal);

    // 根据角色查询权限
    List<String> getUserPermissions(List<String> roles);
}
```

实现类：

```java
@Service
public class UserDemoServiceImpl implements UserDemoService {
    @Autowired
    private UserMapper userMapper;
    @Override
    public User getUserByName(String name) {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("name", name);
        User user = userMapper.selectOne(queryWrapper);
        return user;
    }

    @Override
    public List<String> getUserRolesInfo(String principal) {
        return userMapper.getRolesInfoByUsername(principal);
    }

    @Override
    public List<String> getUserPermissions(List<String> roles) {
        return userMapper.getUserPermissionInfoByRoles(roles);
    }
}
```

> 完善在自定义realm中的写法

```java
@Component
public class MyRealm extends AuthorizingRealm {
    @Autowired
    private UserDemoService userDemoService;

    // 自定义授权方法:获取当前登陆用户的角色、权限信息，返回给shiro用来进行授权认证
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("自定义授权方法。");
        // 1、获取用户身份信息
        String username = (String) principalCollection.getPrimaryPrincipal();
        // 2、调用业务层获取用户的角色信息
        List<String> roles = userDemoService.getUserRolesInfo(username);
        System.out.println("当前用户角色信息： "+ roles);
        // 2.5、获取用户的权限信息
        List<String> userPermissions = userDemoService.getUserPermissions(roles);
        System.out.println("当前用户的权限信息： "+userPermissions);
        // 3、创建对象，封装当前登录用户的角色和权限信息
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        // 4、存储角色
//        simpleAuthorizationInfo.addRole("admin");
        simpleAuthorizationInfo.addRoles(roles);
        simpleAuthorizationInfo.addStringPermissions(userPermissions);
        return simpleAuthorizationInfo;
    }

    // 自定义登录认证方法
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        // 获取用户身份信息
        String userName = authenticationToken.getPrincipal().toString();
        // 业务层获取用户信息，从数据库当中存的用户信息
        User user = userDemoService.getUserByName(userName);
        System.out.println("pwd: "+user.getPwd());
        // 判断数据库中是否有这个用户信息，非空判断，将数据封装返回
        if (user != null) {
            AuthenticationInfo info = new SimpleAuthenticationInfo(
                    authenticationToken.getPrincipal(),
                    user.getPwd(),
                    ByteSource.Util.bytes("abc"),
                    authenticationToken.getPrincipal().toString()
            );
            return info;
        }
        return null;
    }
}
```

> main页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录成功</title>
</head>
<body>
<h1>这是登陆成功的页面</h1>
<br>
登录用户为：<span th:text="${session.user}"></span>
<a href="/logout"> 登出 </a>
<br>
<a href="/myController/userLoginRoles"> 测试角色 </a>
<br>
<a href="/myController/userLoginPermissions"> 测试权限 </a>
</body>
</html>
```

### 5.4 代码仓库

[https://github.com/lqyspace/shiro-learning](https://github.com/lqyspace/shiro-learning)

### 5.5 登录异常处理

（1）创建认证异常处理类，使用@ControllerAdvice加@ExceptionHandler实现特殊异常处理。

```java
@ControllerAdvice
public class PermissionsException {
    // 抓一下特殊异常处理
    @ResponseBody
    @ExceptionHandler(value = UnauthorizedException.class)
    public String unauthenticated(UnauthorizedException e) {
        return "无权限";
    }

    @ResponseBody
    @ExceptionHandler(value = AuthorizationException.class)
    public String unauthenticated(AuthorizationException e) {
        return "权限认证失败";
    }
}
```

（2）让页面上没有权限的功能不显示

首先添加依赖：

```java
<!--配置Thymeleaf与Shiro的整合依赖-->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

其次，配置类添加新配置，用于解析thymeleaf中的shiro:相关属性

```java
// 配置类添加新配置，用于解析thymeleaf中的shiro:相关属性
@Bean
public ShiroDialect shiroDialect() {
    return new ShiroDialect();
}
```

然后，Thymeleaf中常用的shiro:属性

```
guest标签
<shiro:guest>
</shiro:guest>
用户没有身份验证时显示相应的信息，即游客访问信息。

user标签
<shiro:user>
</shiro:user>
用户已经身份验证/记住我登陆后显示相应的信息

authenticated标签
<shiro:authenticated>
</shiro:authenticated>
用户已经身份验证通过，即subjec.login登陆成功，不是记住我登陆的。

notAuthenticated标签
<shiro:notAuthenticated>
</shiro:notAuthenticated>
用户已经身份验证通过，既没有调用subjec.login进行登录，包括记住我自动登陆的也属于未进行身份验证。
这句话的意思就是包括了rememberMe的形式进行登录的情况。

principal标签
<shiro:principal/>
<shiro:principal property="username"/>
相当于((User)Subject.getPrincipals()).getUsername().

lacksPermission标签
<shiro:lacksPermission name="org:create">
</shiro:lacksPermission>
如果当前Subject没有权限将显示body体内容。

hasRole标签
<shiro:hasRole name='admin'>
</shiro:hasRole>
如果当前subject有角色将显示body体内容

hasAnyRoles标签
<shiro:hasAnyRoles name="admin, user">
</shiro:hasAnyRoles>
如果当前subject有任意一个角色（或的关系）将显示body内容

lacksRole标签
<shiro:lacksRole name="abc">
</shiro:lacksRole>
如果当前subject没有角色将显示body体内容

hasPermission标签
<shiro:hasPermission name="user:create">
</shiro:hasPermission>
如果当前Subject有权限将显示body体内容
```

改造的main.html：

```html
<!DOCTYPE html>
<html lang="en" xmlns:shiro="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>登录成功</title>
</head>
<body>
<h1>这是登陆成功的页面</h1>
<br>
登录用户为：<span th:text="${session.user}"></span>
<a href="/logout"> 登出 </a>
<br>
<a shiro:hasRole="admin" href="/myController/userLoginRoles"> 测试角色 </a>
<br>
<a shiro:hasPermission="user:delete" href="/myController/userLoginPermissions"> 测试权限 </a>
</body>
</html>
```



## 6、实现缓存

### 6.1、缓存工具EhCache

**EhCache**：是一个广泛使用的开源Java分布式缓存。主要面向通用缓存，Java EE和轻量级容器，可以和大部分Java项目无缝整合，例如：Hibernate中的缓存就是基于EhCache实现的。

**EhCache**支持内存和磁盘存储，默认存储在内容中，如果内存不够时把缓存数据同步到磁盘中。EhCache支持基于**Filter**的**Cache**实现，也支持**Gzip**压缩算法。

**EhCache**直接在JVM虚拟机中缓存，速度快，效率高。

**EhCache**缺点就是缓存共享太麻烦，集群分布式应用使用不方便



### 6.2、EhCache搭建使用

> **EhCache的Demo**

引入依赖：

```
<!--EhCache-->
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
    <version>2.6.11</version>
</dependency>
```

在resources新建ehcache.xml配置文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<ehcache>
    <!--磁盘的缓存位置-->
    <diskStore path="java.io.tmpdir/ehcache"/>
    <!--默认缓存-->
    <defaultCache
        maxEntriesLocalHeap="10000"
        eternal="false"
        timeToIdleSeconds="120"
        maxEntriesLocalDisk="1000000"
        diskExpiryThreadIntervalSeconds="120"
        memoryStoreEvictionPolicy="LRU">
        <persistence strategy="localTempSwap"/>
    </defaultCache>

    <!--helloworld缓存-->
    <cache name="HelloWorldCache"
        maxElementsInMemory="1000"
        eternal="false"
        timeToIdleSeconds="5"
        timeToLiveSeconds="5"
        overflowToDisk="false"
        memoryStoreEvictionPolicy="CPU"/>
</ehcache>
```

测试：

```java
public class TestEH {
    public static void main(String[] args) {
        // 获取编译目录下资源的流对象
        InputStream inputStream = TestEH.class.getClassLoader().getResourceAsStream("ehcache.xml");
        // 获取Ehcache的缓存管理对象
        CacheManager cacheManager = new CacheManager(inputStream);
        // 获取缓存对象
        Cache cache = cacheManager.getCache("HelloWorldCache");
        // 创建缓存数据
        Element element = new Element("name", "zhangsan");
        // 存入缓存
        cache.put(element);
        // 从缓存中取出数据输出
        Element name = cache.get("name");
        System.out.println("缓存中的数据 = " + name.getObjectValue());

    }
}
```

输出：

```java
缓存中的数据 = zhangsan
```

**如果在使用的过程中报错：Caused by: net.sf.ehcache.CacheException: Another unnamed CacheManager already exists in the same VM. Please provide unique names for each CacheManager in the config or do one of following:**

> [!note]
>
> Ehcache不允许创建同样名称的CacheManager对象。如果我们没有ehcache.xml中配置CacheManager的名称，那么默认的名称是`__DEFAULT__`。解决方式是`<ehcache name="">`中配置CacheManager的名称，并确保唯一。这样代码就不会报错。
>
> ![image-20241117000657826](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411170007011.png)

> **Shiro整合Ehcache**

Shiro官方提供了shiro-ehcache，实现了整合EhCache作为Shiro的缓存工具。可以缓存认证执行的Realm方法，减少对数据库的访问，提高认证效率。

```
<!--Shiro整合EhCache-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-ehcache</artifactId>
    <version>1.4.2</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.6</version>
</dependency>
```

完善ShiroConfig中的代码，添加获取缓存管理器部分代码：

```java
@Configuration
public class ShiroConfig {
    @Autowired
    private MyRealm myRealm;

    // 配置SecurityManager
    @Bean
    public DefaultWebSecurityManager defaultWebSecurityManager(){
        // 1、创建defaultWebSecurityManager 对象
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        // 2、创建加密对象，设置相关属性
        HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
        // 2.1、采用md5加密
        hashedCredentialsMatcher.setHashAlgorithmName("md5");
        // 2.3、迭代加密次数
        hashedCredentialsMatcher.setHashIterations(3);
        // 3、将加密对象存储到MyRealm当中
        myRealm.setCredentialsMatcher(hashedCredentialsMatcher);
        // 4、将myRealm相关对象存入到defaultSecurityManager 对象
        manager.setRealm(myRealm);
        // 4.5 设置rememberMe
        manager.setRememberMeManager(rememberMeManager());
        // 4.6 设置缓存管理器
        manager.setCacheManager(getEhCacheManager());
        // 5、返回
        return manager;
    }

    // 获取缓存管理器
    private EhCacheManager getEhCacheManager() {
        EhCacheManager ehCacheManager = new EhCacheManager();
        InputStream inputStream = null;
        try {
            inputStream = ShiroConfig.class.getClassLoader().getResourceAsStream("classpath:ehcache/ehcache.xml");
        } catch (Exception e) {
            e.printStackTrace();
        }
        CacheManager cacheManager = new CacheManager(inputStream);
        ehCacheManager.setCacheManager(cacheManager);

        return ehCacheManager;
    }

    // 配置Shiro内置过滤器的拦截范围
    @Bean
    DefaultShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();
        // 设置不认证可以访问的资源，anon表示所有url都可以匿名访问
        chainDefinition.addPathDefinition("/myController/userLogin", "anon");
        chainDefinition.addPathDefinition("/myController/login", "anon");
        // 设置登出过滤器，logout表示登出
        chainDefinition.addPathDefinition("/logout", "logout");
        // 添加存在用户的过滤器(rememberMe)，user表示rememberMe之后就可以访问
        chainDefinition.addPathDefinition("/myController/userLoginRm", "user");
        // 设置需要进行登录认证的拦截范围，authc表示所有的url都必须通过认证才能访问
        chainDefinition.addPathDefinition("/**", "authc");
        return chainDefinition;
    }

    // 配置类添加新配置，用于解析thymeleaf中的shiro:相关属性
    @Bean
    public ShiroDialect shiroDialect() {
        return new ShiroDialect();
    }

    // Cookie设置
    public SimpleCookie rememberMe() {
        // 创建一个名为rememberMe的Cookie
        SimpleCookie cookie = new SimpleCookie("rememberMe");
        // 设置跨域：如果启用，允许此Cookie在特定的域下共享
        // cookie.setDomain(domain);
        // 设置Cookie的路径为根路径/，使其在该域下的所有路径都可用
        cookie.setPath("/");
        // 将Cookie设置为httpOnly，增强了安全性，防止JavaScript访问此Cookie，防范跨站脚本攻击。
        cookie.setHttpOnly(true);
        // 设置Cookie的最大存活时间，单位秒
        cookie.setMaxAge(30 * 24 * 60 * 60);
        return cookie;
    }

    // 创建Shiro的cookie管理对象
    public CookieRememberMeManager rememberMeManager() {
        CookieRememberMeManager manager = new CookieRememberMeManager();
        manager.setCookie(rememberMe());
        // 设置加密密钥CipherKey，用于加密“记住我”数据（注意这里的密钥过于简单，实际中应使用更复杂的密钥以确保安全性）
//        manager.setCipherKey("12345678987654321".getBytes());
        manager.setCipherKey(Base64.encode("12345678987654321".getBytes()));
        return manager;
    }
}
```



## 7、会话管理

### 7.1、SessionManager

**会话管理器**，负责创建和管理用户的会话（Session）生命周期，它能够在任何环境中，在本地管理用户会话，即使没有Web/Servlet/EJB容器，也可以保存会话。默认情况下，Shiro会检测当前环境中现有的会话机制（比如Servlet容器）进行适配，如果没有，比如独立的应用程序或者非Web环境，它将会使用内部的企业会话管理器来提供相应的会话管理服务，其中还涉及一个名为SessionDAO的对象。SessionDAO负责Session的持久化操作（CRUD），允许Session数据写入到后端持久化数据库。



### 7.2、会话管理实现

SessionManager由SecurityManager管理。Shiro提供了三种实现：

![image-20241117005158040](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411170051194.png)

- DefaultSessionManager：用于JavaSE环境
- ServletContainerSessionManager：用于Web环境，直接使用Servlet容器的会话
- DefaultWebSessionManager：用于Web环境，自己维护会话（不使用Servlet容器的会话管理）

### 7.3、获得Session的方式

> **实现**

```java
Session session = SecurityUtils.getSubject().getSession();
session.setAttribute("key", "value");
```

> **说明**

Controller中的request，在shiro过滤器中的doFilterInternal方法，被包装成ShiroHttpServletRequest。

SecurityManager和SessionManager会话管理器决定session来源于ServletRequest还是由Shiro管理的会话。

无论是通过request.getSession或subject.getSession获取到session，操作session，两者都是等价的。
