[TOC]

# 一、包装类

## 1.1 基本类型包装类

- 基本类型包装类的作用

  - 将基本数据类型封装成数据对象的好处在于可以在对象中定义更多的功能方法操作该数据
  - 常用的操作之一：用于基本数据类型与字符串之间的转换

- 基本类型对应的包装类

  ![image-20230222183524462](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221835497.png)



## 1.2 Integer类

- Integer类概述

  包装一个对象中原始类型int的值

- Integer类构造方法

  ![image-20230222183920616](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302221839647.png)

- 示例代码

  ```java
  public class IntegerDemo {
      public static void main(String[] args) {
          // public Integer(int value)：根据int值创建Integer对象（过时）
          Integer i = new Integer(100);
          System.out.println(i);
  
          // public Integer(String s):根据String值创建Integer对象
          Integer i2 = new Integer("12");
          System.out.println(i2);
  //        Integer i3 = new Integer("abc"); // NumberFormatException
          System.out.println(i2);
  
          // public static Integer valueof(int i):返回表示指定的 int 值的Integer实例
          Integer i3 = Integer.valueOf(100);
          System.out.println(i3);
  
          // public static Integer valueof(string s) :返回一个保存指定值的Integer对象string
          Integer i4 = Integer.valueOf("200");
          System.out.println(i4);
          
          Integer i5 = Integer.valueOf("abc"); // NumberFormatException
          System.out.println(i5);
      }
  }
  ```

- 常量

```java
Integer.MAX_VALUE // 2^31-1
Integer.MIN_VALUE // -2^31
```



## 1.3 int 和String类型的相互转换

### 1.3.1 int转换为String

- 转换方式
  - 方式一：直接在数字后加一个空字符
  - 方式二：通过String类静态方法valueOf()
  
- 示例代码：

  ```java
  public class IntegerDemo{
      public static void main(String[] args){
          // int  --- String
          int number = 100;
          // 方式一
          String s1 = number + "";
          // public static String valueOf(int i)
          String s2 = String.valueOf(number);
          System.out.println(s2);
          System.out.println("-------");
      }
  }
  ```



### 1.3.2 String转int

- 转换方式

  - 方式一：现将字符串数字成Integer，再调用valueOf()方法
  - 方式二：通过Integer静态方法parseInt()进行转换

- 示例代码

  ```java
  public class IntegerDemo{
      public static void main(String[] args){
          // String --- int 
          String s = "100";
          
          // 方式一：String --- Integer --- int
          Integer i = Integer.valueof(s);
          // public int intValue()
          int x = i.intValue();
          System.out.println(x);
          
          // 方式二
          // public static int parseInt(String s)
          int y = Integer.parseInt(s);
          System.out.println(y);
      }
  }
  ```



## 1.4 字符串数据排列案例

- 案例需求

  有一个字符串："91 27 46 38 50"，请写程序实现最终输出结果是："27 38 46 50 91"

- 代码实现

  ```java
  public class IntegerTest{
      public static void main(String[] args){
          //定义一个字符串
          s1 = "91 27 46 38 50";
          
          //把字符串中的数字存入一个int数组中
          String[] strArray = s1.split(" ");
          
          // 定义一个int数组，存入
          int[] arr = new int[strArray.length];
          for(int i=0; i<strArray.length; i++){
              arr[i] = Integer.parseInt(strArray[i]);
          }
          
          // 对int 数组进行排序
          Array.sort(arr);
          
          // 将排序后的数组拼接到一个字符串中
          StringBuilder sb = new StringBuilder();
          for (int i=0; i<arr.length; i++){
              if(i == arr.length-1){
                  sb.append(arr[i]);
              }else{
                  sb.append(arr[i]).append(" ");
              }            
          }
          String result = sb.toString();
          
      }
  }
  ```

  

## 1.5 自动拆箱和自动装箱

- 自动装箱

  把基本数据类型转换为包装类类型

- 自动拆箱

  把包装类类型转换为对应的基本数据类型

- 代码示例

```java
Integer i = 200 ; // 自动装箱
i += 100;  // i = i + 100; i + 100 自动拆箱；i = i + 100 自动装箱
```





# 二、时间日期类

## 2.1 Date类

![image-20230222210331448](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302222103490.png)

- Date()方法获取的是系统当前的时间：如：Wed Feb 22 21:29:49 CST 2023
- Date（long time）方法获取的是1970年1月1日0时0分0秒加上time毫秒的时间：如：Thu Jan 01 09:00:00 CST 1970

- 示例代码

  ```java
  public class DateDemo{
      public static void main(){
          // public Date():分配一个 Date对象，并初始化，以便它代表它被分配的时间，精确到毫秒
          Date d1 = new Date();
          System.out.println(d1);
          
          // public Date(long date):分配一个 Date对象，并将其初始化为表示从标椎基准时间起制定的毫秒数
          long date = 1000*60*60;
          Date d2 = new Date(date);
          System.out.println(d2);
      }
  }
  ```



## 2.2 Data类的常用方法

![image-20230222212355347](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302222123400.png)

- getTime()获取的是从1970年1月1日以后到现在中间时间的毫秒值
- setTime(long time)是根据你给定的毫秒值，加上1970年1月1日之后的时间

