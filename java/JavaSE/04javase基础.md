[TOC]

# 四、集合

## 1.2  List集合

### 1.2.1 List集合的概述与特点

- List集合概述
  - 有序集合（也称为序列），用户可以精确控制列表中每个元素的插入位置。用户可以通过整数索引访问元素，并搜索列表中的元素。
  - 与Set集合不同，列表通常允许重复的元素
- List集合的特点
  - 有索引
  - 可以存储重复的元素
  - 元素的存取有序



### 1.2.2 List集合的特有方法

![image-20230304233434795](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303042334875.png)



### 1.2.3 并发修改异常

```java
public class ListDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        list.add("hello");
        list.add("world");
        list.add("java");

        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()){
            String s = iterator.next();
            if (s.equals("world")){
                list.add("javaee");
            }
        }

        System.out.println(list);


    }
}
```

输出：

```java
Exception in thread "main" java.util.ConcurrentModificationException
	at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1042)
	at java.base/java.util.ArrayList$Itr.next(ArrayList.java:996)
	at com.mycollection.ListDemo.main(ListDemo.java:16)

进程已结束,退出代码1
```



通过一下分析源码：

```java
public interface List<E> extends Collection<E> {
    Iterator<E> iterator();
    boolean add(E e);
}


Exception in thread "main" java.util.ConcurrentModificationException
	at java.base/java.util.ArrayList$Itr.checkForComodification(ArrayList.java:1042)
	at java.base/java.util.ArrayList$Itr.next(ArrayList.java:996)
	at com.mycollection.ListDemo.main(ListDemo.java:16)


以上的报错，我们可以看出，错误的原因是在 ArrayList的子类Itr中的next方法的checkForComodification方法引起的。
我们跟进一下ArrayList的子类Itr中的源码：

public class ArrayList<E> extends AbstractList<E> implements List<E> {

    public Iterator<E> iterator() {
            return new Itr();
    }

    public boolean add(E e) {
            modCount++;
            add(e, elementData, size);
            return true;
    }
    
    public E get(int index) {
        Objects.checkIndex(index, size);
        return elementData(index);
    }

    private class Itr implements Iterator<E> {

            int expectedModCount = modCount;

            public E next() {
                checkForComodification();
                int i = cursor;
                if (i >= size)
                    throw new NoSuchElementException();
                Object[] elementData = ArrayList.this.elementData;
                if (i >= elementData.length)
                    throw new ConcurrentModificationException();
                cursor = i + 1;
                return (E) elementData[lastRet = i];
            }

            final void checkForComodification() {
                if (modCount != expectedModCount)
                    throw new ConcurrentModificationException();
            }
    }


}
首先，我们通过list.interator()方法实例化了 Itr() 对象。在Itr类中分别调用了next 和 checkForComodification
    
这两个方法。在Itr的实例化中，对 expectedModCount 进行了初始化， 与modCount的值是一样的。

我们可以看出：next调用了一个 checkForComodification 方法，而 checkForComodification 方法中判断了
modCount 与 expectedModCount 是否相等，如果不相等就抛出异常。其中 modCount 是实际修改的次数，而 expectedModCount是预期修改的次数。

在Itr被实例化的时候，就把实际修改值modCount赋给了预期修改值expectedModCount。

我们再来看 modCount 是来自父类 AbstractList 的属性：

public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {
    protected transient int modCount = 0;
}
可以看出modCount的初始值是 0.

所以在调用next方法的时候，每次都会判断 modCount 和 expectedModCount 这两个值是否一致，
正常来讲，这两个值一定是一样的，但是，我们在下面使用了 list.add()方法，
而add方法中会对 modCount 进行自加操作，所以就导致了 modCount 和 expectedModCount
的值不一样

```

修改代码：

```java
public class ListDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        list.add("hello");
        list.add("world");
        list.add("java");

//        触发并发修改异常：ConcurrentModificationException
//        Iterator<String> iterator = list.iterator();
//        while (iterator.hasNext()){
//            String s = iterator.next();
//            if (s.equals("world")){
//                list.add("javaee");
//            }
//        }
//
//        使用for增强循环也会默认使用迭代，因此使用增强for循环也会报错：ConcurrentModificationException
//        for (String s: list){
//            if (s.equals("world"))
//                list.add("javaee");
//        }

//        修改：for循环，使用get获取可以避免
        // 在get方法里面没有作预期修改值的判断，因此不会抛出异常
        for (int i = 0; i<list.size(); i++){
            String s = list.get(i);
            if (s.equals("world")){
                list.add("javaee");
            }
        }

        System.out.println(list);


    }
}
```



- 并发修改异常出现原因

  迭代器遍历的过程中，通过集合对象修改了集合的元素，造成了迭代器获取元素中 **预期修改值** 与 **实际修改值** 不一致，则会出现：**ConcurrentModificationException**

