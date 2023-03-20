[TOC]

# 十、IO流

## 1 案例

案例：集合到文件（数据排序升级版）

![image-20230319152455032](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191524096.png)

学生类：

```java
public class Student {
    private String name;
    private int chinese;
    private int math;
    private int english;

    public Student(String name, int chinese, int math, int english) {
        this.name = name;
        this.chinese = chinese;
        this.math = math;
        this.english = english;
    }

    public Student() {
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getChinese() {
        return chinese;
    }

    public void setChinese(int chinese) {
        this.chinese = chinese;
    }

    public int getMath() {
        return math;
    }

    public void setMath(int math) {
        this.math = math;
    }

    public int getEnglish() {
        return english;
    }

    public void setEnglish(int english) {
        this.english = english;
    }

    public int getSum(){
        return this.chinese + this.math + this.english;
    }
}
```

测试类：

```java
public class TreeSetToFileDemo {
    public static void main(String[] args) throws IOException {
        TreeSet<Student> ts = new TreeSet<>(new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                int num = o2.getSum() - o1.getSum();
                int num2 = num==0? o2.getChinese() - o1.getChinese(): num;
                int num3 = num2==0? o2.getMath() - o1.getMath(): num2;
                int num4 = num3==0? o2.getEnglish() - o2.getEnglish(): num3;
                return num4;
            }
        });
        // 录入学生成绩
        for (int i=0; i<5;i++){
            Scanner sc = new Scanner(System.in);
            System.out.println("请录入第" + (i+1) + "个学生信息：");
            System.out.println("姓名：");
            String name = sc.nextLine();
            System.out.println("语文成绩：");
            int chinese = sc.nextInt();
            System.out.println("数学成绩：");
            int math = sc.nextInt();
            System.out.println("英语成绩：");
            int english = sc.nextInt();

            Student stu = new Student();
            stu.setName(name);
            stu.setChinese(chinese);
            stu.setMath(math);
            stu.setEnglish(english);

            ts.add(stu);
        }

        BufferedWriter bw = new BufferedWriter(new FileWriter("stu.txt"));

        for (Student stu : ts){
            StringBuilder sb = new StringBuilder();
            sb.append(stu.getName()).append(",").append(stu.getChinese()).append(",")
                    .append(stu.getMath()).append(",").append(stu.getEnglish()).append(",")
                    .append(stu.getSum());
            bw.write(sb.toString());
            bw.newLine();
            bw.flush();
        }

        bw.close();

    }
```



案例：复制单击文件夹

![image-20230319161313911](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303191613970.png)

```java
public class CopyFolderDemo {
    public static void main(String[] args) throws IOException {
        //创建数据源File对象
        File srcFolder = new File("E:\\itcast");
        // 获取数据源目录File对象的名称
        String srcFolderName = srcFolder.getName();// itcast
//        System.out.println(srcFolderName);
        // 创建目的地File对象，路径是模块名+itcast组成（myCharStream\\itcast）
        File destFolder = new File("src\\myCharStream",srcFolderName);
        // 判断目的目录的File对象是否存在
        if (!destFolder.exists()){
            destFolder.mkdirs();
        }

        // 获取数据源目录下所有文件的File数组
        File[] listFile = srcFolder.listFiles();
        for (File srcFile: listFile){
            String srcFileName = srcFile.getName();
            File destFile = new File(destFolder, srcFileName);
            // 复制文件
            copyFile(srcFile, destFile);
        }

    }

    private static void copyFile(File srcFile, File destFile) throws IOException {
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcFile));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destFile));

        byte[] bys = new byte[1024];
        int len;
        while ((len=bis.read(bys))!=-1){
            bos.write(bys, 0, len);
            bos.flush();
        }
        bos.close();
        bis.close();
    }
}
```

案例：复制多集文件夹

![image-20230319211435203](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303192114257.png)

```java
public class CopyFoldersDemo {
    public static void main(String[] args) throws IOException {
        //原文件夹路径
        File srcFolder = new File("E:\\itcast");
//        获取源文件夹路径名称
        String srcFolderName = srcFolder.getName();

//        目标文件夹
        File destParentFolder = new File("src");

        copyFiles(srcFolder, destParentFolder);

    }

    private static void copyFiles(File srcFolder, File destParentFolder) throws IOException {// itcast, src
        File tmpFolder = new File(destParentFolder, srcFolder.getName());
        if (srcFolder.isDirectory()){
            // 获取文件列表
            File[] listfiles = srcFolder.listFiles();
            if (!tmpFolder.exists()){
                tmpFolder.mkdir();
            }
            for (File file: listfiles){
                copyFiles(file, tmpFolder);
            }
        }else{
            BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcFolder));
            BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(tmpFolder));

            byte[] bys = new byte[1024];
            int len;
            while ((len=bis.read(bys))!=-1){
                bos.write(bys, 0, len);
                bos.flush();
            }
            bos.close();
        }
    }
}
```



