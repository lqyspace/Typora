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



### Connection接口

作用：Connection接口，具体的实现类由数据库厂商实现，代表一个连接对象。

功能：执行sql语句

| 方法                                           | 说明                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| Statement createStatement()                    | 创建一个Statement对象，用于将SQL语句发送到数据库             |
| PreparedStatement prepareStatement(String sql) | 创建一个PreparedStatement对象，用于将参数化SQL语句发送到数据库 |

功能：管理事务

| 方法                                   | 说明                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| void setAutoCommit(boolean autoCommit) | 将此连接的自动提交模式设置为给定状态；调用该方法将参数设置为false，即开启事务 |
| void commit()                          | 提交事务                                                     |
| void rollback()                        | 撤销事务                                                     |



### Statement接口

用于执行静态SQL语句并返回其生成的结果的对象

方法：

| 方法                               | 说明                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| boolean execute(String sql)        | 执行给定的SQL语句，该语句可能返回多个结果。                  |
| int executeUpdate(String sql)      | 执行给定的DML语句，这可能是insert、update或delete语句，返回影响的行数。或者不返回任何内容，如SQL DDL语句的SQL语句，返回0 |
| ResultSet executeQuery(String sql) | 执行给定的SQL语句，该语句返回单个ResultSet对象               |

练习：

插入练习