- 解决方法

  for循环遍历，使用get方法获取元素，可以避免对预期修改值的判断，然后使用集合对象的操作即可



### 1.2.4 ListIterator 列表迭代器

- ListIterator介绍

  - 通过List集合的listIterator()方法得到，所以说它是List集合特有的迭代器
  - 用于允许程序员沿任一方向遍历的列表迭代器，**在迭代期间修改列表**，并获取列表中迭代器的当前位置

- ListIterator中常用的方法

  - E next()：返回列表的下一个元素，并且前进光标的位置
  - boolean hasNext()：如果迭代具有更多的元素，则返回 true
  - E previous()：返回列表中的上一个元素，并且向后移动光标的位置
  - boolean hasPrevious()：如果此列表迭代器在相反的方向遍历列表时具有更多的元素，则返回true
  - void add(E e): 将指定元素插入列表
  - void remove()：从列表中删除next() 或 previous() （可选操作）返回的最后一个元素
  - void set(E e)：用指定元素（可选操作）替换 next() 或 previous() 返回的最后一个元素
  - int nextIndex()：返回 由后续调用 返回的元素 的索引 next()
  - int previousIndex()：返回 由后续调用 返回的的元素 的索引 previous()

  ```java
  public class ListIteratorDemo {
      public static void main(String[] args) {
          List<String> lst = new ArrayList<String>();
          lst.add("hello");
          lst.add("world");
          lst.add("java");
  
          ListIterator<String> stringListIterator = lst.listIterator();
          while (stringListIterator.hasNext()){
              String s = stringListIterator.next();
              System.out.println(s);
          }
  //
  
          System.out.println("-----------------");
  		stringListIterator = lst.listIterator();
          while (stringListIterator.hasNext()){
              String ss = stringListIterator.next();
              if (ss.equals("world")){
  //                在上一个例子中，我们是通过lst添加的元素会报错
  //                而本例中，则是用迭代器添加的元素成功运行
                  stringListIterator.add("javaee");
              }
          }
          System.out.println(lst);
  
      }
  }
  ```

输出：

```java
hello
world
java
-----------------
[hello, javaee, world, java]
```



源码分析：

```java
public interface List<E> extends Collection<E> {
    boolean add(E e);
    ListIterator<E> listIterator();
}

public interface ListIterator<E> extends Iterator<E>{
    E next();
    boolean hasPrevious();
    int nextIndex();
    E previous();
    void add(E e);
    ......
}

public class ArrayList<E> extends AbstractList<E>
        implements List<E>
    // 子类ArrayList对 List 中的 add方法 的具体实现
    public boolean add(E e) {
        modCount++;
        add(e, elementData, size);
        return true;
    }

	// 子类ArrayList 对 List 中的 listIterator() 方法的具体实现
	public ListIterator<E> listIterator() {
        return new ListItr(0);
    }
	
	private class Itr implements Iterator<E> {
        
        int expectedModCount = modCount;

        // prevent creating a synthetic constructor
        Itr() {}

        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }    

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }

	private class ListItr extends Itr implements ListIterator<E> {
        ListItr(int index) {
            super();
            cursor = index;
        }

        public void add(E e) {
            checkForComodification();

            try {
                int i = cursor;
                ArrayList.this.add(i, e);// 使用ArrayList里面的add方法，修改modCount+1
                cursor = i + 1;
                lastRet = -1;
                expectedModCount = modCount;// 执行add的时候，把实际修改次数赋给了预期修改次数，所以在next方法里面检查这两个值的时候不会报错
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
    }
}
```

总结：

- 如果使用 ArrayList 里面的add方法，会将实际修改次数加一，然后使用iterator迭代器的next方法时，会做一步检查，检查 modCount 与 expectedModCount 是否相等，如果不相等就会抛出异常
- 如果使用的是 ListIterator 里面的 listIterator进行迭代的话，执行ArrayList里面的add方法，导致modCount自加一，但是后面会继续把modCount的值赋给expectedModCount，因此在使用listIterator迭代器的next的方法时，检查modCount 与 expectedModCount 相等，从而避免了抛出异常



## 1.3 增强for循环

> **增强for：简化数组和Collection集合的遍历**

- 实现Iterator接口的类允许其对象称为增强型for语句的目标
- 它是JDK5之后出现的，其内部原理是一个Iterator迭代器



> **增强for的格式**

- 格式

  for(元素数据类型 变量名：数组或者Collection集合){

  }



### 1.3.1List集合存储对象的三种遍历方式

![image-20230305125722860](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051257917.png)

![image-20230305125733321](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051257354.png)



## 1.4 数据结构

### 1.4.1数据结构之栈和队列

> 栈

![image-20230305130335463](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051303507.png)

![image-20230305130259115](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051302158.png)

