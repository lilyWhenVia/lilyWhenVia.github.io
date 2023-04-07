---
    layout:     post
    title:      Collections_Map-集合_泛型
    date:       2023-04-07
    author:     lily
    header-img: img/JAVASE.jpg
    catalog: 	 true
    tags:       JAVASE
---

<a name="xRA9n"></a>
# 接口概述
1. collections作为接口提供了java内部相关数据结构的方法规范
2. map接口
<a name="Pn3CQ"></a>
# 集合框架概述

1. collections接口-----存储单个数据
   1. List子接口-----"动态数组"，存储的数据特点为，数据有序，可重复。
      1. ArrayList 实现类-----是list接口的主要实现类，底层封装了数组，按地址顺序存储；线程不安全，效率较高
      2. LinkedList实现类-----底层实现为链表，链式存储
      3.  vector 实现类----- 列表的古老实现类，内部方法是线程安全的，效率较低
         1. stack子类
   2. set子接口-----存储无序的，不可重复的数据
      1. hashSet----set接口的主要实现类，底层为hashMap，无新增方法，都是collection中已存在的方法。
            1. LinkedHashSet----在hashSet的存储数据的基础上，让每个数据都维护两个引用，pre && next，这是得linkedhashset更容易遍历
      2. treeSet----底层为红黑树，比hashset更容易查找数据
   3. Queue接口...略
2. map接口-----存储一对数据
   1. hashMap----作为map接口的主要实现类，底层为数组+链表+红黑树
      1. LinkedhashMap ---- hashmap基础加上数据之间双向链表
   2. treeMap 
   3. hashtable
      1. properties 作为
<a name="W2ZgN"></a>
# List实现类
<a name="zye2i"></a>
## ArrayList：

- **源码设计：**

**在jdk8之时，调用Alist类时并未生成初始化数组，而是直到向列表内添加第一个数据之时才初始化一个长度为16的Object数组， **<br />在不断向列表中添加数据之时会先判断数组空余长度是否足够，若足够，则可直接向数组中添加数据；若不足够，会创建一个长度为原来数组长度1.5倍的新数组，并把原数组数据拷贝到新数组内，并使用新数组继续存储数据。<br />插入数据特点：

1. 像python一样，向中间位置插入数据，之后的数据会自动向后移。若长度不够再扩容
2. 泛型，插入数据要类型一致

ArrayList 类位于 java.util 包中，使用前需要引入它，语法格式如下：
> import java.util.ArrayList; // 引入 ArrayList 类 
> ArrayList<E> objectName =new ArrayList<>();　 // 初始化

- **E**: 泛型数据类型，用于设置 objectName 的数据类型，**只能为引用数据类型**。
- **objectName**: 对象名。
<a name="bNMsu"></a>
## LinkedList

- **双向链表**，**内部没有声明数组**，而是定义了Node类型的first和last， 用于记录首末元素。同时，定义内部类Node，作为LinkedList中保存数据的基 本结构。Node除了保存数据，还定义了两个变量： 
   - prev变量记录前一个元素的位置 
   - next变量记录下一个元素的位置
- ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1667898967138-218ce4d9-56a2-4126-8529-1b58cb9892fd.png#averageHue=%23f8e7e5&clientId=ua82ecd57-9ac3-4&from=paste&height=238&id=u31d49cfc&name=image.png&originHeight=357&originWidth=514&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60054&status=done&style=none&taskId=u165510ee-7061-452a-a3d7-c8154938821&title=&width=342.6666666666667)
<a name="eHUBH"></a>
### linkedList存入数据方式

1. 创建方式---注意将集合变成列表的创建方式
> LinkedList 类位于 java.util 包中，使用前需要引入它，语法格式如下：
> 引入 LinkedList 类 
> `import java.util.LinkedList`  
> LinkedList<E> list = new LinkedList<E>();    // 普通创建方法 
> 或者
>  LinkedList<E> list = new LinkedList(Collection<? extends E> c); // 使用集合创建链表