## 2 复制文件的异常处理

> 原始写法

![image-20230319211723368](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303192117409.png)

- 代码示例

  ```java
  public static void merthod(){
          FileReader fr = null;
          FileWriter fw = null;
          try {
              fr = new FileReader("arrbw.txt");
              fw = new FileWriter("newarrbw.txt");
              char[] chs = new char[1024];
              int len;
              while ((len=fr.read(chs))!=-1){
                  fw.write(chs, 0, len);
              }
          }catch (IOException e){
              e.printStackTrace();
          }finally {
              try {
                  if (fr!=null){
                      fr.close();
                  }
                  if (fw!=null){
                      fw.close();
                  }
              }catch (IOException e){
                  e.printStackTrace();
              }
  
          }
      }
  ```

  

> JDK7之后的代码改进

![image-20230319213646193](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303192136233.png)

注意：JDK7之前的还要写 finally。JDK7版本以后自动释放资源，不用写close。

```java
//JDK7的改进方案
public static void merthod1(){
    try (FileReader fr = new FileReader("arrbw.txt");
         FileWriter fw = new FileWriter("newarrbw.txt");){
        char[] chs = new char[1024];
        int len;
        while ((len=fr.read(chs))!=-1){
            fw.write(chs, 0, len);
        }
    }catch (IOException e){
        e.printStackTrace();
    }
}
```



> JDK9的改进方案

![image-20230319214028546](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303192140596.png)

```java
// JDK9的改进方案
public static void method3() throws IOException {
    FileReader fr = new FileReader("arrbw.txt");
    FileWriter fw = new FileWriter("newarrbw.txt");
    try (fr;fw){
        char[] chs = new char[1024];
        int len;
        while ((len=fr.read(chs))!=-1){
            fw.write(chs, 0, len);
        }
    }catch (IOException e){
        e.printStackTrace();
    }
}
```

注意：

- JDK9改进了之后仍然要写抛出异常
- JDK7改进之后的可以不写抛出异常



## 3 IO特殊操作

- System中有两个静态的成员变量
  - public static final stream InputStream in：标准输入流，通常该流对应于键盘输入或者主机环境活用户指定的另一个输入源
  - public static final PrintStream out：标准输出流。通常该流对应于显示输出或由主机环境或用户指定的另一个输出目标

### 3.1 标准输入流

- 自己实现键盘录入数据太麻烦了

  ```java
  BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  ```

- 写起来太麻烦，Java就提供了一个类供我们使用

  ```java
  Scanner sc = new Scanner(System.in);
  ```

代码示例：

```java
public class SystemInDemo {
    public static void main(String[] args) throws IOException {
        // public static final InputStream in: 标准输入流
//        InputStream is = System.in;// 字节流

//        int by;
//        while ((by=is.read())!=-1){
//            System.out.print((char) by);
//        }

        // 如何把字节流转成字符流
//        InputStreamReader isr = new InputStreamReader(is);
//        //使用字符流能不能实现一次读取一行数据？可以
////        但是，一次读取一行数据的方法是字符缓冲流的特有方法
//        BufferedReader br = new BufferedReader(isr);
        // 将以上可以简写：
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("请输入一个字符串：");
        String line = br.readLine();
        System.out.println("你输入的字符串：" + line);

        System.out.println("请输入一个整数：");
        int i = Integer.parseInt(br.readLine());
        System.out.println("你输入的数字是：" + i);

//        自己实现键盘录入数据太麻烦了，所以java就提供了一个类供我们使用
        Scanner sc = new Scanner(System.in);
    }
}
```



### 3.2 标准输出流

输出语句的本质是一个标准的输出流：

- PrintStream ps = System.out;
- PrintStream类有的方法，System.out都可以使用。

```java
public class SystemOutDemo {
    public static void main(String[] args) {
        // public static final PrintStream out:标椎输出流
        PrintStream ps = System.out;

        //能够方便的打印各种数据
//        ps.print("hello");
//        ps.print(100);
//        ps.println("hello");
//        ps.println(100);

//        System.out的本质是一个字节输出流
        System.out.println(100);
        System.out.println("hello");
    }
}
```



