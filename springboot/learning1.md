[TOC]

# Springboot2.1

> 教学文档

[SpringBoot2 · 语雀](https://www.yuque.com/atguigu/springboot/lcfeme)

> 教学源码地址

[SpringBoot2核心技术与响应式编程: SpringBoot2核心技术与响应式编程](https://gitee.com/leifengyang/springboot2)


## 相关注解

@Controller：控制器的作用
@ResponseBody：将函数或者类的结果直接返回给浏览器，如果不进行页面跳转的话

以上两项的作用整合到一起：
@RestController：直接将此注解放在响应的函数或者类上

## 简化项目部署
1、在pom.xml里面的 project下添加：

    <packaging>jar</packaging> 直接打成jar

2、在dependencies下面添加：
```java
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

3、在idea里面右侧的Maven中，Lifecycle下的clean和package同时选中，然后执行，然后等待生成jar
4、到相应的目录下，在命令行里执行
```java
java -jar 打成的jar的名称
```