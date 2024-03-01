# SpringBoot与Web开发

## SpringMVC快速使用

### 基于restful http接口的CURD

**假设这里有两个模块，分别是springmvc01和springmvc02：**

下面是springmvc01的项目代码：

`User.java`

```java
package com.lqy.springmvc01.entity;

public class User {
    private Integer id;
    private String username;
    private String address;

    public User() {
    }

    public User(Integer id, String username, String address) {
        this.id = id;
        this.username = username;
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", address='" + address + '\'' +
                '}';
    }

    public User(String username, String address) {
        this.username = username;
        this.address = address;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

`Result.java`

```java
package com.lqy.springmvc01.entity;

public class Result<T> {
    private Integer code;
    private String message;
    private T data;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public Result() {
    }

    public Result(T data) {
        this.data = data;
    }

    public Result(Integer code, String message, T data) {
        this.code = code;
        this.message = message;
        this.data = data;
    }

    public Result(Integer code, String message) {
        this.code = code;
        this.message = message;
    }

    @Override
    public String toString() {
        return "Result{" +
                "code=" + code +
                ", message='" + message + '\'' +
                ", data=" + data +
                '}';
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

`UserService.java`

```java
package com.lqy.springmvc01.service;

import com.lqy.springmvc01.entity.User;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@Service
public class UserService {
    private static Map<Integer, User> users = new HashMap<>();
    static {
        users.put(1, new User(1, "zhangsan", "北京"));
        users.put(2, new User(2, "lisi", "上海"));
        users.put(3, new User(3, "wangwu", "深圳"));
        users.put(4, new User(4, "xushu", "非洲"));
        users.put(5, new User(5, "zhuge", "塔寨村"));
    }

    public User getUserById(Integer id){
        return users.get(id);
    }

    public void add(User user){
        Integer newId = users.size() + 1;
        user.setId(newId);
        users.put(newId, user);
    }

    public void update(User user){
        users.replace(user.getId(), user);
    }

    public List<User> getAllUser(){
        return new ArrayList<>(users.values());
    }

    public void delete(Integer id){
        users.keySet().removeIf(key -> key == id);
    }
}
```

`UserController.java`

```java
package com.lqy.springmvc01.controller;

import com.lqy.springmvc01.entity.Result;
import com.lqy.springmvc01.entity.User;
import com.lqy.springmvc01.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/user")
public class UserController {
    @Autowired
    UserService userService;
    @GetMapping("/{id}")
    public Result getUser(@PathVariable Integer id){
        User userById = userService.getUserById(id);
        return new Result<>(200, "查询成功", userById);
    }

    @PostMapping("/add")
    public Result addUser(@RequestBody User user){// 可以接受json数据
        userService.add(user);
        return new Result<>(200, "添加成功", userService.getAllUser());
    }

    @PutMapping("/{id}")
    public Result editUser(@RequestBody User user){
        userService.update(user);
        return new Result<>(200, "修改成功", userService.getAllUser());
    }

    @DeleteMapping("/{id}")
    public Result deleteUser(@PathVariable Integer id){
        userService.delete(id);
        return new Result<>(200, "删除成功", userService.getAllUser());
    }
}
```

### 调用rest http接口

> **通过RestTemplate调用**

**RestTemplate**是spring提供的用于访问Rest服务的，RestTemplate提供了多种访问远程Http服务的，传统情况下在java代码里访问restful服务，一般使用Apache的HttpClient。不过此种方法使用起来太过繁琐。spring提供了一种简单便捷的模板类来操作，这是RestTemplate。

适用于微服务架构下 服务之间的远程调用   ps：以后使用微服务架构，spring cloud feign

**WebClient 都可以调用远程服务，区别：webclient依赖webflux，webclient请求远程服务是无阻塞的，响应的。**

**RestTemplate它是阻塞式的，需要等待请求响应后才能执行下一句代码**

示例：

此时就需要用上另一个项目springmvc02：

需要用上的类`User.java`，`Result.java`的类都一样

下面这个类是OrderController.java：

```java
package com.lqy.springmvc02.controller;

import com.lqy.springmvc02.entity.Result;
import com.lqy.springmvc02.entity.User;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpMethod;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class OrderController {
    // 声明了RestTemplate
    private final RestTemplate restTemplate;

    // 当bean 没有无参构造函数的时候，spring将自动拿到有参的构造函数，参数进行自动注入
    // 这个加入Autowired注解是一样的
    public OrderController(RestTemplateBuilder restTemplateBuilder) {
        this.restTemplate = restTemplateBuilder.build();
    }
    @RequestMapping("/order")
    public String order(){
        // 下单 需要远程访问rest服务

        // 基于restTemplate调用查询
        Result forObject = restTemplate.getForObject("http://localhost:8080/user/{id}", Result.class, 1);
        return forObject.toString();
    }

    // 基于restTemplate调用新增
    @RequestMapping("/orderAdd")
    public String addOrder(){
        User user = new User("徐庶", "上海");
        // url: 请求的远程rest url
        // object：post请求的参数
        // Class<T>: 返回的类型
        // ... Object: 是@PathVariable 占位符参数
        ResponseEntity<Result> resultResponseEntity = restTemplate.postForEntity("http://localhost:8080/user/add", user, Result.class);
        System.out.println(resultResponseEntity.toString());
        return resultResponseEntity.getBody().toString();
    }

    @RequestMapping("/putOrder")
    public String putOrder(){
        User user = new User(2,"李子豪", "上海");
//        restTemplate.put("http://localhost:8080/user/add", user, Result.class);
        HttpEntity<User> userHttpEntity = new HttpEntity<>(user);
        ResponseEntity<Result> exchange = restTemplate.exchange("http://localhost:8080/user/{id}", HttpMethod.PUT, userHttpEntity, Result.class, 2);
        System.out.println(exchange.toString());
        return exchange.getBody().toString();
    }

    @RequestMapping("deleteOrder")
    public String deleteOrder(){
        ResponseEntity<Result> exchange = restTemplate.exchange("http://localhost:8080/user/{id}", HttpMethod.DELETE, null, Result.class, 3);
        System.out.println(exchange.toString());
        return exchange.getBody().toString();
    }

}
```

请求方式说明：

| Delete                  | delete                                                       |
| ----------------------- | ------------------------------------------------------------ |
| Get                     | getForObject按照指定Class返回对象                            |
|                         | getForEntity返回对象为ResponseEntity对象，包含了响应中的一些重要信息，比如响应头、响应状态、响应体等 |
| HEAD                    | headForHeaders                                               |
| OPTIONS                 | optionsForAllow                                              |
| POST                    | postForAllow                                                 |
|                         | postForObject                                                |
| PUT                     | put                                                          |
| any支持任何请求方法类型 | exchange                                                     |
|                         | execute                                                      |

也可以在单元测试下使用：

```java
package com.lqy.springmvc02;

import com.lqy.springmvc02.entity.Result;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpMethod;
import org.springframework.http.ResponseEntity;

@SpringBootTest
class Springmvc02ApplicationTests {

    @Test
    void contextLoads() {
        TestRestTemplate testRestTemplate = new TestRestTemplate();
        ResponseEntity<Result> exchange = testRestTemplate.exchange("http://localhost:8080/user/{id}", HttpMethod.DELETE, null, Result.class, 1);
        System.out.println(exchange.toString());
    }

}
```

### 通过postman调用

![image-20240216230454651](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402162304714.png)

![image-20240216232118864](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402162321901.png)

### 通过MockMVC测试

MockMVC是由spring-test包提供的，实现了对http请求的模拟，能够直接使用网络的形式，转换到Controller的调用，不需要启动tomcat服务器。使得测试速度快，不依赖网络环境，同时提供了一套验证的工具，结果的验证十分方便。

确保依赖存在`junit5`和`spring-test`：

```xml
<!--starter-test依赖了junit5   spring-test-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

单元测试方法，构建一个测试类，添加注解`@SpringBootTest`和`@AutoConfigureMockMvc`：

```java
package com.lqy.springmvc01;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.lqy.springmvc01.entity.User;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;

@SpringBootTest
@AutoConfigureMockMvc // 专门用于做mockmvc的，由spring-test提供，依赖junit5，如果没有该注解需要通过代码构建mvc类
public class MockMVCTests {
    @Autowired
    MockMvc mockMvc;

    @Test
    void testMockMvcGet() throws Exception {
        // 不依赖web服务器，不依赖网络环境
        // 发起一个模拟请求
        mockMvc.perform(
                MockMvcRequestBuilders.get("/user/{id}", 1) // 发送get请求
                // 设置响应的文本类型
                        .accept(MediaType.APPLICATION_JSON_UTF8)
                        // .param()  ?name=xxx&age=xxx

        )
                // 响应断言
                .andExpect(MockMvcResultMatchers.status().isOk())// 断言状态码为200
//                .andExpect(MockMvcResultMatchers.content().string("Hello Tom!"))// 希望响应的内容
                .andExpect(MockMvcResultMatchers.jsonPath("$.data.username").value("zhangsan"))// $ 相当于json对象
                .andDo(MockMvcResultHandlers.print());
    }

    @Test
    void testMockMvcPost() throws Exception {
        User user = new User("杜甫", "苏州");
        ObjectMapper objectMapper = new ObjectMapper();
        String userJson = objectMapper.writeValueAsString(user);
        mockMvc.perform(
                MockMvcRequestBuilders.post("/user/add")
                        .accept(MediaType.APPLICATION_JSON_UTF8)// 设置响应的文本类型
                        .contentType(MediaType.APPLICATION_JSON_UTF8) // 设置请求的文本类型
                        .content(userJson) // json数据已经传好
        )
                // 响应断言
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andDo(MockMvcResultHandlers.print());
    }

    @Test
    void testMockMvcDelete() throws Exception {
        mockMvc.perform(
                MockMvcRequestBuilders.delete("/user/{id}", 1)
                        .accept(MediaType.APPLICATION_JSON_UTF8)

        )
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andDo(MockMvcResultHandlers.print());
    }

    @Test
    void testMockMvcPut() throws Exception {
        User user = new User(2, "李白和", "湖北");
        ObjectMapper objectMapper = new ObjectMapper();
        String userJson = objectMapper.writeValueAsString(user);

        mockMvc.perform(
                MockMvcRequestBuilders.put("/user/{id}", 2)
                        .accept(MediaType.APPLICATION_JSON_UTF8)
                        .contentType(MediaType.APPLICATION_JSON_UTF8)
                        .content(userJson)

        )
                .andExpect(MockMvcResultMatchers.status().isOk())
                .andDo(MockMvcResultHandlers.print());
    }
}
```

我们在测试编译的过程中，如果出现`Please refer to dump files (if any exist) [date].dump, [date]-jvmRun[N].dump and [date]`的报错，尝试如下的解决方法：

pom.xml配置：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin><!--编译跳过测试文件检查的生命周期-->
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <skip>true</skip>
            </configuration>
        </plugin>
    </plugins>
    <resources>
        <!--如果pro和xml文件放在源码java包下，也需要编译-->
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```

单元测试方法：

```java
/**
	1、mockMvc.perform执行一个请求
	2、MockMvcRequestBuilders.get("xxx")构造一个请求
	3、ResultActions.param()添加请求值
	4、ResultActions.accept(MediaType.TEXT_HTML_VALUE)设置返回类型
	5、ResultActions.andExcept添加执行完成后的断言
	6、ResultActions.andDo添加一个结果处理器，表示要对结果做什么事情，比如使用MockMvcResultHandlers.print()输出整个响应结果信息
	7、ResultActions.andReturn表示执行完成后返回相应的结果
*/

// 不依赖web服务器，不依赖网络环境
// 发起一个模拟请求
mockMvc.perform(
    MockMvcRequestBuilders.get("/user/{id}", 1) // 发送get请求
    // 设置响应的文本类型
    .accept(MediaType.APPLICATION_JSON_UTF8)
    // .param()  ?name=xxx&age=xxx

)
    // 响应断言
    .andExpect(MockMvcResultMatchers.status().isOk())// 断言状态码为200
    //                .andExpect(MockMvcResultMatchers.content().string("Hello Tom!"))// 希望响应的内容
    .andExpect(MockMvcResultMatchers.jsonPath("$.data.username").value("zhangsan"))// $ 相当于json对象
    .andDo(MockMvcResultHandlers.print());
```

测试结果打印：

```java
MockHttpServletResponse:
           Status = 200
    Error message = null
          Headers = [Content-Type:"application/json;charset=UTF-8"]
     Content type = application/json;charset=UTF-8
             Body = {"code":200,"message":"添加成功","data":[{"id":1,"username":"zhangsan","address":"北京"},{"id":2,"username":"lisi","address":"上海"},{"id":3,"username":"wangwu","address":"深圳"},{"id":4,"username":"xushu","address":"非洲"},{"id":5,"username":"zhuge","address":"塔寨村"},{"id":6,"username":"杜甫","address":"苏州"}]}
    Forwarded URL = null
   Redirected URL = null
          Cookies = []
```



## SpringMVC自动配置原理分析

SpringBoot为SpringMVC提供了自动配置，可与大多数应用程序完美的配合。

自动配置在Spring的默认值之上添加了以下功能：

- 包含`ContentNegotiatingViewResolver`和`BeanNameViewResolver`

  - `ViewResolver`都是SpringMvc内置的视图解析器

    - `ContentNegotiatingViewResolver`

      - 它并不会解析视图，而是委派给其他的视图解析器进行解析

        - 所有的视图解析器，都会根据返回的视图名称进行解析

        ```java
        @Override
        @Nullable
        public View resolveViewName(String viewName, Locale locale) throws Exception {
            RequestAttributes attrs = RequestContextHolder.getRequestAttributes();
            Assert.state(attrs instanceof ServletRequestAttributes, "No current ServletRequestAttributes");
            List<MediaType> requestedMediaTypes = getMediaTypes(((ServletRequestAttributes) attrs).getRequest());
            if (requestedMediaTypes != null) {
                // 获得所有匹配的视图
                List<View> candidateViews = getCandidateViews(viewName, locale, requestedMediaTypes);
                // 获取最终的视图
                View bestView = getBestView(candidateViews, requestedMediaTypes, attrs);
                if (bestView != null) {
                    return bestView;
                }
            }
        
            String mediaTypeInfo = (logger.isDebugEnabled() && requestedMediaTypes != null ?
                                    " given " + requestedMediaTypes.toString() : "");
        
            if (this.useNotAcceptableStatusCode) {
                if (logger.isDebugEnabled()) {
                    logger.debug("Using 406 NOT_ACCEPTABLE" + mediaTypeInfo);
                }
                return NOT_ACCEPTABLE_VIEW;
            }
            else {
                logger.debug("View remains unresolved" + mediaTypeInfo);
                return null;
            }
        }
        ```

        委派给其他的视图解析器进行解析：

        ```java
        @Override
        protected void initServletContext(ServletContext servletContext) {
            Collection<ViewResolver> matchingBeans =
                BeanFactoryUtils.beansOfTypeIncludingAncestors(obtainApplicationContext(), ViewResolver.class).values();
            if (this.viewResolvers == null) {
                this.viewResolvers = new ArrayList<>(matchingBeans.size());
                for (ViewResolver viewResolver : matchingBeans) {
                    if (this != viewResolver) {
                        this.viewResolvers.add(viewResolver);
                    }
                }
            }
            else {
                for (int i = 0; i < this.viewResolvers.size(); i++) {
                    ViewResolver vr = this.viewResolvers.get(i);
                    if (matchingBeans.contains(vr)) {
                        continue;
                    }
                    String name = vr.getClass().getName() + i;
                    obtainApplicationContext().getAutowireCapableBeanFactory().initializeBean(vr, name);
                }
        
            }
            AnnotationAwareOrderComparator.sort(this.viewResolvers);
            this.cnmFactoryBean.setServletContext(servletContext);
        }
        ```

        由以上代码可以得出结论，它是从Spring IOC容器获得ViewResolver类型的Bean，那么我们可以**定制**一个`ViewResolve`，`ContentNegotiatingViewResolver`也会帮我们委派解析

  - `BeanNameViewResolver`

    - 会根据handler方法返回的视图名称（xushu），去**ioc容器**中找到名字叫xushu的一个bean，并且这个bean要实现View接口

      示例：

      ```java
      @GetMapping("/test")
      public String getUser(){
          return "xushu";
      }
      ```

      可以配置一个名字叫xushu的视图。

      ```java
      @Component
      public class Xushu implements View {
          @Override
          public String getContentType() {
              return "text/html";
          }
      
          @Override
          public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
              response.getWriter().print("Welcome to XushuView");
          }
      }
      ```

      

- 支持提供静态资源，包括`WebJars`的支持（在本文档的后面部分中有介绍）。

  - 以前要访问jpg/css，js等，这些静态资源文件，需要在web.xml配置，在springboot中不需要配置，只需要放在约定的文件夹中即可（约定大于配置）

  - 在WebMvcAutoConfiguration.java类中，有一个`addResourceHandlers`方法，是用来处理静态资源的

    ![image-20240219215623370](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402192156433.png)

    Webjars：就是静态资源放在jar包中进行访问

    webjars官网：https://www.webjars.org/

    当访问/webjars/**时就会去classpath:/META-INF/resources/webjars/对应进行映射（当访问http://localhost:8080/webjars/jquery/3.5.1/jquery.js对应映射到/META-INF/resources/webjars/jquery/3.5.1/jquery.js）

    在webjars的官方网站中，选择Mevan的方式进行依赖导入

    ```xml
    <dependency>
        <groupId>org.webjars.npm</groupId>
        <artifactId>jquery</artifactId>
        <version>4.0.0-beta</version>
    </dependency>
    ```

    ![image-20240219220155890](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402192201958.png)

    ![image-20240219220419692](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402192204738.png)

  - 在static文件中访问的静态资源地址的原理：

    ![image-20240219224959427](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402192249479.png)

    `getStaticLocations`

      

    对应的映射地址：

    ![image-20240219225058256](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402192250296.png)

    **欢迎页配置：**

    在`WebMvcAutoConfiguration.java`的类文件中有一个函数叫 `getWelcomePage()`：

    ```java
    private Optional<Resource> getWelcomePage() {
        // 拿到上面静态资源地址
        String[] locations = getResourceLocations(this.resourceProperties.getStaticLocations());
        // 去里面找一个index.html的首页
        return Arrays.stream(locations).map(this::getIndexHtml).filter(this::isReadable).findFirst();
    }
    ```

    也可以通过配置文件指定具体的静态资源地址：

    ```xml
    spring.web.static-locations=classpath:/static/
    ```

- 自动注册`Converter`，`GeneriConverter`和`Formatter` Bean类

  - 使用方式可以参考SpringMvc课程中讲的

- 支持HttpMessageConverters

  - `HttpMessageConverters` 负责http请求和响应的报文处理

    ![image-20240220160217019](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402201602081.png)

- 自动注册`MessageCodesResolver`

  - 修改4XX错误下  格式转换出错  类型转换出错  的错误代码：

  - 以前的格式

  - errorCode+"."+object.name+"."+field

    - typeMismatch.user.birthday

  - 可以通过

    ```xml
    spring.mvc.message‐codes‐resolver‐format=postfix_error_code
    ```

    将格式修改为：object.name+"."+field+"."+errorCode

- 静态index.html支持

  - 在springboot中可以直接返回html视图

  - 因为自动`WebMvcAutoConfiguration`配置类中

    ```java
    @Bean
    @ConditionalOnMissingBean
    public InternalResourceViewResolver defaultViewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix(this.mvcProperties.getView().getPrefix());
        resolver.setSuffix(this.mvcProperties.getView().getSuffix());
        return resolver;
    }
    ```

    所以就可以通过在配置文件中完成

    ```xml
    spring.mvc.view.prefix=/pages/
    spring.mvc.view.suffix=.html
    ```

- 自动使用`ConfigurableWebBindingInitializer` Bean



## SpringMvc定制自动配置

1、在大多数的情况下，SpringBoot在自动配置中标记了很多的@ConditionalOnMissingBean(xxx.class)；意思就是说如果容器中没有这个bean，当前的@Bean才会生效。你只需要在自己的配置类中配置对应的一个@Bean就可以覆盖默认的自动配置。还的结合源码的实际功能进行定制。

2、通过`WebMvcConfigurer`进行扩展

- 扩展视图控制器
- 扩展拦截器
- 扩展全局CORS

```java
// 配置拦截器之前先写个拦截器的类
```

![image-20240220174558619](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402201745701.png)

```java
package com.lqy.springmvc03.interceptors;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.time.Duration;
import java.time.LocalDateTime;

/*
* 记录当前请求的拦截时间
* */
public class TimeInterceptor implements HandlerInterceptor {
    LocalDateTime begin;
    Logger logger = LoggerFactory.getLogger(TimeInterceptor.class);
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        begin = LocalDateTime.now();
        logger.info("当前请求："+request.getRequestURI()+"; 开始时间："+begin);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        LocalDateTime end = LocalDateTime.now();

        Duration between = Duration.between(begin, end);
        long l = between.toMillis();
        logger.info("当前请求："+request.getRequestURI()+"; 执行时间："+l+"毫秒");
    }
}
```

然后写一个自定义的`WebMvcConfigurer`的类：

```java
package com.tulingxueyuan.config;

import com.tulingxueyuan.controller.CustomErrorController;
import com.tulingxueyuan.interceptors.TimeInterceptor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.boot.autoconfigure.condition.SearchStrategy;
import org.springframework.boot.autoconfigure.web.servlet.WebMvcProperties;
import org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController;
import org.springframework.boot.autoconfigure.web.servlet.error.ErrorViewResolver;
import org.springframework.boot.web.servlet.error.ErrorAttributes;
import org.springframework.boot.web.servlet.error.ErrorController;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.config.annotation.*;
import org.springframework.web.servlet.i18n.*;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

import java.util.stream.Collectors;

/***
 * @Author 徐庶   QQ:1092002729
 * @Slogan 致敬大师，致敬未来的你
 */
//@EnableWebMvc    加了这个会使Springmvc自动配置失效
@Configuration
public class MyWebMvcConfigurer implements WebMvcConfigurer {
    /**
     * 添加视图控制器
     * 立即访问
     * <mvc:view-controller path="/" view-name="index" />
     *
     * @param registry
     */
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/tuling").setViewName("hello");
    }

