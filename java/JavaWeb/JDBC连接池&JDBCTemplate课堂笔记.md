# 今日内容
	1. 数据库连接池
	
	2. Spring JDBC : JDBC Template



## 数据库连接池
```java
1. 概念：其实就是一个容器(集合)，存放数据库连接的容器。
	    当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

2. 好处：
	1. 节约资源
	2. 用户访问高效
		用户直接从连接池获取连接对象，自己不用去创建连接

3. 实现：
	1. 标准接口：DataSource   javax.sql包下的：一个连接到 这个DataSource所代表的物理数据源 的工厂
		1. 方法：
			* 获取连接：getConnection() 尝试与此DataSource对象表示的数据源建立连接
			* 归还连接：Connection.close()。如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接了。而是归还连接

	2. 一般我们不去实现它，有数据库厂商来实现
		1. C3P0：数据库连接池技术
		2. Druid：数据库连接池实现技术，由阿里巴巴提供的
```




```java
4. C3P0：数据库连接池技术
	* 步骤：
		1. 导入jar包 (两个) c3p0-0.9.5.2.jar mchange-commons-java-0.2.12.jar ，
			* 不要忘记导入数据库驱动jar包
		2. 定义配置文件：
			* 名称： c3p0.properties 或者 c3p0-config.xml（必须是这两个名，自动寻找这两个名字的文件）
			* 路径：直接将文件放在src目录下即可。

		3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
		4. 获取连接： getConnection
	* 代码：
		 //1.创建数据库连接池对象
        DataSource ds  = new ComboPooledDataSource();
        //2. 获取连接对象
        Connection conn = ds.getConnection();
        

5. Druid：数据库连接池实现技术，由阿里巴巴提供的
	1. 步骤：
		1. 导入jar包 druid-1.0.9.jar
		2. 定义配置文件：
			* 是properties形式的
			* 可以叫任意名称，可以放在任意目录下
		3. 加载配置文件: Properties
		4. 获取数据库连接池对象：通过工厂来来获取  DruidDataSourceFactory
		5. 获取连接：getConnection
	* 代码：
		 //3.加载配置文件
        Properties pro = new Properties();
        InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);
        //4.获取连接池对象
        DataSource ds = DruidDataSourceFactory.createDataSource(pro);
        //5.获取连接
        Connection conn = ds.getConnection();
	2. 定义工具类
		1. 定义一个类 JDBCUtils
		2. 提供静态代码块加载配置文件，初始化连接池对象
		3. 提供方法
			1. 获取连接方法：通过数据库连接池获取连接
			2. 释放资源
			3. 获取连接池的方法
```

`C3P0`配置文件：`c3p0-config.xml`名称必须是这个，自动寻找这个名字的配置文件：

```xml
<c3p0-config>
  <!-- 使用默认的配置读取连接池对象 -->
  <default-config>
  	<!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/zz?useSSL=false</property>
    <!--  这个地方仍然可以简写：jdbc:mysql:///zz?useSSL=false -->
    <property name="user">root</property>
    <property name="password">admin</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">10</property>
    <property name="checkoutTimeout">3000</property>
  </default-config>

  <named-config name="otherc3p0"> 
    <!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/zz</property>
    <property name="user">root</property>
    <property name="password">admin</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">8</property>
    <property name="checkoutTimeout">1000</property>
  </named-config>
</c3p0-config>
```

**验证c3p0数据库连接池的最大连接数：**

```java
public class C3P0Demo02 {
    public static void main(String[] args) throws SQLException {
        // 获取DataSource，使用默认配置
        DataSource ds = new ComboPooledDataSource();
        // 获取DataSource，使用指定名称配置
        // DataSource ds = new ComboPooledDataSource("otherc3p0");
        // 验证最大连接数
        for (int i=0; i<=10; i++){
            Connection conn = ds.getConnection();
            System.out.println(i+": "+conn);
            if (i==5) conn.close();
        }
    }
}
```

结论：1、`c3p0`数据库连接池支持同时连接声明的最大连接池数；2、如果连接数超过了连接池支持的最大的连接数，则会报错超时；3、归还的连接可以继续被其他对象使用



**druid数据库连接池：**

配置文件：

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/zz?useSSL=false
# url=jdbc:mysql:///zz?useSSL=false
username=root
password=admin
# 初始化连接数
initialSize=5
# 最大连接数
maxActive=10
# 最大等待时间
maxWait=3000
```

代码实例：

```java
public class DruidDemo01 {
    public static void main(String[] args) throws Exception {
        // 1. 导入jar
        // 2. 定义配置文件
        // 3. 加载配置文件
        Properties info = new Properties();
        InputStream is = DruidDemo01.class.getClassLoader().getResourceAsStream("druid.properties");
        info.load(is);
        // 4. 获取连接池对象
        DataSource ds = DruidDataSourceFactory.createDataSource(info);
        // 5. 获取连接
        Connection conn = ds.getConnection();
        System.out.println(conn);
    }
}
```

结论：1、`duid`配置文件可以放在任意目录下；2、`url`也可以省略：`url=jdbc:mysql:///zz?useSSL=false`

> `JDBCUtils`工具类