```java
public class JDBCDemo03 {
    public static void main(String[] args){
        Connection conn = null;
        Statement stat = null;
        try {
            // 注册
            Class.forName("com.mysql.jdbc.Driver");
            String sql = "insert into account(id, name, balance) values(null, '杜甫', 3000);";
            // 创建连接
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/zz?characterEncoding=utf8&&useSSL=false", "root", "admin");
            // 获取Statement对象
            stat = conn.createStatement();
            // 执行SQL语句
            int count = stat.executeUpdate(sql);
            System.out.println(count);
            if (count>0)
                System.out.println("添加成功！");
            else
                System.out.println("添加失败！");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (conn!=null){
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (stat!=null){
                try {
                    stat.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

修改练习：

```java
public class JDBCDemo04 {
    public static void main(String[] args){
        Connection conn = null;
        Statement stat = null;
        // 注册
        try {
            Class.forName("com.mysql.jdbc.Driver");
            String sql = "update account set balance = 1200 where id = 1;";
            Properties pro = new Properties();
            BufferedReader br = new BufferedReader(new FileReader("D:\\IdeaIU2023\\workspace\\learnmysql\\day01\\src\\cn\\itcast\\jdbc\\pro.properties"));
            pro.load(br);
            // 创建连接
            conn = DriverManager.getConnection("jdbc:mysql:///zz?characterEncoding=utf8&&useSSL=false", pro);
            // 创建Statement
            stat = conn.createStatement();
            //执行sql
            int count = stat.executeUpdate(sql);
            if (count>0)
                System.out.println("更新成功");
            else
                System.out.println("更新失败");
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (FileNotFoundException e) {
            throw new RuntimeException(e);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            if (stat!=null){
                try {
                    stat.close();
                } catch (SQLException e) {
                    throw new RuntimeException(e);
                }
            }
            if (conn!=null){
                try {
                    conn.close();
                } catch (SQLException e) {
                    throw new RuntimeException(e);
                }
            }
        }
    }
}
```



### ResultSet接口

作用：封装数据库查询的结果集，对结果集进行遍历，取出每一条记录。

![image-20230710224204994](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307102242066.png)

接口中的方法：

| 方法                  | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| boolean next()        | 1)   游标向下移动<br />2）返回boolean类型，如果还有下一条记录，返回true，否则返回false |
| 数据类型 getXxx(参数) | 1）通过字段名，参数是String类型，返回不同的类型，如getInt("id")，getString("name")，getBoolean("gender")，getDate("birthday")<br />2）通过列号，参数是整数，从1开始。返回不同的类型。如getInt(1)，getString(2)，getBoolean(3)，getDate(4) |

![image-20230710225114358](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307102251487.png)

![image-20230710225130600](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202307102251675.png)

注：java.sql.Date、Time、Timestamp(时间戳)，三个共同父类是：java.util.Date

练习：

```java
public class JDBCDemo05 {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stat = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            String url = "jdbc:mysql:///zz?characterEncoding=utf8&&useSSL=false";
            Properties info = new Properties();
            BufferedReader br = new BufferedReader(new FileReader("D:\\IdeaIU2023\\workspace\\learnmysql\\day01\\src\\cn\\itcast\\jdbc\\pro.properties"));
            info.load(br);
            conn = DriverManager.getConnection(url, info);
            stat = conn.createStatement();
            ResultSet rs = stat.executeQuery("select * from account;");
            while (rs.next()){
                System.out.println("编号："+rs.getInt("id")+", 姓名："+rs.getString("name")+", 工资："+rs.getString("balance"));
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (stat!=null){
                try {
                    stat.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn!=null){
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

注：关于ResultSet接口中的注意事项：

- 如果光标在第一行之前，使用rs.getXxx()获取列值，报错：Before start of result set
- 如果光标在最后一行之后，使用rs.getXxx()获取列值，报错：After end of result set
- 使用完毕以后要关闭结果集ResultSet，再关闭Statement，再关闭Connection



小案例：

```java
public class JDBCDemo06 {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stat = null;
        ResultSet rs = null;
        ArrayList<Emp> arrs = new ArrayList<>();
        try {
            Class.forName("com.mysql.jdbc.Driver");
            String url = "jdbc:mysql:///zz?characterEncoding=utf8&&useSSL=false";
            Properties info = new Properties();
            BufferedReader br = new BufferedReader(new FileReader("D:\\IdeaIU2023\\workspace\\learnmysql\\day01\\src\\cn\\itcast\\jdbc\\pro.properties"));
            info.load(br);
            conn = DriverManager.getConnection(url, info);
            stat = conn.createStatement();
            rs = stat.executeQuery("select * from emp;");
            Emp emp = null;
            while (rs.next()){
                emp = new Emp();
                emp.setId(rs.getInt("id"));;
                emp.setName(rs.getString("name"));
                emp.setGender(rs.getString("gender"));
                emp.setSalary(rs.getDouble("salary"));
                emp.setJoin_date(rs.getDate("join_date"));
                emp.setDept_id(rs.getInt("dept_id"));
                arrs.add(emp);
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (rs!=null){
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (stat!=null){
                try {
                    stat.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn!=null){
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
        System.out.println(arrs);
    }
}
```



**抽取JDBC的工具类：**

```java
public class JDBCUtils {
    /**
     * 文件的读取，只需读取一次
     * 所以可以定义一个静态代码块
     */
    private static String url;
    private static String user;
    private static String password;
    private static String driver;

    static {// 静态代码块的异常只能处理不能抛
        try {
            // 获取src路径下的文件的方式——>ClassLoader类加载器
            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
            URL res = classLoader.getResource("cn/itcast/jdbc/pro.properties");// 它是以src为相对路径的
            String path = res.getPath();
            System.out.println(path);

            Properties info = new Properties();
            info.load(new FileReader(path));
            url = info.getProperty("url");
            user = info.getProperty("user");
            password = info.getProperty("password");
            driver = info.getProperty("driver");
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取连接的静态方法
     * @return
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }

    /**
     * 释放资源
     */
    public static void close(Statement stat, Connection conn){
        if (stat!=null){
            try {
                stat.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    public static void close(ResultSet rs, Statement stat, Connection conn){
        if (rs!=null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (stat!=null){
            try {
                stat.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class JDBCDemo08 {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stat = null;
        ResultSet rs = null;
        try {
            conn = JDBCUtils.getConnection();
            stat = conn.createStatement();
            String sql = "select * from emp;";
            rs = stat.executeQuery(sql);
            while (rs.next()){
                System.out.println(rs.getInt("id") + ", "+ rs.getString("name"));
            }

        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtils.close(rs,stat,conn);
        }
    }
}
```

登录练习：

```java
public class JDBCDemo09 {
    public static void main(String[] args) {
        // 1. 键盘录入
        Scanner sc = new Scanner(System.in);
        System.out.println("桥输入用户名:");
        String username = sc.nextLine();
        System.out.println("请输入密码：");
        String password = sc.nextLine();
        boolean flag = new JDBCDemo09().login(username, password);
        if (flag)
            System.out.println("登录成功");
        else
            System.out.println("登陆失败");
    }

    public boolean login(String username, String password){
        if (username == null || password == null)
            return false;
        Connection conn = null;
        Statement stat = null;
        ResultSet rs = null;
        try {
            //获取连结
            conn = JDBCUtils.getConnection();
            stat = conn.createStatement();
            rs = stat.executeQuery("select * from learn where username='"+username+"' and password='"+password+"'");
            return rs.next();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtils.close(rs, stat, conn);
        }
    }
}
```

注意：这段代码存在**SQL注入**的问题，如果我将密码输入为 `a' or 'a'='a`，那么得到的SQL语句就是恒等式。

SQL注入：在拼接SQL时，有一些SQL的特殊关键词参与字符串的拼接，会造成安全性问题。

解决SQL注入：使用 `PreparedStatement`对象来解决。



### PreparedStatement接口

表示预编译SQL语句的对象，参数使用占位符 `?` 进行替代。

`PreparedStatement`接口是 `Statement`接口的子接口，继承父类中的所有方法，它是一个预编译的SQL语句。

---

`Statement`对象执行一条SQL语句都会先将SQL语句发送给数据库，数据库预先编译SQL，再执行。

如果有一万条类似的SQL语句，数据库需要编译1万次，执行一万次，效率低。

---

`prepareStatement`会先将SQL语句发送给数据库预编译，`PreparedStatement`会引用预编译后的结果，可以多次传入不同的参数给 `PreparedStatement`对象并执行。如果有一万条类似的插入数据的语句，数据库只需要预编译一次，传入1万次不同的参数并执行，减少了SQL语句的编译次数，提高了执行效率。

---

1、因为有预编译的功能，提高了SQL的执行效率。

2、可以有效地防止SQL注入的问题，安全性更高。

---

Connection创建 `PreparedStatement`对象：

| 方法                                           | 说明                                                        |
| ---------------------------------------------- | ----------------------------------------------------------- |
| PreparedStatement prepareStatement(String sql) | 指定预编译的SQL语句，SQL语句中使用占位符?，创建一个语句对象 |

---

PreparedStatement接口中的方法：

| 方法                     | 说明                                  |
| ------------------------ | ------------------------------------- |
| int executeUpdate()      | 执行DML，增删改的操作，返回影响的行数 |
| ResultSet executeQuery() | 执行DQL，查询的操作，返回结果集       |

---

使用步骤：

- 导入驱动
- 注册驱动
- 获取数据库连接对象 Connection
- 定义SQL
  - 注意：SQL的参数使用 ？ 作为占位符。如：select * from stu where username = ? and password  = ?;
- 获取执行SQL语句的对象PreparedStatement：Connection.prepareStatement(String sql)
- 给`?`赋值：
  - 方法：PreparedStatement.setXxx(参数1，参数2)
  - 参数1：`?` 的位置编号从1开始
  - 参数2：`?` 的值
- 执行SQL，接受返回的结果，不需要传递sql语句
  - PreparedStatement.executeQuery()
  - PreparedStatement.executeUpdate()
- 处理结果
- 释放资源

代码实例：

```java
public boolean login2(String username, String password){
        if (username == null || password == null)
            return false;
        Connection conn = null;
        PreparedStatement pstat = null;
        ResultSet rs = null;
        try {
            //获取连结
            conn = JDBCUtils.getConnection();
            pstat = conn.prepareStatement("select * from learn where username=? and password=?");
            // 给？赋值
            pstat.setString(1, username);
            pstat.setString(2, password);
            rs = pstat.executeQuery();
            return rs.next();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            JDBCUtils.close(rs, pstat, conn);
        }
    }
```

注意：后期使用 `PreparedStatement`完成数据库的增删改查操作

1、可以防止SQL注入

2、效率更高



## JDBC控制事务

1、事务：一个事务包含多个业务操作，如果这个业务操作被事务管理，则这多个步骤要么同时成功，要么一起失败。

2、操作：

 1. 开启事务

    在sql执行之前开启事务

 2. 提交事务

    当所有SQL都执行完提交事务

 3. 回滚事务

    在catch中回滚事务

3、使用Connection接口管理实务

| 方法                                   | 说明                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| void setAutoCommit(boolean autoCommit) | 将此连接的自动提交模式设置为给定状态；调用该方法将参数设置为false，即开启事务 |
| void commit()                          | 提交事务                                                     |
| void rollback()                        | 撤销事务                                                     |

4、演示：

```java
/*
* 事务操作
* */
public class JDBCDemo10 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement psta = null;

        try {
            conn = JDBCUtils.getConnection();
            // 开启事务
            conn.setAutoCommit(false);
            // 转账
            psta = conn.prepareStatement("update account set balance = balance + ? where name=?");
            psta.setInt(1, -500);
            psta.setString(2, "张三");
            psta.executeUpdate();
            psta.setInt(1, 500);
            psta.setString(2, "王五");

            int i=3/0;//手动制造异常
            psta.executeUpdate();
            // 提交事务
            conn.commit();
        } catch (Exception e) {
            // 有异常发生，进行回滚操作
            if (conn!=null){
                try {
                    conn.rollback();
                    System.out.println("转账失败");
                } catch (SQLException ex) {
                    e.printStackTrace();
                }
            }
            e.printStackTrace();
        } finally {
            JDBCUtils.close(psta, conn);
        }
    }
}
```





























