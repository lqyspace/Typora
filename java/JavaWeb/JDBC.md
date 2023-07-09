# JDBC

## JDBC概念

Java DataBase Connectivity ——Java数据库连接，Java语言操作数据库。

JDBC：其实是官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。

使用JDBC的好处：

- 程序员如果要开发访问数据库的程序，只需要回调用JDBC接口中的方法即可，不用关注类是如何实现的。
- 使用同一套Java代码，进行少量的修改就可以访问其他JDBC支持的数据库。

![image-20230709230229069](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307092302130.png)

使用JDBC开发使用到的包：

![image-20230709231734518](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307092317555.png)

JDBC的核心API：

![image-20230709231833071](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307092318116.png)

快速入门：

- 导入驱动jar包
- 注册驱动
- 获取数据库连接对象 Connection
- 定义SQL
- 获取执行SQL语句对象的 Statement
- 执行SQL，接受返回结果
- 处理结果
- 释放资源

> **导入驱动jar包**

![image-20230709232255152](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307092322217.png)

> **加载和注册驱动**

| 加载和注册驱动的方法              | 描述                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| Class.forName(数据库驱动的实现类) | 加载和注册数据库驱动，数据库驱动由MySQL厂商 "com.mysql.jdbc.Driver" |

```java
public class JDBCDemo01 {
    /*
    * JDBC的快速入门
    * */
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1. 导入驱动jar
        // 2. 注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        // 3. 获取数据库连接对象
        Connection connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/zz?characterEncoding=utf8&&useSSL=false", "root", "admin");
        // 4. 定义SQL语句
        String sql = "update account set balance = 12120 where id = 1";
        // 5. 获取执行SQL语句的对象，Statement
        Statement statement = connection.createStatement();
        // 6. 执行SQL
        int count = statement.executeUpdate(sql);
        // 7. 处理结果
        System.out.println(count);
        // 8. 释放资源
        statement.close();
        connection.close();
    }
}
```

输出：

```java
1
```

注意：如果不添加 `useSSL`这个选项汇报如下警告：

```java
Sun Jul 09 23:44:16 CST 2023 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
```

如果将上面 `useSSL`改为 true，会报如下的错误，有空再去研究原因：

