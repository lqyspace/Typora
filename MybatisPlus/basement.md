[TOC]
# MybatisPlus

## 介绍
只做增强，不作改变

### 代码以及文档发布地址
官方地址：[http://mp.baomidou.com](http://mp.baomidou.com)
代码发布地址：
GitHub：https://github.com/baomidou/mybatis-plus
Gitee：https://gitee.com/baomidou/mybatis-plus
文档发布地址：
[http://mp.baomidou.com/#/?id=%E7%AE%80%E4%BB%8B+](http://mp.baomidou.com/#/?id=%E7%AE%80%E4%BB%8B+)

### 配置
> pom.xml的配置

```java
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <!--            mvn的就近配置原则-->
    <version>5.1.49</version>
</dependency>
```

> application.yml 的配置

数据库的连接配置：
```java
server:
  port: 8082
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: admin
    driver-class-name: com.mysql.jdbc.Driver
```

### 小试牛刀
> javaBean

```java
package com.lqy.mp.bean;


import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    // 对应数据库中的主键
    private Long id;
    private String name;
    private Integer age;
    private String email;

}

```

> UserMapper
```java
package com.lqy.mp.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.lqy.mp.bean.User;
import org.springframework.stereotype.Repository;

@Repository // 代表持久层
public interface UserMapper extends BaseMapper<User> {
    /*
    * 继承BaseMapper基本类
    * 相当于已经把所有的CRUD的操作都已经编写完成
    * 不需要像以前那样编写大量的文件
    * */
}

```
**注意**:需要在主启动类上扫描mapper包下的所有的接口
```java
package com.lqy.mp;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.lqy.mp.mapper") // 扫描mapper文件夹
public class MybatisPlus01Application {

    public static void main(String[] args) {
        SpringApplication.run(MybatisPlus01Application.class, args);
    }

}

```

> 测试中的启动类

```java
package com.lqy.mp;

import com.lqy.mp.bean.User;
import com.lqy.mp.mapper.UserMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
class MybatisPlus01ApplicationTests {
    @Autowired
    private UserMapper userMapper;

    @Test
    void contextLoads() {
        System.out.println(("----- selectAll method test ------"));
        // 查询全部用户
        // 参数是一个Wrapper，条件构造器，这里不用的话使用null
        List<User> userList = userMapper.selectList(null);
        System.out.println("userlist.length:"+userList.size());
        userList.forEach(System.out::println);
    }

}


```

### 配置日志
> pom.xml 的日志配置

配置日志：
所有的sql语句的执行是不可见，但是配置日志，可以看见sql语句的具体查询过程。
```java
mybatis-plus:
  configuration: # 配置日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## CRUD操作

### insert操作
```java
@Test
public void testInsert(){
    User user = new User();
    user.setName("李明");
    user.setAge(23);
    user.setEmail("123452@qq.com");
    int result = userMapper.insert(user); // 帮我们在自动生成id
    System.out.println(result);// 受影响的行数
    System.out.println(user);// 发现，id会自动回填
}
```
![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200000941.png)

> 数据库插入的id的默认值为：全局的唯一id
#### 主键的生成策略
分布式系统的唯一id生成：[分布式系统唯一ID生成方案汇总 - nick hao - 博客园](https://www.cnblogs.com/haoxinyue/p/5208136.html
)

**雪花算法**:
snowflake是Twitter开源的分布式ID生成算法,结果是一个long型的ID。 其核心思想是:使用41 bit作为毫秒数, 10bit作为机器的
ID ( 5个bit是数据中心, 5个bit的机器ID ) , 12bit作为毫秒内的流水号(意味着每个节点在每毫秒可以产生4096个ID ) , 最后还
有一个符号位,永远是0。

#### 主键自增
我们需要配置主键自增：
1、实体类上字段：@TableId(type = IdType.AUTO)
2、数据库字段一定要自增。
3、再次插入即可。

#### 其他的源码解释
```java
public enum IdType {
    AUTO(0),// 数据库ID自增
    NONE(1),// 未设置主键
    INPUT(2),// 手动输入
    ASSIGN_ID(3), // 默认的全局唯一id
    ASSIGN_UUID(4); // 全局唯一id uuid

    private final int key;

    private IdType(int key) {
        this.key = key;
    }

    public int getKey() {
        return this.key;
    }
}
```

### update操作
```java
@Test
public void testUpdate(){
    User user = new User();
    // 通过条件自动拼接动态sql
    user.setId(6L);
    user.setName("关注我的种种好");
    user.setAge(18);
    int i = userMapper.updateById(user);
    System.out.println(i);

}
```
![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200000953.png)
![](D:/Boostnote%E7%AC%94%E8%AE%B0/img/mybatisplus/3.png)
所有的sql都是动态配置的。

#### 自动填充
创建时间，修改时间，这些操作都是自动完成的，我们不希望手动操作。
阿里巴巴开发手册：所有的数据库表：gmt_create，gmt_modified几乎所有的表都要配置上！而且需要自动化。

> 方式一：数据库级别（不建议：工作中不允许你修改数据库）

1、在表中新增字段create_time，update_time，类型为datetime
2、同步实体类：
```java
private Data createTime;
private Data updateTime;
```
3、运行即可

> 方式二：代码级别

1、数据库中有create_time,update_time两个字段。
2、加入注解：
```java
// 字段添加时填充内容
@TableField(fill = FieldFill.INSERT)
private Data createTime;

@TableField(fill = FieldFill.INSERT_UPDATE)
private Data updateTime;
```
3、自定义实现类 MyMetaObjectHandler：
```java
package com.lqy.mp.handler;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import lombok.extern.slf4j.Slf4j;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.util.Date;

@Slf4j
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    // 插入时的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill ......");
        // setFieldValByName(String fieldName, Object fieldVal, MetaObject metaObject)
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }

    // 更新时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill .......");
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}