**注意：先进后出**

> 队列

![image-20230305130437258](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051304301.png)

**注意：先进先出**



### 1.4.2 数据结构之数组和链表

- 数组结构

  查询快、增删慢

- 队列结构

  查询慢、增删快（相对于数组而言）



### 1.4.3 List集合的子类特点

- ArrayList集合

  底层是数组实现，查询快，增删慢

- LinkedList集合

  底层是链表结构实现，查询慢，增删快



## 1.5 LinkedList的特有功能

![image-20230305131519024](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051315067.png)



# 五、Set集合

## 1 Set集合概述和特点

- Set集合的特点

  - 元素的**存取无序**
  - **没有索引**，只能通过迭代器或增强for循环遍历
  - **不能存储重复元素**

- Set集合的基本使用

  ```java
  public class SetDemo {
      public static void main(String[] args) {
          Set<String> set = new HashSet<String>();
          set.add("java");
          set.add("hello");
          set.add("world");
  
          set.add("hello");// 不存储重复的结构
  
          for (String s: set){
              System.out.println(s);
          }
      }
  }
  ```

  

## 2 哈希值

- 哈希值简介

  是JDK根据对象的地址或字符串或数字算出来的int类型的数值

- 如何获取哈希值

  Object类中的public int hashCode()：返回对象的哈希值

- 哈希值的特点

  - 同一个对象多次调用hashCode()方法返回的哈希值是相同的
  - 默认情况下，不同对象的哈希值是不同的。而重写hashCode()方法，可以实现让不同的对象的哈希值相同
  - 当重写hashCode和equals方法时，自动生成即可。

- 获取哈希值的代码

  学生类：

  ```java
  package myset.setutil.bean;
  
  public class Student {
      private String name;
      private int age;
  
      public Student(String name, int age) {
          this.name = name;
          this.age = age;
      }
  
      public Student() {
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
      public int hashCode() {
          return 0;// 导致不同的实例对象的哈希值都是0
      }
  }
  ```

  测试类：

  ```java
  public class HashDemo {
      public static void main(String[] args) {
          Student stu = new Student("李磊",23);
  
          /// 同一个对象的hashCode是相同的
          System.out.println(stu.hashCode()); // 0
          System.out.println(stu.hashCode()); // 0
          System.out.println("----------------");
  
          Student st2 = new Student("李磊",23);
          // 默认情况下，不同对象的hashCode是不同的
          // 但是重新hashCode方法，可以返回相同的值
          System.out.println(st2.hashCode()); // 0
          System.out.println("-----------------");
  
          System.out.println("hello".hashCode()); // 99162322
          System.out.println("world".hashCode()); // 113318802
          System.out.println("java".hashCode()); // 3254818
  
          System.out.println("world".hashCode()); // 113318802
          System.out.println("----------------------");
          System.out.println("重地".hashCode()); // 1179395
          System.out.println("通话".hashCode()); // 1179395
          // 之所以这两个一样，是因为字符串重写了hashCode方法
      }
  }
  ```

  示例：保证学生对象的唯一性：
  
  学生类：
  
  ```java
  package myset.setutil.bean;
  
  public class Student {
      private String name;
      private int age;
  
      public Student(String name, int age) {
          this.name = name;
          this.age = age;
      }
  
      public Student() {
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
      public boolean equals(Object o) {
          if (this == o) return true;
          if (o == null || getClass() != o.getClass()) return false;
  
          Student student = (Student) o;
  
          if (age != student.age) return false;
          return name != null ? name.equals(student.name) : student.name == null;
      }
  
      @Override
      public int hashCode() {
          int result = name != null ? name.hashCode() : 0;
          result = 31 * result + age;
          return result;
      }
  }
  
  ```
  
  测试类：
  
  ```java
  public class HashSetDemo {
      public static void main(String[] args) {
          HashSet<Student> hs = new HashSet<Student>();
  
          // 创建学生类
          Student s1 = new Student("料情绪",33);
          Student s2 = new Student("张曼玉",33);
          Student s3 = new Student("王祖贤",32);
  
          Student s4 = new Student("王祖贤",32);
  
          hs.add(s1);
          hs.add(s2);
          hs.add(s3);
          hs.add(s4);
  
          for (Student s: hs){
              System.out.println(s.getName() + ":" + s.getAge());
          }
      }
  }
  ```
  
  输出：
  
  ```
  王祖贤:32
  料情绪:33
  张曼玉:33
  ```
  
  注意：由于重写了hashCode方法，因此可以实现让不同的对象（地址不同，但是内容相同）返回相同的哈希值。则认为是重复元素。
  
  如果没有重写hashCode方法，输出便是4个。
  
  
  
  注：默认的 `equals`方法如下：
  
  ```java
  public boolean equals(Object obj) {
      return (this == obj);
  }
  ```
  
  默认的 `equals` 比较的是两个对象的地址值，如果不重写 `equals` 的话，`s3.equals(s4)` 返回的是 false；如果重写了 `equals` 方法的话，那么 `s3.equals(s4)` 返回的就是 true。
  
  

