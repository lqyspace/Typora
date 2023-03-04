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