2. 添加数据方式--->**数据可以插入链表的指定位置 ,后续链表相对关系不变**
| public boolean add(E e) | 链表末尾添加元素，返回是否成功，成功为 true，失败为 false。 |
| --- | --- |
| public void add(int index, E element) | 向指定位置插入元素。 |
| public boolean addAll(Collection c) | 将一个集合的所有元素添加到链表后面，返回是否成功，成功为 true，失败为 false。 |
| public boolean addAll(int index, Collection c) | 将一个集合的所有元素添加到链表的指定位置后面，返回是否成功，成功为 true，失败为 false。 |

<a name="KUueA"></a>
## 链表转为数组
```java
linkedlist.toArray(new int[]);
使用toArray方法
```
<a name="QV2A6"></a>
# Set实现类
<a name="axlgt"></a>
## HashSet ：

   - **底层设计：new hashSet，底层为外部实际new hashMap。hashSet底层就是hashMap**
   - **特点：不能保证元素的排列顺序 、HashSet 不是线程安全的 、集合元素可以是 null**
- **存储数据方式**
   1. 以存入数据计算其对应hash值 ，自定义类重写的hashCode方法参与其一部分运算
   2. 用得到的大几率唯一的hash值进行映射运算，运算结果为该数据在底层数组存储的位置
   3. 若该数组位置上本无数据存放，直接把该数据存入此地址中。
      1. 若已有数据存放（一个或者多个按链表及其他形式存储），调用equals方法比较已存入数据与要存入数据内容是否相同，若equals全返回true，放弃存入；
         1. 若全返回false（都不相等），链入结构。
   4. 底层数组初始容量为16，当如果使用率超过0.75，（16*0.75=12），就会扩大容量为原来的2倍。（16扩容为32，依次为64,128....等）
<a name="Kvfki"></a>
## LinkedhashSet：

   - **本质为hashSet的链式拓展，数据之间多了两个引用，类似双向链表。**

向这一上两个数据结构内添加自定义类时需要重写 hashCode() 和 equals()方法 ；
<a name="sAaOf"></a>
## treeSet：底层为treeMap
treeSet存储数据之时采用比较排序存储，存入数据大小有序，因此要存入同一类型的数据，存入数据之时需要调用该类的compareTo()方法，所以自定义类需要充血compareTo方法
<a name="TOMwF"></a>
# 集合常用方法
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1667898442545-72c8e3ab-8983-4616-a3eb-44e0d75a8d63.png#averageHue=%23faf6f4&clientId=ua82ecd57-9ac3-4&from=paste&height=415&id=u5b1f3be8&name=image.png&originHeight=623&originWidth=1093&originalType=binary&ratio=1&rotation=0&showTitle=false&size=194463&status=done&style=none&taskId=u638d4df1-5d42-4793-9178-484220c63db&title=&width=728.6666666666666)
<a name="dzT3u"></a>
# Map实现类
**map接口与collection接口无继承关系。主要实现类的内部类（inner class）entrySet、Node**
<a name="HmKge"></a>
## hashMap底层设计

1. 底层**装载内部类Node**<K,V>（jdk7用Entry）作为基本属性
> **static class **Node<K,V> **implements **Map.Entry<K,V>{...}

2. **hashMap用Node数组存储每一对key-value**，每组数据以Node节点管理。而hashSet以Set泛型类entrySet存储单个数据
> **transient **Node<K,V>[] table;
> **transient **Set<Map.Entry<K,V>> entrySet;

3. 存储数据调用put方法时实际上存储方式与hashSet相似，即计算key的hash值再填入node数组中进行存储。
4. 且jdk8时若Node数组某个位置已以链式存储，那么当前要存入对象会挨个比较链表节点，若与比较节点不同则会及时链到该对象下面（next属性）。
<a name="hb356"></a>
### properties--hashtable的子类

   - Properties类表示一组持久的属性。Properties可以保存到流中或从流中加载。属性列表中的每个键及其对应的值都是一个字符串。
   - 流操作相关类
<a name="eAqwn"></a>
# collections类