## 3 HashSet集合概述和特点

### 3.1 HashSet集合的特点

- 底层数据结构是**哈希表**
- 对集合的迭代顺序不做任何保证，也就是说不保证存储和取出的元素顺序一致
- 没有带索引的方法，所以不能使用普通for循环遍历
- 由于是Set集合，所以是不包含重复元素的集合



### 3.2 HashSet的基本使用

```java
public class SetDemo {
    public static void main(String[] args) {
        Set<String> set = new HashSet<String>();
        set.add("java");
        set.add("hello");
        set.add("world");

        set.add("hello");// 不存储重复的结构

        for (String s: set){
            System.out.println(s);
        }
    }
}
```



### 3.3 HashSet集合保证元素唯一性源码分析

- HashSet集合保证元素唯一性的原理

  1. 根据对象的哈希值计算存储位置

     如果当前位置没有元素，则直接存入

     如果当前位置有元素存在，则进入第二步

  2. 当前元素和已经存在的元素比较哈希值

     如果哈希值不同，则将当前元素进行存储

     如果哈希值相同，则进入第三步

  3. 通过equals()方法比较两个元素的内容

     如果内容不相同，则将当前元素进行存储

     如果内容相同，则不存储当前元素

![image-20230305135732729](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051357782.png)

- HashSet集合存储元素
  - 要保证元素的唯一性，需要重写hashCode()和equals()方法

```java
Set<String> set = new HashSet<String>();
set.add("java");
set.add("hello");
set.add("world");

set.add("hello");// 不存储重复的结构
------------------------------------------------
public interface Set<E> extends Collection<E> {
    boolean add(E e);
}

public class HashSet<E> extends AbstractSet<E> implements Set<E>{
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
}

public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>{
    public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        // 如果哈希表没有初始化就对其初始化
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;

        // 根据对象的哈希值计算对象的存储位置，如果该位置没有元素，就存储
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            /*
              存入的元素和以前的元素比较哈希值
                如果哈希值不同，会继续向下执行，把元素添加到集合
                如果哈希值相同，会调用对象的equals（）方法进行比较
                    如果返回false，会继续向下执行，把元素添加到集合
                    如果返回true，说明元素重复，不存储
            */
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        // new一个新的节点添加到集合中去
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
}
```



### 3.4 常见数据结构之哈希表

> **哈希表**

- JDK8之前，底层采用 **数组+链表** 实现，可以说是一个 元素为链表的数组
- JDK8之后，在长度比较长的时候，底层实现了优化

![image-20230309235103032](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303092351133.png)

说明：

1.  计算6个字符串的哈希值
2. HashSet的默认初始容量是16，所以声明一个长度16的数组
3. 对这6个字符串取余，模为16
4. 得到hello的存储位置为2，由于数组中并没有元素，所以可以直接将hello存入；然后存储world，发现还是2，位置上有元素，然后开始比较元素的哈希值，发现哈希值不一样，所以直接存入；再存入java，数组下标也是2，开始比较哈希值，哈希值不一样，如果哈希值一样的话，比较内容，内容不一样，直接存入。**存入的过程是以链表的形式进行存储。**
5. 当再次存入world时，存储的位置是2，发现2上有值，比较哈希值，发现两者的哈希值也是一样的，然后比较内容，内容是一样的，则认为是一个元素，所以world将不再进行存入
6. 当存入“重地”和“通话”的时候以此类推。



## 4 LinkedHashSet集合概述和特点

- LinkedHashSet集合的特点

  - 它是由哈希表和链表实现的Set接口，具有可预测的迭代次序
  - 由链表保证元素有序，也就是说元素的存储和去除的顺序是一致的
  - 由哈希表保证元素唯一，也就是说没有重复的元素

- LinkedHashSet集合的基本使用

  ```java
  public class LinkedHashSetDemo {
      public static void main(String[] args) {
          LinkedHashSet<String> lhs = new LinkedHashSet<>();
  
          lhs.add("heello");
          lhs.add("world");
          lhs.add("java");
          lhs.add("world");
          for (String s: lhs){
              System.out.println(s);
          }
      }
  }
  // 存储和取出的顺序是一致的
  ```

输出：

```java
heello
world
java
```



## 5 Set集合的排序

### 5.1 TreeSet集合概述和特点

- TreeSet集合概述

  - 元素有序，这里的顺序不是指存储和取出的顺序，而是按照一定的规则进行排序，具体的排序方式取决于构造方法
    - TreeSet()：根据元素的自然排序进行排序
    - TreeSet(Comparater comparater)：根据指定的比较器进行排序
  - 没有带索引的方法，所以不能使用普通的for循环遍历；但是可以使用迭代器和增强for进行遍历。
  - 由于是Set集合，所以不包含重复元素的集合

