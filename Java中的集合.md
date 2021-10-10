# Java中的集合

表现为单个单元中的任何一组单独的对象都成为对象的集合。在 Java 中，JDK 1.2 中定义了一个名为 “集合框架” 的单独框架，其中包含所有集合类和接口。

Collection 接口（java.util.Collection）和 Map 接口（java.util.Map）是 Java 集合类的两个主要 “根” 接口。

#### 框架的定义：

框架是一组提供现成架构的**类**和**接口**。为了实现一个新特性或一个类，不需要定义一个框架。然而，一个最佳的面向对象设计总是包括一个带有类集合的框架，这样所有的类都执行相同类型的任务。

#### 一个单独的集合框架的作用：

在 Collection Framework 被引入之前，对 Java 对象（或集合）进行分组的标准方法是 Arrays 或 Vectors 或 Hashtables。所有这些集合都没有通用的接口。因而这些集合的构造函数，方法等不相同。

以下是通过往 Array，Hashtable 和 Vector 里添加元素来创建集合的示例：

~~~java
import java.io.*;
import java.util.*;

class CollectionDemo{
    
    public static void main(String args[]){
        
        // 构造一个 Array
        int arr[] = new int[] {1,2,3,4};
        Vector<Integer> v = new Vector();
        Hashtable<Integer,String> h = new Hashtable();
        
        // 往 Vector 中添加元素
        v.addElement(1);
        v.addElement(2);
        
        // 往 Hashtable 中添加元素
        h.put(1,"geeks");
        h.put(2,"4geeks");
        
        // 访问数组，Vector，和 Hashtable 的第一个元素
        System.out.println(arr[0]);
        System.out.println(v.elementAt(0));
        System.out.println(h.get(1));
    }
}
~~~

这些集合（Array，Vector，Hashtable）没有实现统一的成员变量访问接口，很难编写用于所有集合类型的算法。另一个缺点是大多是 “Vector” 方法都被修饰为 final ，无法扩展 “Vector” 类来实现类似类型的集合。因此引入了集合框架。

集合框架的优点：

(1) 一致的 API：API 有一组基本的接口，如 Collection，Set，List 或 Map。实现这些接口的所有类（ArrayList，LinkedList，Vector等）都有一些通用的方法集。

(2) 减少编程工作：此时，面向对象设计的基本概念以及基本实现（即抽象）

(3) 提高程序速度和质量：通过使用带有数据结构和算法的高性能集合框架来提高性能。这样程序员就不用考虑数据结构的最佳实现，而可以通过集合框架来简单地达成最佳实现。

#### 集合框架的层次结构

![1633247560990](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1633247560990.png)

* 类：类是用户定义的蓝图或原型，从中创建对象，它表示一种对象共同拥有的一组属性或方法
* 接口：接口也有属性和变量，但是接口中的方法必须是抽象的（只有方法签名，没有主体）。接口指定类必须做什么而不是怎么做。

以下是集合接口的通用方法：

| 方法        | 描述 |
| ----------- | ---- |
| add(Object) | 将对象添加到集合中 |
|addAll(Collection c)|讲一个集合的元素全部添加到此集合中|
|clear()|将集合中的元素全部清除|
|contains(Object o)|如果集合包含指定的元素，则返回 true|
|containsAll(Collection c)|如果此集合包含给定集合中的全部元素，则返回true|
|equals(Object o)|将指定的对象与此集合比较，查看是否相等|
|hashCode()|返回集合的 Hashcode|
|isEmpty()|如果集合为空集合，则返回 true|
|iterator()|此方法返回集合中的迭代器|
|max()|此方法返回集合中的最大值|
|parallelStream()|返回一个以该集合为源的并行 Stream|
|remove(Object o)|从集合中删除给定对象，如果存在重复值，则删除第一个出现的对象，|
|removeAll(Collectin c)|从此集合中删除包含在给定集合中的元素|
|removeIf(Predicate filter)|删除集合中满足给定**谓词**的元素|
|retainAll(Collection c)|仅保留此集合中包含在给定集合中的元素|
|size()|返回集合中元素的数量|
|spliterator()|在集合的元素上创建 Spliterator|
|stream()|返回以该集合为源的顺序 Stream|
|toArray()|返回包含此集合中所有元素的数组|



