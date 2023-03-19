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

