## 4 特殊操作流

### 4.1 打印流

- 打印流分类
  - 字节打印流：PrintStream
  - 字符打印流：PrintWriter
- 打印流的特点
  - 只负责输出数据，不负责读取数据
  - 有自己特有的方法
- 字节打印流
  - PrintStream(String fileName)：使用指定的文件名创建新的打印流
  - 使用继承父类的方法写数据，查看的时候会转码；使用自己特有的方法写数据，查看的数据原样输出

```java
public class PrintStreamDemo {
    public static void main(String[] args) throws FileNotFoundException {
        //PrintStream(String fileName)：使用指定的文件名创建新的打印流
        PrintStream ps = new PrintStream("bw.txt");

        //写数据
        // 字节输出流的方法
//        ps.write(97); // a，经过转码

        // 使用特有的操作
//        ps.print(97); // 97
//        ps.println();
//        ps.print(98);//98
        ps.println(97);//97
        ps.println(98);//98
    }
}
```



- 字符打印流

  ![image-20230320002930244](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303200029315.png)

```java
public class PrintWriterDemo {
    public static void main(String[] args) throws IOException {
        // PrintWriter(String fileName)：使用指定的文件名创建一个新的PrintWriter，而不需要自动执行行刷新
//        PrintWriter pw = new PrintWriter("pw.txt");

//        pw.write("hello");
//        pw.write("\r\n");
//        pw.flush();// 没有这句的话，数据存不进去
//        pw.write("world");
//        pw.write("\r\n");
//        pw.flush();

//        pw.println("hello");
//        pw.flush();// 加上这句，数据才能存进去
//        pw.println("world");
//        pw.flush();
        //pw.close()

        // PrintWriter(Writer out, boolean outoFllush)：创建一个新的PrintWriter
        PrintWriter pww = new PrintWriter(new FileWriter("pww.txt"), true);
//        PrintWriter pww = new PrintWriter(new FileWriter("pww.txt"), false);
        pww.println("hello");// 实现了自动刷新数据
        pww.println("world");// 实现了自动刷新数据
        pww.close();
    }
}
```



### 4.2 案例

![image-20230320004055139](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303200040188.png)



```java
public class CopyJavaDemo {
    public static void main(String[] args) throws IOException {
        /*
        BufferedReader br = new BufferedReader(new FileReader("src\\fileexception\\FileException.java"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("src\\FileException.java"));

        String line;
        while ((line=br.readLine())!=null){
            bw.write(line);
            bw.newLine();
            bw.flush();
        }
        //释放资源
        br.close();
        bw.close();

         */

        BufferedReader br = new BufferedReader(new FileReader("src\\fileexception\\FileException.java"));
        PrintWriter  pw = new PrintWriter(new FileWriter("src\\FileException.java"), true);

        String line;
        while ((line=br.readLine())!=null){
            pw.println(line);
        }
        pw.close();
        br.close();

    }
}
```

注意：这里的 `pw.prrintln()` ，自动执行的刷新；不像传统的那种方式，不再需要刷新和换行。



## 5 对象序列化流

### 5.1 对象序列化和反序列化

对象序列化：就是将对象保存在磁盘中，或者在网络中传输对象

这种机制就是使用一个**字节序列**表示一个对象，该字节序列包含：对象的类型，对象的数据和对象中存储的属性等信息，字节序列写到文件之后，相当于文件中持久保存了一个对象的信息。

反之，该字节序列还可以从文件中读取回来，重构对象，对它进行反序列化



要实现序列化和反序列化就要使用对象序列化刘和对象反序列化流：

- 对象序列化流：`ObjectOutputStream`
- 对象反序列化流：`ObjectInputStream`

他们都是字节输入流。



### 5.2 对象序列化流

对象序列化流：`ObjectOutputStream`

- 将Java对象的原始数据类型和图形写入`OutputStream`，可以使用`ObjectInputStream`读取（重构）对象。可以通过使用流的文件来实现对象的持久存储。如果流是网络套接字流，则可以在另一个主机上或者另一个进程中重构对象 



构造方法：

- `ObjectOutputStream(OutputStream out)`：创建一个写入指定的`OutputStream`的`ObjectOutputStream`



序列化对象的方法：

- `void writeObject(Object obj)`：将指定的对象写入`ObjectOutputStream`



注意：