```

4、测试插入，更新
```java
@Test
public void testInsert(){
    User user = new User();
    // 通过条件自动拼接sql
    user.setName("李明");
    user.setAge(23);
    user.setEmail("123452@qq.com");

    int result = userMapper.insert(user); // 帮我们在自动生成id
    System.out.println(result);// 受影响的行数
    System.out.println(user);// 发现，id会自动回填
}

@Test
public void testUpdate(){
    User user = new User();
    user.setId(6L);
    user.setName("关注我的种种好");
    user.setAge(20);
    int i = userMapper.updateById(user);
    System.out.println(i);

}
```

### 乐观锁
> 乐观锁：顾名思义十分乐观，他总是认为不会出现问题，无论干什么都不去上锁！如果出现问题，再次更新值测试
>
> 顾名思义十分悲观，他总是认为会出现问题，无论干什么都会上锁，再去操作！

当要更新一条记录的时候，希望这条记录没有被别人更新
乐观锁实现方式：
1、取出记录时，获取当前 version
2、更新时，带上这个 version
3、执行更新时， set version = newVersion where version = oldVersion
4、如果 version 不对，就更新失败

#### 乐观锁配置过程
1、给数据库中增加version字段，默认值为1
![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200000951.png)
2、同步实体类，同时在该字段上加上@version注解
```java
@Version// 乐观锁@version
private Integer version;
```
3、配置类：
```java
package com.lqy.mp.config;


import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.OptimisticLockerInnerInterceptor;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@MapperScan("com.lqy.mp.mapper") // 扫描mapper文件夹,从主启动类移动到此处
@Configuration
public class MyBatisPlusConfig {
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}

```
4、测试乐观锁
``` java
// 测试乐观锁成功
@Test
public void testOptimisticLocker(){
    // 1、查询用户信息
    User user = userMapper.selectById(8L);
    // 2、修改用户信息
    user.setName("冷全阳");
    user.setEmail("23141421@qq.com");
    // 3、执行更新操作
    userMapper.updateById(user);

}

// 测试乐观锁失败
@Test
public void testOptimisticLocker1(){
    // 线程1
    User user = userMapper.selectById(8L);
    user.setName("冷全阳1111");
    user.setEmail("23141421@qq.com");

    // 模拟另一个线程执行插队操作
    User user2 = userMapper.selectById(8L);
    user2.setName("冷全阳2222");
    user2.setEmail("23141421@qq.com");
    userMapper.updateById(user2); // 执行成功

    // 自旋锁来多次尝试提交！
    userMapper.updateById(user);//执行失败， 如果没有乐观锁就会覆盖插队线程的值！

}
```

### 查询操作
```java
// 测试单个查询
@Test
public void testSelectById(){
    User user = userMapper.selectById(8L);
    System.out.println(user);
}