- TreeSet集合的基本使用

  ```java
  public class TreeSetDemo {
      public static void main(String[] args) {
          TreeSet<Integer> ts = new TreeSet<>();
          ts.add(10);
          ts.add(40);
          ts.add(30);
          ts.add(50);
          ts.add(20);
          ts.add(30);
  
          for (Integer i:ts){
              System.out.println(i);
          }
      }
  }
  ```

  输出：

  ```java
  10
  20
  30
  40
  50
  ```


注：**默认排序的顺序是升序**

### 5.2 自然排序Comparable的使用

- 存储学生对象并遍历，创建TreeSet集合使用无参构造方法
- 要求：按照年龄从小到大，年龄相同时，按照姓名的字母顺序排列

- 实现步骤：
  - 用TreeSet集合存储自定义对象，无参构造方法使用的是自然排序对元素进行排序
  - 自然排序，就是让元素所属的类实现Comparable接口，并且需要重写CompareTo(T o)方法
  - 重写方法时，一定要注意排序规则必须按照要求的主要条件和次要条件来写



测试类：

```java
public class TreeSetDemo01 {
    public static void main(String[] args) {
        // 按照年龄从小到大排列，年龄相同时，按照姓名的字母表顺序
        TreeSet<Student> ts = new TreeSet<>();
        Student s1 = new Student("xihi", 23);
        Student s2 = new Student("wangzhaojun", 24);
        Student s3 = new Student("diaochan", 25);
        Student s4 = new Student("yangyuhuan", 26);

        Student s5 = new Student("libqingxia", 26);
		Student s6 = new Student("libqingxia", 26);

        // 把学生添加到集合
        ts.add(s1);
        ts.add(s2);
        ts.add(s3);
        ts.add(s4);
        ts.add(s5);
        ts.add(s6);// s6并未有添加进去，保证了学生的唯一性

        for (Student s: ts){
            System.out.println(s.getName() + ":"+ s.getAge());
        }
    }
}
```

学生类：

```java
public class Student implements Comparable<Student>{
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Student() {
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
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        Student student = (Student) o;

        if (age != student.age) return false;
        return name != null ? name.equals(student.name) : student.name == null;
    }

    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }


    @Override
    public int compareTo(Student s) {
//        return 0; // 此时打印会输出一个元素，因为在添加第二个元素的时候，会和第一个元素进行比较，如果比较的结果是0，则认为是同一个元素
//        所以没有添加成功
//        return 1;// 此时比较的结果：第二个元素比第一个元素大，第三个元素比第二个元素大，后续元素比前的元素的大，为升序
//        return -1; // 此时则认为：第二个元素比第一个元素小，第三个元素比第二个元素小，为降序
//        int num = this.age - s.age; // this在前，为升序，this在后，为降序
//        int num = this.age - s.age;

//        总结：return 1 时为升序， return -1 时为降序
//        而this在前的时候为升序，this在后的时候为降序，说明this在前时，是用大的值减小的值；this在后时，是用小的值减大的值
        int num1 = this.age - s.age;
        int num = num1==0? this.name.compareTo(s.name):num1;
        return num;
    }
}
```

总结：

- 将对象按照自定义的规则进行排序时，需要 **实现Comparable<Student> 接口** 同时重写 **compareTo**方法
- return 0时，比较时则认为是同一个元素，所以在使用集合的add方法时，并不能有效添加。
- return 1时为升序，则认为后添加的元素比先添加的元素大
- return -1时为降序，则认为后添加的元素比先添加的元素小
- this在前时为升序，this在后时为降序
- String类也实现了 **Comparable<String>接口**，因此也可以使用 compareTo方法
- compareTo方法相当于 Set唯一性原理里面的第三步，比较内容。





### 5.3 比较器排序Comparator的使用

- 存储自定义对象并遍历，创建TreeSet集合并使用带参构造方法
- 要求：按照年龄从小到大排序，年龄相同时，按照姓名的字母表顺序排列

学生类：

```java
public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Student() {
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
public class TreeSetDemo {
    // 存储学生对象并遍历，创建TreeSet集合并使用带参构造方法
//    要求：按照年龄从小到大排序，年龄相同时，按照姓名的字母表顺序排序
    public static void main(String[] args) {
        TreeSet<Student> ts = new TreeSet<>(new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                // this.age - s.age
                // o1 - o2
                int num = o1.getAge() - o2.getAge();
                int num1 = num == 0? o1.getName().compareTo(o2.getName()) : num;
                return num1;
            }
        });

        Student s1 = new Student("xihi", 27);
        Student s2 = new Student("wangzhaojun", 24);
        Student s3 = new Student("diaochan", 21);
        Student s4 = new Student("yangyuhuan", 26);

        Student s5 = new Student("libqingxia", 26);
        Student s6 = new Student("libqingxia", 26);

        // 把学生添加到集合
        ts.add(s1);
        ts.add(s2);
        ts.add(s3);
        ts.add(s4);
        ts.add(s5);
        ts.add(s6);

        for (Student s: ts){
            System.out.println(s.getName() + ":" + s.getAge());
        }
    }
}
```