- NotSerializableException：如果一个对象没有实现java.io.Serializable接口，那么该对象就不能被序列化，运行的时候会抛出此异常。
- java.io.Serializable接口中没有方法，因此不需要重写方法

```java
public class ObjectOutputStreamDemo {
    public static void main(String[] args) throws IOException {
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("oos.txt"));

        //创建对象
        Student s = new Student("林青霞",32);

        oos.writeObject(s);

        // 释放
        oos.close();
    }
}
```

输出：

```java
Exception in thread "main" java.io.NotSerializableException: objectoutputstream.bean.Student
	at java.base/java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1185)
	at java.base/java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:349)
	at objectoutputstream.ObjectOutputStreamDemo.main(ObjectOutputStreamDemo.java:17)
```

注意：在此例中，Student对象并没有继承Serializable接口

更改：将Student对象序列化

```java
public class Student implements Serializable {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

此时测试通过。



### 5.3 对象的反序列化

对象的反序列化流 ：ObjectInputStream

- ObjectInputStream反序列化先前使用ObjectOutputStream编写的原始数据和对象



构造方法：

- ObjectInputStream（InputStream in）：创建从指定的InputStream读取的ObjectInputStream



反序列化对象的方法：

- Object  readObject()：从ObjectInputStream读取一个对象

```java
public class ObjectInputStreamDemo {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("oos.txt"));

        //Object readObject（）:从ObjectInputStream读取一个对象
        Object  obj = ois.readObject();

        Student s = (Student) obj;
        System.out.println(s.getName() + ","+s.getAge());

        ois.close();
    }
}
```





### 5.4 对象序列化流的问题

> **用对象序列化流序列化了一个对象后，假如我们修改了对象所属的类文件，读取数据会不会出问题？**

**修改前：**

学生类：

```java
public class Student implements Serializable {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

测试类：

```java
public class ObjectStreamDemo {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
//        write();
        read();
    }
    // 反序列化
    private static void read() throws IOException, ClassNotFoundException {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("oos.txt"));
        Object obj = ois.readObject();
        Student s = (Student) obj;
        System.out.println(s.getName() + "," + s.getAge());
        ois.close();
    }
    // 序列化
    private static void write() throws IOException {
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("oos.txt"));
        Student s = new Student("林青霞",32);
        oos.writeObject(s);
        oos.close();
    }

}
```

此时测试类可以正常通过：

现在开始修改Student的类文件：

**修改后：**

学生类：

```java
public class Student implements Serializable {
    private String name;
    private int age;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

我们在学生类里面加了一个`toString`方法，接下来运行测试类：爆出异常

```java
Exception in thread "main" java.io.InvalidClassException: objectoutputstream.bean.Student; local class incompatible: stream classdesc serialVersionUID = -6907431380092395188, local class serialVersionUID = -2413673435791492435
	at java.base/java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:689)
	at java.base/java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1903)
	at java.base/java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1772)
	at java.base/java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2060)
	at java.base/java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1594)
	at java.base/java.io.ObjectInputStream.readObject(ObjectInputStream.java:430)
	at objectoutputstream.ObjectStreamDemo.read(ObjectStreamDemo.java:15)
	at objectoutputstream.ObjectStreamDemo.main(ObjectStreamDemo.java:10)
```

我们来看一下InvalidClassException这个类的说明：

![image-20230320133756560](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303201337644.png)

由于Student类中不包含未知的数据类型，也有可访问的无参构造方法。因此抛出这个异常的原因是：**类的串行版本与从流中读取的类的描述符不匹配**。



从上面的报错中，我们可以看到 `serialVersionUID`并不一致。关于这个具体的描述，我们可以取序列化接口中去查看。

![image-20230320184432725](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303201844808.png)

![image-20230320185213060](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303201852128.png)



> **如果出问题了，该如何解决？**

![image-20230320185326156](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303201853215.png)

在可序列化类中加入：

```java
private static final long serialVersionUID = 42L;
```

这样就会有一致的序列化号。



> **如果一个对象中的某一个成员变量的值不想被序列化，又该如何实现呢？**

可以在不想序列化的变量前面加 `transient`，这样这个指定的变量就不参与序列化。

测试类：由于age没有参与序列化，所以输出为0,  0是对象的属性age的默认值。

```java
public class ObjectStreamDemo {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
//        write();
        read();
    }
    // 反序列化
    private static void read() throws IOException, ClassNotFoundException {
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("oos.txt"));
        Object obj = ois.readObject();
        Student s = (Student) obj;
        System.out.println(s.getName() + "," + s.getAge());
        ois.close();
    }
    // 序列化
    private static void write() throws IOException {
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("oos.txt"));
        Student s = new Student("林青霞",32);
        oos.writeObject(s);
        oos.close();
    }

}
```

输出：

```java
林青霞,0
```

学生类：

```java
public class Student implements Serializable {
    private String name;
    private transient int age;
    private static final long serialVersionUID = 1L;