// 测试批量查询
@Test
public void testSelectByBatchId(){
    List<User> users = userMapper.selectBatchIds(Arrays.asList(1,2,3));
    users.forEach(System.out::println);
}

// 条件查询之一使用 map 操作
@Test
public void testSelectByIds(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name","Tom");
    map.put("age",28);
    List<User> users = userMapper.selectByMap(map);
    users.forEach(System.out::println);
}
```

### 分页查询
1、使用limit进行分页
2、使用pageHelper第三方插件
3、MP其实也内置了分页插件

> 如何使用

1、配置拦截器组件即可
```java
//Spring boot方式
@Configuration
@MapperScan("com.baomidou.cloud.service.*.mapper*")
public class MybatisPlusConfig {

    // 旧版
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }

    // 最新版
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));
        return interceptor;
    }

}
```
2、直接使用Page对象即可！
```java
// 测试分页查询
@Test
public void testPage(){
    // 参数一：当前页面
    // 参数二：页面大小
    Page<User> page = new Page<>(1,5);
    userMapper.selectPage(page,null);
    page.getRecords().forEach(System.out::println);
    System.out.println(page.getTotal());
}
```

### 删除操作
基本的删除操作
```java
// 通过id删除
@Test
public void testDeleteById(){
    userMapper.deleteById(1484787348767027201L);
}


// 通过id批量删除
@Test
public void testDeleteBatchId(){
    userMapper.deleteBatchIds(Arrays.asList(1484787552991895553L,1484787582482112514L));
}

// 通过map删除
@Test
public void testDeleteByMap(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name",333);
    userMapper.deleteByMap(map);
}
```
### 逻辑删除
> 物理删除：从数据库中直接删除
>
> 逻辑删除：在数据库中并没有真正的删除，而是通过一个变量使它失效！deleted=0 => deleted=1

类似于回收站的功能：
1、在数据库中增加一个 deleted字段
![](D:/Boostnote%E7%AC%94%E8%AE%B0/img/mybatisplus/5.png)
2、同步实体类
```java
@TableLogic // 逻辑删除
private Integer deleted;
```
3、配置
步骤1：
> application.yml

```java
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: deleted # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)

```
步骤2：
```java
@TableLogic // 逻辑删除
private Integer deleted;
```
走的是更新操作，并不是删除操作。
![](D:/Boostnote%E7%AC%94%E8%AE%B0/img/mybatisplus/6.png)
再次查询时，无法查到：
![](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202205200000403.png)

### 条件查询器
测试1：
```java
@Test
void contextLoads() {
    // 查询name不为空的用户，并且邮箱不为空的用户，年龄大于等于12
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.isNotNull("name")
            .isNotNull("email")
            .le("age",12);
    userMapper.selectList(wrapper).forEach(System.out::println);
}
```

测试2：
```java
@Test
void test2(){
    // 查询名字
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.eq("name","Billie");
    User user = userMapper.selectOne(wrapper); // 查询一个数据，出现多个结果使用list或者map
    System.out.println(user);

}
```

测试3：
```java
@Test
void test3(){
    // 查询名字
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.between("age",20,30);
    Long count = userMapper.selectCount(wrapper); // 查询结果数量
    System.out.println(count);

}
```

测试4：记住查看输出的SQL语句进行分析
```java
@Test
void test4(){
    // 模糊查询
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    // 名字中不包含 e 的
    // likeLeft : %e     likeRight : e%    like: %e%
    wrapper.notLike("name","e")
            .likeRight("email","t");
    List<Map<String, Object>> maps = userMapper.selectMaps(wrapper);

    maps.forEach(System.out::println);

}
```

测试5：
```java
@Test
void test5(){
    // 模糊查询
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    // id 在子查询中查出来
    wrapper.inSql("id","select id from user where id<13");
    List<Object> objects = userMapper.selectObjs(wrapper);


    objects.forEach(System.out::println);

}
```

测试6：
```java
@Test
void test6(){
    // 模糊查询
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    // id 进行排序
    wrapper.orderByDesc("id");
    List<User> users = userMapper.selectList(wrapper);

    users.forEach(System.out::println);

}
```