总结：

- 用TreeSet集合存储自定义对象时，带参构造方法使用的是 **比较器排序** 对元素进行排序
- 比较器排序，就是让集合构造方法接收 **Comparator的实现类对象**，要重写 compare(T o1, T o2)方法
- 重写方法时，一定要注意排序规则必须按照要求的**主要条件和次要条件**来写。



案例：成绩排序案例

![image-20230310103144647](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303101031786.png)

![image-20230310103206867](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303101032926.png)

![image-20230310103232559](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303101032637.png)

![image-20230310103247841](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303101032891.png)

**注：**

- `equals` 方法常用于比较两个对象的内容是否相等

  - 默认的 `equals`方法

    ```java
    // Object中
    public boolean equals(Object obj) {
        return (this == obj);
    }
    ```

    ```java
    // String中
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
    ```

  - 重写的 `equals`方法

    ```java
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
    
        Student student = (Student) o;
    
        if (age != student.age) return false;
        return name != null ? name.equals(student.name) : student.name == null;
    }
    ```

- `hashCode` 方法可以使不同的对象具有相同的哈希值，常用于不同的对象，但是内容相同时需要添加到`Set`集合中时。不同的对象添加到`Set`中时，由于地址不同因此会认为是不同的，因此对于内容相同的不同对象，为了避免重复添加，因此可以重写 `hashCode`方法

  - 默认的 `hashCode`方法

    ```java
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;
    
            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
    ```

  - 重写的 `hashCode`方法

    ```java
    @Override
    public int hashCode() {
        int result = name != null ? name.hashCode() : 0;
        result = 31 * result + age;
        return result;
    }
    ```

- `compareTo`方法可以让对象按照一定的规则进行排序，在比较两个对象的过程中，如果 `compareTo` 返回的结果是 0，则认为这两个对象相同，即认为这两个是重复的对象

  - 默认的 `compareTo`方法

    ```java
    public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;
    
        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }
    ```

  - 重写的 `compareTo`方法

    ```java
    @Override
        public int compareTo(Student o) {
    //        return 0;
            int num1 = this.age - o.age;
            int num = num1==0? this.name.compareTo(o.name) : num1;
            return num;
        }
    ```



### 5.4 案例：不重复的随机数

需求：编写一个程序，获取10个1-20之间的随机数，需要随机数不能重复，并在控制台输出

思路：

- 创建Set集合对象

- 创建随机数对象

- 判断集合的长度是不是小于10

  是：产生一个随机数，添加到集合

  否：回到3继续

- 遍历集合

```java
public class SetDemo01 {
    public static void main(String[] args) {
        // 创建Set集合对象
//        Set<Integer> set = new HashSet<>();// 无序
        Set<Integer> set = new TreeSet<>(); // 有序

        // 创建随机数对象
        Random r = new Random();

        while (set.size()<10){
            int number = r.nextInt(20) + 1;
            set.add(number);
        }

        for (Integer i: set)
            System.out.println(i);
    }
}
```

总结：

- 用HashSet集合，最终的数据是不会排序的
- 用TreeSet集合，最终的数据是可以排序的





# 六、泛型

## 1 泛型概述

泛型：是`JDK5`中引入的特性，他提供了编译时类型安全检测机制，该机制允许在编译时检测到非法的类型；

它的本质是 **参数化类型** 也就是说所操作的数据类型被指定为一个参数；

提到参数，最熟悉的就是定义方法时有形参，然后调用此方法时传入实参；

而参数化类型，就是 **将类型由原来的具体的类型参数化，然后在使用/调用时传入具体的类型**；

这种参数类型可以用在类，方法和接口中，分别被称为**泛型类**，**泛型方法**，**泛型接口**。



## 2 泛型定义格式

- <类型>：指定一种类型的格式。这里的类型可以看成是形参。
- <类型1，类型2...>：指定多种类型的格式，多种类型之间用逗号隔开。这里的类型可以看成是形参。
- 将来具体调用的时候给定的类型可以看成是实参，**并且实参的类型只能是引用数据类型。**