#### 扩展集合接口的接口

集合框架包含多个接口，每个接口都用于存储特定数据结构的数据。

框t架中的接口：

(1) Iterable Interface：集合框架的根接口。所有集合框架的接口和类都实现了这个接口，这个接口的作用是为集合提供迭代器。这个接口仅包含一个方法：

~~~java
Iterator Iterator();
~~~

(2) Collection Interface：这个接口扩展了 Iterator 接口。这个接口包含每个集合都有的所有基本方法，包括往集合中添加元素，删除元素，清空元素等。这个接口中拥有的方法可以确保方法的名称对于所有的集合类是通用的。可以说 Collection 接口为集合类奠定基础。

(3) List Interface：这是 Collection 接口的子接口。该接口用于列表类型的数据。用于存储有序的对象集合，允许存在重复元素。这个接口由各种类实现，比如 ArrayList，Vector，Stack。

~~~java
List<T> al = new ArrayList<>();
List<T> ll = new LinkedList<>();
List<T> v = new Vector<>();
~~~



实现 List 接口的类如下：

ArrayList：ArrayList 提供了 Java 中的动态数组。它会比标准数组慢，但是在对数组进行大量操作时十分管用。比如从集合中删除对象，集合会增长或缩小，则 ArrayList 的大小会自动改变。ArrayList 不能用于原始类型数据，如 int ，char 等，要存储这种数据时，需要使用包装类。

~~~python
import java.io.*;
import java.util.*;

class GFG{
    public static void main(String args[]){
        
        ArrayList<Integer> al = new ArrayList<Integer>();
        
        for(int i = 1;i <= 5;i++)
        	al.add(i);
        
        System.out.println(al); // 打印 [1, 2, 3, 4, 5]
        
        al.remove(3); // 移除集合中索引为3的元素
        
        System.out.println(al);	// 打印 [1, 2, 3, 5]
        
        for(int i = 0;i < al.size();i++){
            System.out.print(al.get(i) + " ") // 打印 1 2 3 5
        }
    }
}
~~~



LinkedList：LinkList 类是链表数据结构的一个实现，是一种线性存储结构，元素的存放是无序的。每个元素都是一个单独的对象（所以不能用原始类型），具有数据部分和地址部分。每个元素被成为一个节点。

~~~java
import java.io.*;
import java.util.*;

class GFG{
    public static void main(String args[]){
        
        LinkedList<Integer> ll = new LinkedList<Integer>();
        
        for(int i = 0;i <= 5;i++)
            ll.add(i);
        
        System.out.println(ll); // 打印 [1, 2, 3, 4, 5]
        
        ll.remove(3);
        
        System.out.println(ll); // 打印 [1, 2, 3, 5]
        
        for(int i = 0;i < ll.size();i++){
            System.out.print(ll.get(i) + " "); // 打印 1 2 3 5
        }
    }
}
~~~



Vector：Vector 提供了动态数组，在实现方面跟 ArrayList 相同。但是，Vector 与 ArrayList 之间的主要区别在于 Vector 是同步的，而 ArrayList 是非同步的。

~~~java
import java.io.*;
import java.util.*;

class GFG{
    
    public static void main(String args[]){
        
        Vector<Integer> v = new Vector<Integer>();
        
        for(int i = 0;i <= 5;i++)
            v.add(i);
        
        System.out.println(v); // 打印 [1, 2, 3, 4, 5]
        
        v.remove(3);
        
        System.out.println(v); // 打印 [1, 2, 3, 5]
        
        for(int i = 0;i < v.size();i++){
            System.out.print(v.get(i) + " "); // 打印 1 2 3 5
        }
    }
}
~~~