```java
严重: Exception starting filter JFinalFilter
java.lang.RuntimeException: Plugin start error: com.jfinal.plugin.activerecord.ActiveRecordPlugin. 
com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
 
The last packet successfully received from the server was 316 milliseconds ago.  The last packet sent successfully to the server was 309 milliseconds ago.
	at com.jfinal.core.Config.startPlugins(Config.java:116)
	at com.jfinal.core.Config.configJFinal(Config.java:51)
	at com.jfinal.core.JFinal.init(JFinal.java:63)
	at com.jfinal.core.JFinalFilter.init(JFinalFilter.java:49)
	at org.apache.catalina.core.ApplicationFilterConfig.initFilter(ApplicationFilterConfig.java:285)
	at org.apache.catalina.core.ApplicationFilterConfig.getFilter(ApplicationFilterConfig.java:266)
	at org.apache.catalina.core.ApplicationFilterConfig.<init>(ApplicationFilterConfig.java:108)
	at org.apache.catalina.core.StandardContext.filterStart(StandardContext.java:4747)
	at org.apache.catalina.core.StandardContext.startInternal(StandardContext.java:5389)
	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:145)
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1410)
	at org.apache.catalina.core.ContainerBase$StartChild.call(ContainerBase.java:1400)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at java.lang.Thread.run(Thread.java:748)
Caused by: com.jfinal.plugin.activerecord.ActiveRecordException: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
 
The last packet successfully received from the server was 316 milliseconds ago.  The last packet sent successfully to the server was 309 milliseconds ago.
	at com.jfinal.plugin.activerecord.TableBuilder.build(TableBuilder.java:55)
	at com.jfinal.plugin.activerecord.ActiveRecordPlugin.start(ActiveRecordPlugin.java:226)
	at com.jfinal.core.Config.startPlugins(Config.java:107)
	... 15 more
Caused by: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
 
The last packet successfully received from the server was 316 milliseconds ago.  The last packet sent successfully to the server was 309 milliseconds ago.
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at com.mysql.jdbc.Util.handleNewInstance(Util.java:425)
	at com.mysql.jdbc.SQLError.createCommunicationsException(SQLError.java:989)
	at com.mysql.jdbc.ExportControlled.transformSocketToSSLSocket(ExportControlled.java:203)
	at com.mysql.jdbc.MysqlIO.negotiateSSLConnection(MysqlIO.java:4901)
	at com.mysql.jdbc.MysqlIO.proceedHandshakeWithPluggableAuthentication(MysqlIO.java:1659)
	at com.mysql.jdbc.MysqlIO.doHandshake(MysqlIO.java:1226)
	at com.mysql.jdbc.ConnectionImpl.coreConnect(ConnectionImpl.java:2191)
	at com.mysql.jdbc.ConnectionImpl.connectOneTryOnly(ConnectionImpl.java:2222)
	at com.mysql.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:2017)
	at com.mysql.jdbc.ConnectionImpl.<init>(ConnectionImpl.java:779)
	at com.mysql.jdbc.JDBC4Connection.<init>(JDBC4Connection.java:47)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
	at com.mysql.jdbc.Util.handleNewInstance(Util.java:425)
	at com.mysql.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:389)
	at com.mysql.jdbc.NonRegisteringDriver.connect(NonRegisteringDriver.java:330)
	at com.alibaba.druid.pool.DruidAbstractDataSource.createPhysicalConnection(DruidAbstractDataSource.java:1461)
	at com.alibaba.druid.pool.DruidAbstractDataSource.createPhysicalConnection(DruidAbstractDataSource.java:1525)
	at com.alibaba.druid.pool.DruidDataSource.init(DruidDataSource.java:734)
	at com.alibaba.druid.pool.DruidDataSource.getConnection(DruidDataSource.java:1060)
	at com.alibaba.druid.pool.DruidDataSource.getConnection(DruidDataSource.java:1056)
	at com.alibaba.druid.pool.DruidDataSource.getConnection(DruidDataSource.java:104)
	at com.jfinal.plugin.activerecord.TableBuilder.build(TableBuilder.java:43)
	... 17 more
Caused by: javax.net.ssl.SSLHandshakeException: java.security.cert.CertificateException: java.security.cert.CertPathValidatorException: Path does not chain with any of the trust anchors
	at sun.security.ssl.Alerts.getSSLException(Alerts.java:192)
	at sun.security.ssl.SSLSocketImpl.fatal(SSLSocketImpl.java:1964)
	at sun.security.ssl.Handshaker.fatalSE(Handshaker.java:328)
	at sun.security.ssl.Handshaker.fatalSE(Handshaker.java:322)
	at sun.security.ssl.ClientHandshaker.serverCertificate(ClientHandshaker.java:1614)
	at sun.security.ssl.ClientHandshaker.processMessage(ClientHandshaker.java:216)
	at sun.security.ssl.Handshaker.processLoop(Handshaker.java:1052)
	at sun.security.ssl.Handshaker.process_record(Handshaker.java:987)
	at sun.security.ssl.SSLSocketImpl.readRecord(SSLSocketImpl.java:1072)
	at sun.security.ssl.SSLSocketImpl.performInitialHandshake(SSLSocketImpl.java:1385)
	at sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:1413)
	at sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:1397)
	at com.mysql.jdbc.ExportControlled.transformSocketToSSLSocket(ExportControlled.java:188)
	... 39 more
Caused by: java.security.cert.CertificateException: java.security.cert.CertPathValidatorException: Path does not chain with any of the trust anchors
	at com.mysql.jdbc.ExportControlled$X509TrustManagerWrapper.checkServerTrusted(ExportControlled.java:304)
	at sun.security.ssl.AbstractTrustManagerWrapper.checkServerTrusted(SSLContextImpl.java:985)
	at sun.security.ssl.ClientHandshaker.serverCertificate(ClientHandshaker.java:1596)
	... 47 more
Caused by: java.security.cert.CertPathValidatorException: Path does not chain with any of the trust anchors
	at sun.security.provider.certpath.PKIXCertPathValidator.validate(PKIXCertPathValidator.java:154)
	at sun.security.provider.certpath.PKIXCertPathValidator.engineValidate(PKIXCertPathValidator.java:80)
	at java.security.cert.CertPathValidator.validate(CertPathValidator.java:292)
	at com.mysql.jdbc.ExportControlled$X509TrustManagerWrapper.checkServerTrusted(ExportControlled.java:297)
	... 49 more
```