代码示例：

```java
public class DateDemo01 {
    public static void main(String[] args) {
        Date d1 = new Date();
        System.out.println(d1);
        System.out.println(d1.getTime()/1000/3600/24/365 + 1970);

        long time = 1000*60*60;
        d1.setTime(time);
        System.out.println(d1);

        long date = 1000*60*60;
        Date d2 = new Date(date);
        System.out.println(d2);
    }
}
```

输出：

```java
Wed Feb 22 21:29:49 CST 2023
2023
Thu Jan 01 09:00:00 CST 1970
Thu Jan 01 09:00:00 CST 1970
```





## 2.3 SimpleDateFormat类概述

![image-20230222214502058](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302222145099.png)

![image-20230222214817852](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302222148891.png)

- SimpleDateFormat类的常用构造方法

  - 格式化从Date到String
    - public final String format(Date date): 将日期格式化成日期/时间字符串
  - 解析（从String到Date）
    - public Date parse(String source)：从给定字符串的开始解析文本以生成日期

- 示例代码

  ```java
  public class SimpleDateFromatDemo{
      public static void main(String[] args){
          // 格式化：从 Date 到 String
          Date d = new Date();
          // 无参构造
          SimpleDateFormat sdf = new SimpleDateFormat();
          String s = sdf.format(d);
          System.out.println(s);
      }
  }
  ```

  输出：

  ```java
  2023/2/22 下午10:25
  ```

格式化：

```java
public class SimpleDateFormatDemo {
    public static void main(String[] args) {
        // 格式化：从 Date 到 String
        Date d = new Date();
        //无参构造
//        SimpleDateFormat sdf = new SimpleDateFormat();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH：mm：ss");
        String s = sdf.format(d);

        System.out.println(s);
    }
}
```

输出：

```java
2023-02-22 22：28：08
```



解析：

```java
public class SimpleDateFormatDemo {
    public static void main(String[] args) throws ParseException {
        // 格式化：从 Date 到 String
        Date d = new Date();
        //无参构造
//        SimpleDateFormat sdf = new SimpleDateFormat();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH-mm-ss");
        String s = sdf.format(d);

        System.out.println(s);

        // 从 String 到 Date
        String ss = "2048-08-09 11:11:11";
        SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date dd = sdf1.parse(ss);
        System.out.println(dd);
    }
}
```

输出：

```java
2023-02-22 22-32-20
Sun Aug 09 11:11:11 CST 2048
```



## 2.4 日期工具类案例

![image-20230222224507658](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302222245703.png)

- 工具类

```java
/*
* 工具类
* 构造方法私有化
* */
public class DateUtils {

    // 构造方法私有化
    private DateUtils(){}

    /*
    * 把日期转为指定的格式的字符串
    * 返回参数类型：String
    * 参数：Date d，String format
    * */
    public static String timeToString(Date d, String format){
        SimpleDateFormat sdf = new SimpleDateFormat(format);
        String s = sdf.format(d);
        return s;
    }

    /*
    * 把字符串解析为指定格式的日期
    * 返回值类型：date
    * 参数：String s，String format
    * */
    public static Date stringToDate(String s, String format) throws ParseException {
        SimpleDateFormat sdf = new SimpleDateFormat(format);
        Date d = sdf.parse(s);
        return d;
    }


}
```

- 测试类

```java
import java.text.ParseException;
import java.util.Date;

public class DateDemo02 {
    public static void main(String[] args) throws ParseException {
        // 创建日期对象
        Date date = new Date();

        String s1 = DateUtils.timeToString(date, "yyyy年MM月dd日 HH:mm:ss");
        System.out.println(s1);

        String s2 = DateUtils.timeToString(date, "yyyy年MM月dd日");
        System.out.println(s2);

        String s3 = DateUtils.timeToString(date, "HH:mm:ss");
        System.out.println(s3);

        String s = "2067-09-10 12:23:34";
        Date dd = DateUtils.stringToDate(s, "yyyy-MM-dd HH:mm:ss");
        System.out.println(dd);
    }
}
```

输出：

```java
2023年02月22日 22:44:12
2023年02月22日
22:44:12
Sat Sep 10 12:23:34 CST 2067
```



## 2.5 Calendar类（应用）

- Calendar类概述

  Calendar为特定瞬间与一组日历字段之间的转换提供了一些方法，并为操作日历字段提供了一些方法

  Calendar提供了一个类方法getInstance用于获取这种类型的一般有用的对象，该方法返回一个Calendar对象

  其日历字段已使用当前日期和时间初始化：Calendar rightNow = Calendar.getInstance();

- Calendar类常用的方法

  ![image-20230224220738064](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302242207141.png)

示例代码：

