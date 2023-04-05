<a name="uMsdg"></a>
# string相关类
- String
- StringBuilder：线程安全
   - deleteCharAt() 删除指定位置字符
- StringBuffer：线程不安全，效率高
<a name="mPrC6"></a>
## String底层实现

- final char[] a;底层实际上是不可变的char数组。
- [Serialized Form](../../serialized-form.html#java.lang.StringBuffer) 该类型可序列化，可将string序列化为一串数字，用于网络传输等
<a name="V2IlW"></a>
### string不可变性
在内存中一旦创建，不会在原有的基础上改变，若要把字符串改变，会在常量池中新创建一个新的字符串常量；

- 1.8之时方法区的常量池
- 字符串的字面量赋值和构造器新建
- 常量池没有重复的常量
- 有变量参与的赋值是从堆空间中获取变量，因此整体变量会指向堆空间中而不是指向常量池
```java
//字面量赋值
String s1 = "helloWorld"; /* 指向常量池*/
//构造器赋值/初始化
String s2 = new String("helloWorld"); /* 指向堆空间*/
//有变量参与的赋值
String s3 = "hello"; 
String s4 = "World";
String s5 = s3 + s4; /* 堆空间 */
```
<a name="KM0Nz"></a>
## String类
常用方法<br />String.copyValueOf(obj); --->可迭代对象，赋值为string类型并返回该string

| [charAt](../../java/lang/String.html#charAt-int-) (int index)<br />返回 char指定索引处的值。<br />[substring](../../java/lang/String.html#substring-int-int-) (int beginIndex, int endIndex)<br />返回一个字符串，该字符串是此字符串的子字符串。 <br />[toCharArray](../../java/lang/String.html#toCharArray--) () ---> char[]<br />将此字符串转换为新的字符数组。<br />[compareTo](../../java/lang/String.html#compareTo-java.lang.String-) ([String](../../java/lang/String.html) anotherString)<br />按字典顺序比较两个字符串。 <br />[valueOf](../../java/lang/String.html#valueOf-long-) (long l)<br />返回 long参数的字符串 long形式。<br />[copyValueOf]() (char[] data)<br />相当于 [valueOf(char[])]() 。 返回char数组参数的特定子阵列的字符串char形式<br />[compareToIgnoreCase](../../java/lang/String.html#compareToIgnoreCase-java.lang.String-) ([String](../../java/lang/String.html) str)<br />按字典顺序比较两个字符串，忽略...差异。 <br />[contains](../../java/lang/String.html#contains-java.lang.CharSequence-) ([CharSequence](../../java/lang/CharSequence.html) s)<br />当且仅当此字符串包含指定的char值序列时才返回true。<br />[format](../../java/lang/String.html#format-java.lang.String-java.lang.Object...-) ([String](../../java/lang/String.html) format, [Object](../../java/lang/Object.html) ... args)<br />使用指定的格式字符串和参数返回格式化的字符串。 <br />[replace](../../java/lang/String.html#replace-char-char-) (char oldChar, char newChar)<br />返回从替换所有出现的导致一个字符串 oldChar在此字符串 newChar 。 |
| --- |

<a name="jRHwL"></a>
## StringBuilder

- 底层初始长度为16的可变的 char[]  char型数组，当要添加新的字符时会根据需要进行"扩容"，生成另一个长度为原来的1.5倍的新数组，若此新数组长度新加入字符长度
- sb.deleteCharAt(index)
```java
StringBuilder sb = new StringBuilder("123456");
sb.deleteCharAt(2);
System.out.println(sb);
//sout--> 12456
```
<a name="K0Vac"></a>
## StringBuilder

- 此类设计用作简易替换为StringBuffer在正在使用由单个线程字符串缓冲区的地方
- StringBuilder的主要StringBuilder是append和insert方法，它们是重载的，以便接受任何类型的数据。每个都有效地将给定的数据转换为字符串，然后将该字符串的字符附加或插入字符串构建器。append方法始终在构建器的末尾添加这些字符;insert方法将insert添加到指定点。
- [StringBuffer](../../java/lang/StringBuffer.html#StringBuffer--)() 构造一个没有字符的字符串缓冲区，初始容量为16个字符。 	
| [StringBuffer](../../java/lang/StringBuffer.html#StringBuffer--)()<br />构造一个没有字符的字符串缓冲区，初始容量为16个字符。  |
| --- |

<a name="RaYlX"></a>
## 字符串列表
List<String> CBTs = new ArrayList<>();<br />字符串列表会自动把加入的字符或是其他obj 转换为""字符串类型
<a name="c74Bw"></a>
# Arrays工具类
<a name="mIQF0"></a>
# Math工具类
Math类里的常用静态方法

- Math.abs()
- min()
- max()
<a name="wWNGw"></a>
# 日期相关类

- Date
- Localdate

<a name="OpVWD"></a>
# System、Bigdecimal、BigIntege类
<a name="ml1mD"></a>
# object类
equals方法：obj类中的该方法原本是比较两对象的地址值是否相等，即为用" == " 进行判断；但所有的类可以按照重写规则重写该方法，比较两对象的实际内容是否相等/某属性是否相等。
<a name="pALVk"></a>
# Collections类

<a name="cleXs"></a>
# Arrays类
<a name="MUwwF"></a>
## Comparable接口
Arrays.sort(list, Compartor c)---> 可以根据设计的compartor定制排序<br />/**<br />* Compares this object with the specified object for order.  Returns a<br />* negative integer, zero, or a positive integer as this object is less<br />* than, equal to, or greater than the specified object.<br />*<br />负数 == 小于, 零 ==等于, 正数 ==大于<br />* 
<a name="heYZ1"></a>
## compareTo 和 compare方法
```java
Integer.compare(int 1, int 2);
string1.compareTo(String 2);
```
<a name="i1d8V"></a>
## java比较整数类型
```java
二维数组position
Arrays.sort(points, (a, b)->Integer.compare(a[0], b[0]));
Arrays.sort(points, (a, b)->a[0]-b[0]);
当比较的数==正数最小数或最大数边缘时,Integer自带的compare
方法能够判断出大小
而直接进行相减则会出现整数溢出错误,导致运算结果出错
```