```java
public class GenericDemo {
    public static void main(String[] args) {
        // 创建集合对象
        // 没有使用泛型
//        Collection c = new ArrayList();
        Collection<String> c = new ArrayList<String>();

        c.add("hello");
        c.add("hworld");
        c.add("java");
//        c.add(100); // 会自动封装为Integer类型

//        Iterator it = c.iterator();
        Iterator<String> it = c.iterator();
        while (it.hasNext()){
//            Object obj = it.next();
//            System.out.println(obj);
            //向下转型
           //  String s = (String)it.next();
            String s = it.next();
            System.out.println(s);
        }
    }
}
```

泛型的好处：

- 把运行期间的问题提前到了编译期间
- 避免了强制类型转换



## 3 泛型类

> 定义格式

- 格式：修饰符 class 类名 <类型>{ }
- 范例：public class Generic<T>{ }
  - 此处的T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型



自定义一个泛型类：

```java
public class Generic<T> {
    private T t;

    public T getT() {
        return t;
    }

    public void setT(T t) {
        this.t = t;
    }
}
```

测试类：

```java
public class GenericDemo01 {
    public static void main(String[] args) {
        // 没有使用泛型类
        Student stu = new Student();
        stu.setName("林青霞");
        System.out.println(stu.getName());

        Teacher tea = new Teacher();
        tea.setAge(34);
        System.out.println(tea.getAge());

        // 使用自定义的泛型类
        System.out.println("------------------------");
        Generic<String> ger = new Generic<String>();
        ger.setT("林青霞");
        System.out.println(ger.getT());

        Generic<Integer> g = new Generic<Integer>();
        g.setT(10);
        System.out.println(g.getT());

        Generic<Boolean> bool = new Generic<>();
        bool.setT(true);
        System.out.println(bool.getT());


    }
}
```

输出：

```java
林青霞
34
------------------------
林青霞
10
true
```



## 4 泛型方法

实例：

```java
public class GenericUtil<T> {
//    public void show(String s){
//        System.out.println(s);
//    }
//    public void show(Integer i){
//        System.out.println(i);
//    }
//
//    public void show(Boolean b){
//        System.out.println(b);
//    }

    public void show(T t){
        System.out.println(t);
    }
}
```

测试类：

```java
public class GenericDemo02 {
    public static void main(String[] args) {
//        GenericUtil g = new GenericUtil();
//        g.show("lnqingxia");
//        g.show(30);
//        g.show(true);
//        g.show(12.34);// 没有提供相应的方法

//        使用的是泛型类改进版
		GenericUtil<String> g1 = new GenericUtil<>();
        GenericUtil<Integer> g2 = new GenericUtil<>();
        GenericUtil<Boolean> g3 = new GenericUtil<>();
        g1.show("林青霞");
        g2.show(30);
        g3.show(true);
    }
}
```

输出：

```
林青霞
30
true
```



接下来使用**泛型方法**解决：

泛型方法：

```java
public class GenericUtil{
    public <T> void show(T t){
        System.out.println(t);
    }
}
```

测试类：

```java
public class GenericDemo02 {
    public static void main(String[] args) {
//        GenericUtil g = new GenericUtil();
//        g.show("lnqingxia");
//        g.show(30);
//        g.show(true);
//        g.show(12.34);// 没有提供相应的方法

//        使用的是泛型类
//        GenericUtil<String> g1 = new GenericUtil<>();
//        GenericUtil<Integer> g2 = new GenericUtil<>();
//        GenericUtil<Boolean> g3 = new GenericUtil<>();
//        g1.show("林青霞");
//        g2.show(30);
//        g3.show(true);

        GenericUtil g = new GenericUtil();
        g.show("林青霞");
        g.show(30);
        g.show(true);
        g.show(12.34);
    }
}
```

输出：

```java
林青霞
30
true
12.34
```

总结：

- 格式：修饰符 <类型> 返回值类型 方法名(类型 变量名){ }
- 范例：public \<T\> void show(T t){ }



## 5 泛型接口

> 格式

- 格式：修饰符 interface 接口名<类型> { }
- 范例：public interface Generic<T>{ }

实例：

接口类：

```java
public interface GenericInterface<T> {
    void show(T t);
}
```

实现类：

```java
public class GenericImpl<T> implements GenericInterface<T> {// 实现类如果不写泛型T，编译会不通过
    @Override
    public void show(T t) {
        System.out.println(t);
    }
}
```

测试类:

```java
public class GenericDemo03 {
    public static void main(String[] args) {
        GenericInterface<String> g1 = new GenericImpl<String>();
        g1.show("林青霞");
    }
}
```



扩展：其实下面这个是 **泛型方法**

接口类：

```java
public interface Generic2 {
    <T> void show(T t);
}
```

实现类：

```java
public class Generic2Impl implements Generic2 {// 泛型方法，所以类上可以不用泛型T
    @Override
    public <T> void show(T t) {
        System.out.println(t);
    }
}
```

测试类：