    public Student() {
    }

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

//    @Override
//    public String toString() {
//        return "Student{" +
//                "name='" + name + '\'' +
//                ", age=" + age +
//                '}';
//    }
}
```



## 6 Properties

### 6.1 Properties作为Map集合的使用

Properties概述：

- 是一个Map体系的集合类
- Properties可以保存到流中或从流中加载
- 属性列表中的每个键及其对应的值都是一个字符串。



Properties基本使用：

```java
public class PropertiesDemo {
    public static void main(String[] args) {
//        Properties<String, String> prop = new Properties<>();// 写法错误
        Properties properties = new Properties();

        // 存储元素
        properties.put("001","张曼玉");
        properties.put("002","张曼玉2");
        properties.put("003","张曼玉3");

        Set<Object> keySet = properties.keySet();
        for (Object obj: keySet){
            Object value = properties.get(obj);
            System.out.println(obj + "," + value);
        }
    }
}
```

注：不能写泛型



### 6.2 Properties作为Map集合的特有方法

![image-20230320214852874](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303202148006.png)



```java
public class PropertiesDemo02 {
    public static void main(String[] args) {
        Properties prop = new Properties();
        prop.setProperty("001", "张曼玉");
        /*
        * public synchronized Object setProperty(String key, String value) {
              return put(key, value);
          }

        * @Override
          public synchronized Object put(Object key, Object value) {
              return map.put(key, value);
          }
          *
          * 这就使原本接受Object类型的，现在只能接受String
        * */
        prop.setProperty("002", "张曼玉2");
        prop.setProperty("003", "张曼玉3");

        System.out.println(prop.getProperty("002"));
        System.out.println(prop.getProperty("00111"));// null

        Set<String> names = prop.stringPropertyNames();
        for (String s: names){
//            System.out.println(s);
            String value = prop.getProperty(s);
            System.out.println(s + "," + value);
        }
    }
}
```



### 6.3 Properties和IO流相结合的方法

![image-20230320222917069](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303202229209.png)

```java
public class PropertiesandIODemo {
    public static void main(String[] args) throws IOException {
//        myStore();
        myLoad();
    }

    private static void myLoad() throws IOException {
        Properties prop = new Properties();
        FileReader fr = new FileReader("src\\fw.txt");
        prop.load(fr);
        fr.close();
        System.out.println(prop);
    }

    public static void myStore() throws IOException {
        Properties prop = new Properties();
        prop.setProperty("001", "张曼玉");
        prop.setProperty("002", "张曼玉2");
        prop.setProperty("003", "张曼玉3");

        FileWriter fw = new FileWriter("src\\fw.txt");
        prop.store(fw, null); // 如果不写描述信息可以为空
        fw.close();
    }
}
```



### 6.4 案例：游戏次数

![image-20230320224730693](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303202247815.png)



```java
public class PlayCountDemo {
    public static void main(String[] args) throws IOException {
        load();
    }

    private static void load() throws IOException {
        Properties prop = new Properties();

        FileReader fr = new FileReader("src\\game.txt");
        prop.load(fr);
        fr.close();

        String count = prop.getProperty("count");
        int num = Integer.parseInt(count);

        if (num>=3){
            System.out.println("游戏结束");
        }else{
            GuessNumber.start();
            num++;
            prop.setProperty("count", String.valueOf(num));
            FileWriter fw = new FileWriter("src\\game.txt");
            prop.store(fw,null);
            fw.close();
        }
    }
}
```

游戏类：

```java
public class GuessNumber {
    private GuessNumber(){

    }

    public static void start(){
        // 随机初始化一个数字
        Random r = new Random();
        int number = r.nextInt(100)+1; // 1-100
        while (true){
            Scanner sc = new Scanner(System.in);
            System.out.println("请输入你要猜的数字：");
            int guessNumber = sc.nextInt();

            if (guessNumber > number){
                System.out.println("你要猜的数字" + guessNumber + "大了");
            }else if (guessNumber < number){
                System.out.println("你要猜的数字" + guessNumber + "小了");
            }else {
                System.out.println("恭喜你猜中了");
                break;
            }
        }
    }
}
```