```java
public class JDBCUtils {
    // 1. 定义成员变量
    private static DataSource ds;

    static {
        try {
            // 1. 加载配置文件
            Properties info = new Properties();
            info.load(JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
            // 2. 获取DataSource
            ds = DruidDataSourceFactory.createDataSource(info);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 获取连接的方法
    public static Connection getConnection() throws SQLException {
        return ds.getConnection();
    }

    // 释放资源
    public static void close(Statement stat, Connection conn){
        close(null, stat, conn);
    }
    // 释放资源
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
                conn.close();// 归还连接
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    /*
    * 获取连接池
    * */
    public static DataSource getDataSource(){
        return ds;
    }
}
```

工具类的测试使用：

```java
public class DruidDemo02 {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement pstat = null;
        // 获取连接
        try {
            conn = JDBCUtils.getConnection();
            String sql = "insert into account values(null, ?, ?)";
            // 获取PreparedStatement对象
            pstat = conn.prepareStatement(sql);
            // 设置参数
            pstat.setString(1, "周瑜");
            pstat.setDouble(2, 3500);
            // 执行SQL
            int count = pstat.executeUpdate();
            if (count>0)
                System.out.println("插入成功！");
            else
                System.out.println("插入失败！");
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            JDBCUtils.close(pstat, conn);
        }
    }
}
```



## Spring JDBC
	* Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发
	* 步骤：
		1. 导入jar包
		2. 创建JdbcTemplate对象。依赖于数据源DataSource
			* JdbcTemplate template = new JdbcTemplate(ds);
	
		3. 调用JdbcTemplate的方法来完成CRUD的操作
			* update():执行DML语句。增、删、改语句
			* queryForMap():查询结果将结果集封装为map集合，将列名作为key，将值作为value 将这条记录封装为一个map集合
				* 注意：这个方法查询的结果集长度只能是1
			* queryForList():查询结果将结果集封装为list集合
				* 注意：将每一条记录封装为一个Map集合，再将Map集合装载到List集合中
			* query():查询结果，将结果封装为JavaBean对象
				* query的参数：RowMapper<类型>：这个类型为封装的结果的类型
					* 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装
					* new BeanPropertyRowMapper<类型>(类型.class): 类型是封装的结果的类型
			* queryForObject：查询结果，将结果封装为对象
				* 一般用于聚合函数的查询
	
		4. 练习：
			* 需求：
				1. 修改1号数据的 salary 为 10000
				2. 添加一条记录
				3. 删除刚才添加的记录
				4. 查询id为1的记录，将其封装为Map集合
				5. 查询所有记录，将其封装为List
				6. 查询所有记录，将其封装为Emp对象的List集合
				7. 查询总记录数



代码案例：

```java
package cn.itcast.jdbctemplate;

import cn.itcast.datasource.util.JDBCUtils;
import cn.itcast.domain.Emp;
import org.junit.Test;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import java.util.Map;

public class JDBCTemplateDemo02 {
    // 1. 获取JDBCTemplate对象
    JdbcTemplate jdbcTemplate = new JdbcTemplate(JDBCUtils.getDataSource());
    public static void main(String[] args) {

    }
    @Test
    public void test1(){
//      1. 修改1号数据的 salary 为 10000
        // 2. 定义sql
        String sql = "update emp set salary = 10000 where id=?";
        // 3. 执行sql
        int count = jdbcTemplate.update(sql, 1);
        System.out.println(count);
    }

    @Test
    public void test2(){
//      2. 添加一条记录
        String sql = "insert into emp values(null, ?,?,?,?, 1)";
        // 执行sql
        int count = jdbcTemplate.update(sql, "郭靖", "男", "9800", "2022-02-02");
        System.out.println(count);
    }

    @Test
    public void test3(){
//      3. 删除刚才添加的记录
        String sql = "delete from emp where id=?";
        int count = jdbcTemplate.update(sql, 7);
        System.out.println(count);
    }

    @Test
    public void test4(){
//      s4. 查询id为1的记录，将其封装为Map集合
        String sql = "select * from emp where id=?";
        Map<String, Object> map = jdbcTemplate.queryForMap(sql, 1);
        System.out.println(map);
    }

    @Test
    public void test5(){
//        5. 查询所有记录，将其封装为List
        String sql = "select * from emp";
        List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
        for (Map<String, Object> map: maps){
            System.out.println(map);
        }
    }

    @Test
    public void test6(){
//      6. 查询所有记录，将其封装为Emp对象的List集合
        String sql = "select * from emp";
        List<Emp> list = jdbcTemplate.query(sql, new RowMapper<Emp>() {
            @Override
            public Emp mapRow(ResultSet resultSet, int i) throws SQLException {
                Emp emp = new Emp();
                emp.setId(resultSet.getInt("id"));
                emp.setName(resultSet.getString("name"));
                emp.setGender(resultSet.getString("gender"));
                emp.setSalary(resultSet.getDouble("salary"));
                emp.setJoin_date(resultSet.getDate("join_date"));
                emp.setDept_id(resultSet.getInt("dept_id"));
                // 直接返回emp就行
                return emp;
            }
        });
        for (Emp e: list){
            System.out.println(e);
        }
    }

    @Test
    public void test6_2(){
//      6. 查询所有记录，将其封装为Emp对象的List集合
        String sql = "select * from emp";
        List<Emp> list = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class));
        // 如果转换的时候报错，请确保结果的基本数据类型均为引用类型，比如：int——>Integer
        for (Emp e: list)
            System.out.println(e);
    }

    @Test
    public void test7(){
//        7. 查询总记录数
        String sql = "select count(id) from emp";
        Long aLong = jdbcTemplate.queryForObject(sql, Long.class);// Long.class 类型为返回值结果类型
        System.out.println(aLong);
    }
}
```