```java
public class GenericDemo03 {
    public static void main(String[] args) {
        Generic2 gg1 = new Generic2Impl();
        gg1.show(12.34);
        gg1.show("string");
        gg1.show(true);
    }
}
```



## 6 类型同配符

为了表示各种泛型List的父类可以使用类型通配符

- 类型通配符：<?>
- List<?>：表示元素类型未知，它的元素可以匹配 **任何类型**
- 这种带通配符的List仅表示它是各种泛型List的父类，**并不能把元素添加其中**



如果说我们不希望List<?>是任何类型的父类，只希望它代表某一类泛型List的父类，可以使用类型通配符的上限

- 类型通配符上限：<? extends 类型>
- List<? extends Number>：它表示的类型是Number或者其子类型



除了可以指定类型通配符的上限，我们也可以指定类型通配符的下限

- 类型通配符下限：<? super 类型>
- List<? super Number>：它表示的类型是Number或者其父类型

示例：

```java
public class GenericDemo {
    public static void main(String[] args) {
        // 类型通配符<?>
        List<?> list1 = new ArrayList<Object>();
        List<?> list2 = new ArrayList<Number>();
        List<?> list = new ArrayList<Integer>();

        // 类型通配符的上限：<? extends 类型>
//        List<? extends Number> list4 = new ArrayList<Object>();// 报错
        List<? extends Number> list5 = new ArrayList<Number>();
        List<? extends Number> list6 = new ArrayList<Integer>();

        //类型通配符的下限：<? super 类型>
        List<? super Number> list7 = new ArrayList<Object>();
        List<? super Number> list8 = new ArrayList<Number>();
//        List<? super Number> list9 = new ArrayList<Integer>();// 报错

    }
}
```



## 7 可变参数

可变参数又称参数个数可变，用作方法的形参出现，那么方法参数就是可变的了

- 格式：修饰符 返回值类型 方法名(数据类型... 变量名){ }
- 范例：public static int sum(int... a){ }

```java
public class ArgsDemo {
    public static void main(String[] args) {
        System.out.println(sum(1,2));
        System.out.println(sum(1,2,3));
        System.out.println(sum(1,2,3,4));
    }

    public static int sum(int... a){
//        System.out.println(a); // 输出数组
//        return 0;
        int sum=0;
        for (int i:a){
            sum+=i;
        }
//        System.out.println(a[1]); // a是一个数组
        return sum;
    }
    
//    public static int sum(int a, int b){
//        return a+b;
//    }
//    public static int sum(int a, int b, int c){
//        return a+b+c;
//    }
}
```

输出：

```java
3
6
10
```

注意：如果参数里面既有可变参数也有不可变参数，应该将不可变参数放在可变参数的前面

```java
public static int sum(int b, int... a){
//        System.out.println(a); // 输出数组
//        return 0;
        int sum=0;
        for (int i:a){
            sum+=i;
        }
//        System.out.println(a[1]); // a是一个数组
        return sum;
    }
```

总结：

- 这里的变量是一个数组
- 如果一个方法有很多参数，**包含可变参数，可变参数要放到最后**





## 8 可变参数的使用

> **Arrays工具类中有一个静态方法**：

- public static <T> List<T> asList(T... a)：返回由指定数组支持的固定大小的列表

- 返回的集合不能做**增删**操作，可以做修改操作



> **List接口中有一个静态方法：**

- public static <E> List<E> of(E... elements)：返回包含任意数量元素的不可变列表

- 返回的集合不能做**增删改**操作



> **Set接口中有一个静态方法：**

- public static <E> Set<E> of(E... elements)：返回一个包含任意数量元素的不可变集合

- 在给元素的时候，不能给重复的元素
- 返回的集合不能做**增删**操作，没有修改的方法

```java
public class ArgsDemo01 {
    public static void main(String[] args) {
        // public static <T> List<T> asList(T... a)：返回由指定数组支持的固定大小的列表
        List<String> list = Arrays.asList("hello", "world", "java");


//        list.add("python"); // UnsupportedOperationException
//        list.remove("world"); // UnsupportedOperationException
//        list.set(1, "javaeee");
//        System.out.println(list);


        // public static <E> List<E> of(E... elements)：返回包含任意数量元素的不可变列表
//        List<String> list1 = List.of("hello", "world", "kjava","world");
//
//        list1.add("123");//UnsupportedOperationException
//        list1.remove("hello"); // UnsupportedOperationException
//        list1.set(1, "12");//UnsupportedOperationException
//        System.out.println(list1);

//        Set<String> set = Set.of("java", "hello", "world", "world");//IllegalArgumentException
        Set<String> set = Set.of("java", "hello", "world");//IllegalArgumentException
//        set.add("jjj");//UnsupportedOperationException
//        set.add("hello");//UnsupportedOperationException
//        set.remove("world");//UnsupportedOperationException
        
        System.out.println(set);
    }
}
```