```java
public class CalendarDemo {
    public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar);

        // public int get(int field): 返回给定日历字段的值
        int year = calendar.get(Calendar.YEAR);
        int month = calendar.get(Calendar.MONTH) + 1;
        int data = calendar.get(Calendar.DATE);
        System.out.println(year+"年"+month+"月"+data);

        // public abstract void add(int field, int amount):根据日历的规则，将指定的时间量加或者减去给定的日历字段
        // 1、3年前的今天
        calendar.add(Calendar.YEAR, -3);
        year = calendar.get(Calendar.YEAR);
        month = calendar.get(Calendar.MONTH)+1;
        data = calendar.get(Calendar.DATE);
        System.out.println(year+"年"+month+"月"+data);

        // 2、10年后的10天前
        calendar.add(Calendar.YEAR, 10);
        calendar.add(Calendar.DATE, -10);
        year = calendar.get(Calendar.YEAR);
        month = calendar.get(Calendar.MONTH)+1;
        data = calendar.get(Calendar.DATE);
        System.out.println(year+"年"+month+"月"+data);

        // public final void set(int year, int month, int date):设置当前日历的年月日
        calendar.set(2050, 10,10);
        year = calendar.get(Calendar.YEAR);
        month = calendar.get(Calendar.MONTH) + 1;
        data = calendar.get(Calendar.DATE);
        System.out.println(year+"年"+month+"月"+data);
    }
}
```

输出：

```java
java.util.GregorianCalendar[time=1677249427866,areFieldsSet=true,areAllFieldsSet=true,lenient=true,zone=sun.util.calendar.ZoneInfo[id="Asia/Shanghai",offset=28800000,dstSavings=0,useDaylight=false,transitions=19,lastRule=null],firstDayOfWeek=1,minimalDaysInFirstWeek=1,ERA=1,YEAR=2023,MONTH=1,WEEK_OF_YEAR=8,WEEK_OF_MONTH=4,DAY_OF_MONTH=24,DAY_OF_YEAR=55,DAY_OF_WEEK=6,DAY_OF_WEEK_IN_MONTH=4,AM_PM=1,HOUR=10,HOUR_OF_DAY=22,MINUTE=37,SECOND=7,MILLISECOND=866,ZONE_OFFSET=28800000,DST_OFFSET=0]
2023年2月24
2020年2月24
2030年2月14
2050年11月10
```



## 2.6 日期案例

![image-20230224225037416](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302242250467.png)

```java
public class CalendarTest {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入年份：");
        int year = sc.nextInt();

        Calendar c = Calendar.getInstance();
        c.set(year, 2, 1);

        c.add(Calendar.DATE, -1);

        int date = c.get(Calendar.DATE);

        System.out.println(year + "年的2月份有" + date + "天");

    }
}
```



# 三、异常

## 3.1 异常概述

![image-20230224232754937](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302242327990.png)



## 3.2 JVM默认处理异常的方式

- 如果程序出现了问题我们没有做任何处理，最终JVM会做默认的处理，处理方式有如下两个步骤：
  - 把异常的名称，错误原因及异常出现的位置等信息输出在了控制台
  - 程序停止运行



## 3.3 try-catch方式处理异常

- 定义格式

```java
try{
    可能出现异常的代码
}catch(异常名 变量名){
    异常的处理代码
}

说明：
    程序从try里面的代码开始执行
    出现异常，会自动生成一个异常类对象，该异常对象将被提交给java运行时系统
    当java运行时系统接收到异常对象是，会到catch中去找匹配的异常类，找到后进行异常处理
    执行完毕之后，程序还可以继续往下执行
```

- 执行流程
  - 程序从try里面的代码开始运行
  - 出现异常，就会跳转到对应的catch里面去执行
  - 执行完毕以后，程序还可以往下继续执行
- 实例代码

```java
public class ExceptionDemo{
    public static void main(String[] args){
        System.out.println("开始");
        method();
        System.out.println("结束");
    }
    
    public static void method(){
        try{
            int[] arr = {1,2,3};
            System.out.println(arr[3]);
            System.out.println("这里能够访问到吗");
        }catch (ArrayIndexOutofBoundsException e){
            // System.out.println("你访问的数据索引不存在，请回去修改为正确的索引");
            e.printStackTrace();
        }
    }
}
```



## 3.4 Throwable成员方法

- 常用方法

![image-20230224233755042](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202302242337086.png)

- 示例代码

```java
public class ExceptionDemo {
    public static void main(String[] args) {
        System.out.println("开始");
        method();
        System.out.println("结束");
    }

    public static void method(){
        try{
            int[] num = {1,2,3};
            System.out.println(num[3]);// 出现异常，将自动生成一个异常类对象，该异常类对象将被自动提交给java运行时系统
            // new ArrayIndexOutOfBoundsException("xxxxx")
        }catch (ArrayIndexOutOfBoundsException e){
            // 打印异常的错误信息到控制台
            e.printStackTrace();// 这个方法输出异常的错误信息是最全的，所以我们经常使用这个方法

            // public String getMessage() 返回此Throwable的详细消息字符串
            System.out.println(e.getMessage());

            // public String toString()：返回此可抛出的简短猫叔
            System.out.println(e.toString());
        }

    }
}

/*
public class Throwable{
   private String detailMessage;

   public Throwable(String message){
        detailMessage = message;
   }// 这说明，出现异常，生成一个异常类对象的时候，是自动进行了带参初始化
*  public String getMessage() {
        return detailMessage;
    }
}
*
* */
```