- collections类是用于操作collection接口和map接口的工具类
- 内含方法均为静态方法，如果提供给它们的集合或类对象为null，则此类的方法都抛出一个NullPointerException 。
- 常用方法：
| [addAll](../../java/util/Collections.html#addAll-java.util.Collection-T...-)([Collection](../../java/util/Collection.html)<? super T> c, T... elements)<br />将所有指定的元素添加到指定的集合。 <br />[shuffle](../../java/util/Collections.html#shuffle-java.util.List-)([List](../../java/util/List.html)<?> list)<br />使用默认的随机源随机排列指定的列表。 <br />[sort](../../java/util/Collections.html#sort-java.util.List-java.util.Comparator-)([List](../../java/util/List.html)<T> list, [Comparator](../../java/util/Comparator.html)<? super T> c)<br />根据指定的比较器引起的顺序对指定的列表进行排序。 <br />[swap](../../java/util/Collections.html#swap-java.util.List-int-int-)([List](../../java/util/List.html)<?> list, int i, int j)<br />交换指定列表中指定位置的元素。  |
| --- |

<a name="ZfM0K"></a>
# Iterator迭代器接口

1. Iterator对象称为迭代器(设计模式的一种)，主要用于遍历 Collection 集合中的元素。
2. Collection接口继承了java.lang.Iterable接口，该接口有一个iterator()方法，那么所 有实现了Collection接口的集合类都有一个iterator()方法，用以返回一个实现了 Iterator接口的对象。
3. Iterator 仅用于遍历集合，Iterator 本身并不提供承装对象的能力。如果需要创建 Iterator 对象，则必须有一个被迭代的集合。
4. 集合对象每次调用iterator()方法都得到一个全新的迭代器对象，默认游标都在集合 的第一个元素之前。
<a name="O3Eoe"></a>
## 使用方法
```java
Collection c = new ArrayList<>();
Iterator it = new Iterator();
while(it.hashNext()){
    sout(it.next()); //next():①指针下移 ②将下移以后集合位置上的元素返回
}
```
在调用**it.next()**方法之前必须要调用**it.hasNext()**进行检测。若不调用，且_下一条记录无效_，直接调用it.next()会抛出NoSuchElementException异常。
<a name="FnUxO"></a>
# 泛型

1. 泛型的意义：体现了程序的健壮性，使得数据的存储更规范更安全。
2. 泛型类和非泛型类/泛型类的继承 ：若父类为泛型类但子类继承时不使用泛型而使用父类已填充类型，则不是泛型类。
3. 泛型方法：泛型方法独立于泛型类
4. 泛型类的相互赋值 ：
   1. `o = s; o = i; `但此时列表o只能读取数据而不能向内添加数据。
```java
List<String> s = new ArrayList<>();
List<Integer> i = new ArrayList<>();
List<Object> o ;// List<?> o1;
```

5. 通配符的使用 ：
   1.  例如：`**public **V merge(K key, V value,BiFunction<? **super **V, ? **super **V, `

`? **extends **V> remappingFunction) {...}`<br />`? **super **V`表示该类是V类的子类，` ? **extends **V`表示该类继承于V类
<a name="IkccG"></a>
### ArrayList 排序
Collections 类也是一个非常有用的类，位于 java.util 包中，提供的 sort() 方法可以对字符或数字列表进行排序。<br />以下实例对字母进行排序：
```java
import java.util.ArrayList;
import java.util.Collections;  // 引入 Collections 类

public class RunoobTest {
    public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Taobao");
        sites.add("Wiki");
        sites.add("Runoob");
        sites.add("Weibo");
        sites.add("Google");
        Collections.sort(sites);  // 字母排序
        for (String i : sites) {
            System.out.println(i);
        }
    }
}
```
Hashtable： （1）Hashtable 是一个散列表，它存储的内容是键值对(key-value)映射。 （2）Hashtable 的函数都是同步的，这意味着它是线程安全的。它的key、value都不可以为null。 （3）HashTable直接使用对象的hashCode。 HashMap： （1）由数组+链表组成的，基于哈希表的Map实现，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。 （2）不是线程安全的，HashMap可以接受为null的键(key)和值(value)。 （3）HashMap重新计算hash值 Hashtable,HashMap,Properties继承关系如下： public class Hashtable<K,V> extends Dictionary<K,V>     implements Map<K,V>, Cloneable, java.io.Serializable public class HashMap<K,V>extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable java.lang.Objecct java.util.Dictionary<K,V> java.util.Hashtable<Object,Object> java.util.Properties 