## 详解JDBC各个对象

---

1、DriverManager：驱动管理对象

2、Connection：数据库连接对象

3、Statement：执行SQL语句的对象

4、ResultSet：结果集对象

5、PreparedStatement：执行SQL对象，是Statement接口的子接口



### DriverManager类

> DriverManager作用

1、管理和注册驱动

```java
static void registerDriver(Driver driver) 使用 DriverManager注册给定的驱动程序。 

// 但是我们在注册驱动的时候为什么写下面这个代码呢
Class.forName("com.mysql.jdbc.Driver");
// 通过分析Driver的源码我们可以得知：
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    public Driver() throws SQLException {
    }

    static {
        try {
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
}
// 我们知道，Class.forName()会进行类加载，而且Driver里面有一个静态代码块，因此Driver里面的静态代码块就会被执行，也就因此执行了注册。
```

不过，就算我们不行 `Class.forName()`也是可以的，那是因为在MySQL驱动的jar包里面已经帮我们自动进行了类加载。如下图所示：

![image-20230710002158826](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307100021899.png)

**注意**：MySQL5之后的jar包可以自动帮你注册驱动，如果你忘记注册驱动，那么jar会自动先读取services里面的文件 `java.sql.Driver` ，然后根据 `com.mysql.jdbc.Driver`自动帮你注册驱动。



2、创建数据库的连接

> 类中的方法

| DriverManager类中的静态方法                                  | 描述                                               |
| ------------------------------------------------------------ | -------------------------------------------------- |
| Connection getConnection(String url, String user, String password) | 通过连接字符串，用户名，密码来得到数据库的连接对象 |
| Connection getConnection(String url, Properties info)        | 通过连接字符串，属性对象来得到连接对象             |

> 使用JDBC连接数据库的参数

| JDBC连接数据库的四个参数 | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 用户名                   | 登录的用户名                                                 |
| 密码                     | 登录的密码                                                   |
| 连接字符串URL            | 不同的数据库URL是不同的，MySQL的写法 jdbc:mysql://localhost:3306/数据库[?参数名=参数值] |
| 驱动类的字符串名         | com.mysql.jdbc.Driver                                        |

> 连接数据库的URL地址格式

```java
协议名:子协议://服务器名或ip地址:端口号/数据库名?参数=参数值&&参数=参数值
```

URL可以简写，前提是必须是本地服务器，端口号是3306

```java
jdbc:mysql:///数据库名?参数=参数值&&参数=参数值
```

> 乱码的处理

```java
jdbc:mysql://localhost:3306/数据库?characterEncoding=utf8
```

> 案例：得到MySQL的连接对象

```java
public class JDBCDemo02 {
    // 使用用户名，密码，URL得到连接对象
    public static void main(String[] args) throws SQLException {
        String url = "jdbc:mysql:///zz?characterEncoding=utf8&&useSSL=false";
        Connection connection = DriverManager.getConnection(url, "root", "admin");
        System.out.println(connection);//com.mysql.jdbc.JDBC4Connection@443b7951
        test();
    }
	// 使用属性文件和URL得到连接对象
    public static void test() throws SQLException {
        String url = "jdbc:mysql:///zz?characterEncoding=utf8&&useSSL=false";
        // 属性对象
        Properties info = new Properties();
        info.setProperty("user", "root");
        info.setProperty("password", "admin");
        Connection connection = DriverManager.getConnection(url, info);
        System.out.println(connection);//com.mysql.jdbc.JDBC4Connection@5b37e0d2
    }
}
```

