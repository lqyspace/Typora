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
modCount 与 expectedModCount 是否相等，如果不相等就抛出异常。其中 modCount 是实际修改的次数，而 expectedModCount
是预期修改的次数。

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

  for循环遍历，使用get方法获取元素，可以避免对预期修改值的判断，然后使用集合对象的额操作即可



### 1.2.4 ListIterator 列表迭代器

- ListIterator介绍

  - 通过List集合的listIterator()方法得到，所以说它是List集合特有的迭代器
  - 用于允许程序员沿任一方向遍历的列表迭代器，**在迭代期间修改列表**，并获取列表中迭代器的当前位置

- ListIterator中常用的方法

  - E next()：返回列表的下一个元素，并且返回光标的位置
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

  

## 3 HashSet集合概述和特点

### 3.1 HashSet集合的特点

- 底层数据结构是哈希表
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

  2. 当前元素的元素和已经存在的元素比较哈希值

     如果哈希值不同，则将当前元素进行存储

     如果哈希值相同，则进入第三步

  3. 通过equals()方法比较两个元素的内容

     如果内容不相同，则将当前元素进行存储

     如果内容相同，则不存储当前元素

![image-20230305135732729](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303051357782.png)

- HashSet集合存储元素
  - 要保证元素的唯一性，需要重写hashCode()和equal()方法

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