    /**
     * 添加拦截器
     *
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new TimeInterceptor())     //添加拦截器
                .addPathPatterns("/**")    // 拦截映射规则
                .excludePathPatterns("/pages/**");  // 设置排除的映射规则

        // 添加国际化拦截器
        registry.addInterceptor(new LocaleChangeInterceptor())
                .addPathPatterns("/**");    // 拦截映射规则
    }

    /**
     * 全局CORS配置
     *
     * @param
     * @Override public void addCorsMappings(CorsRegistry registry) {
     * registry.addMapping("/user/*")   // 映射服务器中那些http接口运行跨域访问
     * .allowedOrigins("http://localhost:8081")     // 配置哪些来源有权限跨域
     * .allowedMethods("GET","POST","DELETE","PUT");   // 配置运行跨域访问的请求方法
     * }
     */


    @Bean
    public LocaleResolver localeResolver() {
        CookieLocaleResolver cookieLocaleResolver = new CookieLocaleResolver();
        // 可以设置过期时间
        cookieLocaleResolver.setCookieMaxAge(60 * 60 * 24 * 30);
        cookieLocaleResolver.setCookieName("locale");
        return cookieLocaleResolver;
    }
}
```

**WebMvcConfigurer原理**

实现WebMvcConfigurer接口可以扩展Mvc实现，又既保留SpringBoot的自动配置。

1、在WebMvcAutoConfiguration也有一个实现了WebMvcConfigurer接口的配置类：WebMvcAutoConfigurationAdapter

2、WebMvcAutoConfigurationAdapter它也是利用这种方式去扩展，所以我们通过查看这个类我们发现它帮助我们实现了其他的不常用的方法，帮助我们进行自动配置，我们只需要定制（拦截器，视图控制器，CORS 在开发中需要额外定制的功能）

```java
// Defined as a nested config to ensure WebMvcConfigurer is not read when not
// on the classpath
@Configuration(proxyBeanMethods = false)
@Import(EnableWebMvcConfiguration.class)
@EnableConfigurationProperties({ WebMvcProperties.class, WebProperties.class })
@Order(0)
public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer, ServletContextAware {
```

导入了EnableWebMvcConfiguration.class：`@Import(EnableWebMvcConfiguration.class)`

`EnableWebMvcConfiguration`它的父类`DelegatingWebMvcConfiguration`上`setConfigurers`使用了`Autowired`，它会去容器中将所有实现了WebMvcConfigurers接口的Bean都自动注入进来，添加到configurers变量中：

```java
@Configuration(proxyBeanMethods = false)
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {

	private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();


	@Autowired(required = false)
	public void setConfigurers(List<WebMvcConfigurer> configurers) {
		if (!CollectionUtils.isEmpty(configurers)) {
			this.configurers.addWebMvcConfigurers(configurers);
		}
	}
```

添加到delegates委派器中

```java
class WebMvcConfigurerComposite implements WebMvcConfigurer {

    private final List<WebMvcConfigurer> delegates = new ArrayList<>();


    public void addWebMvcConfigurers(List<WebMvcConfigurer> configurers) {
        if (!CollectionUtils.isEmpty(configurers)) {
            this.delegates.addAll(configurers);
        }
    }
```

**当添加了@EnableWebMvc就不会使用SpringMvc自动配置类的默认配置，默认配置就会失效。**

为什么呢？

在@EnableWebMvc中，@Import({DelegatingWebMvcConfiguration.class})

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({DelegatingWebMvcConfiguration.class})
public @interface EnableWebMvc {
}
```

在DelegatingWebMvcConfiguration.class中继承了

```java
@Configuration(proxyBeanMethods = false)
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
```

在WebMvcAutoConfiguration中，存在一个@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)，当容器中不存在WebMvcConfigurationSupport这个Bean的时候当前的自动配置才会生效。正因为通过@EnableWebMvc导入了WebMvcConfigurationSupport从而使自动配置类失效。

```java
@AutoConfiguration(after = { DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class,
		ValidationAutoConfiguration.class })
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass({ Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class })
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE + 10)
@ImportRuntimeHints(WebResourcesRuntimeHints.class)
public class WebMvcAutoConfiguration {
```

## Json开发

SpringBoot提供了三个与json映射库有关的集成：

- Gson
- Jackson 性能最好
- JSON-B

jsckson是我们使用的默认的json库。

### Jackson的使用

- @JsonIgnore 进行排除jsoin序列化，将它标注在属性上将不会进行json序列化
- @JsonFormat(pattern="yyyy-MM-dd hh:mm:ss", locale="zh") 进行日期格式化
- @JsonInclude(JsonInclude.Include.NON_NULL)  当属性值为null时则不进行json序列化
- @JsonProperty("uname") 来设置别名

```java
package com.tulingxueyuan.entity;

import com.fasterxml.jackson.annotation.JsonFormat;
import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonInclude;
import com.fasterxml.jackson.annotation.JsonProperty;
import io.swagger.annotations.ApiModelProperty;

import java.util.Date;

/***
 * @Author 徐庶   QQ:1092002729
 * @Slogan 致敬大师，致敬未来的你
 */
public class User {
    @JsonIgnore
    @ApiModelProperty(value = "用户id")
    private Integer id;
    @ApiModelProperty(value = "用户名")
    @JsonProperty("uname")
    private String username;
    @ApiModelProperty(value = "地址")
    private String address;

    @JsonFormat(pattern = "yyyy-MM-dd hh:mm:ss", locale = "zh")
    @JsonInclude(JsonInclude.Include.NON_NULL)
    private Date birthday;

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", address='" + address + '\'' +
                '}';
    }

    public User(Integer id, String username, String address) {
        this.id = id;
        this.username = username;
        this.address = address;
    }

    public User(String username, String address) {
        this.username = username;
        this.address = address;
    }

    public User(Integer id, String username, String address, Date birthday) {
        this.id = id;
        this.username = username;
        this.address = address;
        this.birthday = birthday;
    }

    public User() {
    }
}
```

**SpringBoot还提供了@JsonComponent来根据自己的业务需求进行json的序列化和反序列化。**

```java
package com.lqy.springmvc01.config;


import com.fasterxml.jackson.core.JsonGenerator;
import com.fasterxml.jackson.core.JsonParser;
import com.fasterxml.jackson.core.ObjectCodec;
import com.fasterxml.jackson.databind.DeserializationContext;
import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.JsonSerializer;
import com.fasterxml.jackson.databind.SerializerProvider;
import com.lqy.springmvc01.entity.User;
import org.springframework.boot.jackson.JsonComponent;
import org.springframework.boot.jackson.JsonObjectDeserializer;
import org.springframework.boot.jackson.JsonObjectSerializer;

import java.io.IOException;

@JsonComponent
public class UserJsonCust {
    public static class Serializer extends JsonObjectSerializer<User> {
        @Override
        protected void serializeObject(User user, JsonGenerator jgen, SerializerProvider provider) throws IOException {
            jgen.writeObjectField("id", user.getId());
            jgen.writeObjectField("uname", "xxxx");
            /*jgen.writeFieldName(""); // 单独写key
            jgen.writeObject("");// 单独写value
            // 1. 一次查不出完整的数据返回给客户端，需要根据需求去做一些个性化调整
            // 2. 根据不同的权限给他返回不同的序列化数据
            */

        }
    }

    public static class Deserializer extends JsonObjectDeserializer<User>{
        @Override
        protected User deserializeObject(JsonParser jsonParser, DeserializationContext context, ObjectCodec codec, JsonNode tree) throws IOException {
            User user = new User();
            // 由于在反序列化时，只反序列了id，所以反序列化以后并不会得到除id以外的其他的值
            user.setId(tree.findValue("id").asInt());
            return user;
        }
    }
}
```



### 国际化

SpringBoot：

- 添加国际化资源文件 resources

  ![image-20240220221644350](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402202216439.png)

- 配置messageResource  设置国际化资源文件

  - 在springboot提供了`MessageSourceAutoConfiguration`，所以，不需要我们去配置messageResource
  - 但是它并不生效：开启debug=true

![image-20240220222948817](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402202229879.png)

自动配置类没有生效的原因：

```java
@Configuration(
    proxyBeanMethods = false
)
// 如果自己配置了@Bean 名字叫messageSource的bean 就会用自定义的
@ConditionalOnMissingBean(
    name = {"messageSource"},
    search = SearchStrategy.CURRENT
)
@AutoConfigureOrder(Integer.MIN_VALUE)
// @Conditional 自定义条件匹配 会传入一个实现了Condition接口的类——ResourceBundleCondition
// ResourceBundleCondition会重写matches，自定义匹配规则，如果该方法返回true就匹配成功
@Conditional({ResourceBundleCondition.class})
@EnableConfigurationProperties
public class MessageSourceAutoConfiguration {
```

matches：

```java
public abstract class SpringBootCondition implements Condition {

	private final Log logger = LogFactory.getLog(getClass());

	@Override
	public final boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		String classOrMethodName = getClassOrMethodName(metadata);
		try {
            // 具体的匹配规则就在getMatchOutcome里面
			ConditionOutcome outcome = getMatchOutcome(context, metadata);
			logOutcome(classOrMethodName, outcome);
			recordEvaluation(context, classOrMethodName, outcome);
			return outcome.isMatch();
		}
		catch (NoClassDefFoundError ex) {
			throw new IllegalStateException("Could not evaluate condition on " + classOrMethodName + " due to "
					+ ex.getMessage() + " not found. Make sure your own configuration does not rely on "
					+ "that class. This can also happen if you are "
					+ "@ComponentScanning a springframework package (e.g. if you "
					+ "put a @ComponentScan in the default package by mistake)", ex);
		}
		catch (RuntimeException ex) {
			throw new IllegalStateException("Error processing condition on " + getName(metadata), ex);
		}
	}
```

getMatchOutcome：就在`MessageSourceAutoConfiguration`的子类`ResourceBundleCondition`中

只要在这个方法中将返回的`ConditionOutcome.match=true`那匹配成功

```java
@Override
public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
    // 获取配置文件中spring.messages.basename，如果没有配置 默认值：messages
    String basename = context.getEnvironment().getProperty("spring.messages.basename", "messages");
    ConditionOutcome outcome = cache.get(basename);
    if (outcome == null) {
        outcome = getMatchOutcomeForBasename(context, basename);
        cache.put(basename, outcome);
    }
    return outcome;
}
```

getMatchOutcomeForBasename：

```java
private ConditionOutcome getMatchOutcomeForBasename(ConditionContext context, String basename) {
    ConditionMessage.Builder message = ConditionMessage.forCondition("ResourceBundle");
    for (String name : StringUtils.commaDelimitedListToStringArray(StringUtils.trimAllWhitespace(basename))) {
        // 根据message获取 该类路径下的所有properties的资源文件
        for (Resource resource : getResources(context.getClassLoader(), name)) {
            // 这个条件非常关键，只要basename的类路径下有资源文件就会匹配成功
            if (resource.exists()) {
                return ConditionOutcome.match(message.found("bundle").items(resource));
            }
        }
    }
    return ConditionOutcome.noMatch(message.didNotFind("bundle with basename " + basename).atAll());
}
```

如果要让它生效，就必须保证在类路径下的message文件夹中有国际化的资源文件；或者自己配置spring.messages.basename告诉它资源文件在哪

```xml
spring.message.basename=i18n.message
```

只要找到了国际化的属性资源文件那就会设置`ConditionOutcome.match=true`

当`ConditionOutcome.match=true`那么就`@Conditional({ResourceBundleCondition.class})`就匹配成功。

一旦匹配成功那自动配置类就会生效，就会帮我们配置一个`messageSource`

```java
@Bean
public MessageSource messageSource(MessageSourceProperties properties) {
```





3、需要去解析请求头中的accept-language 或者 解析url参数中?local=

- 其实WebMvcAutoConfiguration类也帮我们配置了一个解析请求头的accept-language的localResolver

```java
@Override
@Bean
@ConditionalOnMissingBean(name = DispatcherServlet.LOCALE_RESOLVER_BEAN_NAME)
public LocaleResolver localeResolver() {
    // 当配置spring.web.locale-resolver=fiexed
    if (this.webProperties.getLocaleResolver() == WebProperties.LocaleResolver.FIXED) {
        // 就会使用配置文件中的本地化语言：spring.web.locale=en_US 就可以设死本地化语言
        return new FixedLocaleResolver(this.webProperties.getLocale());
    }
    // 默认就是使用AcceptHeaderLocaleResolver 作为本地化解析器
    AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
    //spring.web.locale=en_US 作为默认的本地化语言
    localeResolver.setDefaultLocale(this.webProperties.getLocale());
    return localeResolver;
}
```

![image-20240220230703292](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202402202307355.png)

在`AcceptHeaderLocaleResolver`类中：

```java
@Override
	public Locale resolveLocale(HttpServletRequest request) {
		Locale defaultLocale = getDefaultLocale();
        // 当获取的Accept-Language为null时，才会使用使用配置文件中设置的locale:spring.mvc.locale
		if (defaultLocale != null && request.getHeader("Accept-Language") == null) {
			return defaultLocale;
		}
        // 就是使用request.getLocale()
		Locale requestLocale = request.getLocale();
		List<Locale> supportedLocales = getSupportedLocales();
		if (supportedLocales.isEmpty() || supportedLocales.contains(requestLocale)) {
			return requestLocale;
		}
		Locale supportedLocale = findSupportedLocale(request, supportedLocales);
		if (supportedLocale != null) {
			return supportedLocale;
		}
		return (defaultLocale != null ? defaultLocale : requestLocale);
	}
```





4、随意切换本地语言，进行缓存

覆盖原有的localeResolver 因为自动配置类中的localeResolver它只会从accept-language中解析

5、通过messageResource 获取国际化信息

```java
1 1.第一种方法 在Handler方法参数中加入Locale参数，注入ResourceBundleMessageSource 对象
2 messageSource.getMessage(code, args,locale);
3
4 2.第二种方法 或者使用定义工具类：
5 package com.ns.utils;
6
7 import javax.servlet.http.HttpServletRequest;
8
9 import org.springframework.beans.factory.annotation.Autowired;
10 import org.springframework.context.support.ResourceBundleMessageSource;
11
12 /**
13 * 国际化帮助类
14 * @author Administrator
15 *
16 */
17 public class I18nMessageUtil {
18
19 		private static ResourceBundleMessageSource messageSource;
20
21 		private static HttpServletRequest request;
22
23 		/**
24 		* 获取国际化资源属性
25 		* @param code
26 		* @param args
27 		* @return
28 		*/
29 		public static String getMessage(String code,String... args){
30 			return messageSource.getMessage(code, args,request.getLocale());
31 		}
32 		@Autowired
33 		public void setMessageSource(ResourceBundleMessageSource messageSource) {
34 			this.messageSource = messageSource;
35 		}
    	@Autowired
37 		public void setRequest(HttpServletRequest request) {
38 			this.request = request;
39 		}
40
41 	}

```

