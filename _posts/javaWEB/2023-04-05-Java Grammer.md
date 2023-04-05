---
    layout:     post
    title:      Java Grammer
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB.jpg
    catalog: 	 true
    tags:       javaWEB
---

---
    layout:     post
    title:      Java Grammer.md
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB'.jpg
    catalog: 	 true
    tags:       javaWEB
---

<a name="Nin9P"></a>
# JAVA类内部变量名
1. 成员变量/实例字段：作用范围整个类（定义在方法体和语句块之外）
   1. 实例变量：不用static修饰的成员变量，每个实例对象独有一份
   2. 静态变量/类变量：用static修饰的，每个类都有一份，可用类名.静态变量名
   3. 常量：static final修饰
2. 局部变量：作用范围方法内部
3. 属性：有独立的get/set方法的成员变量
<a name="IEErU"></a>
# 整除运算
java整除向下取整
```java
int a = 7;
int b = 2;
int c = a/b;
c = 3; c = 3.5--->3
```
<a name="kB9kL"></a>
# 概要
其实，Java最擅长的是编写如下类型的软件：<br />1、互联网：电商、P2P<br />2、企业级应用：ERP、CRM、BOS、OA

- JavaSE是java分类中的标准版，是刚接触java要学习的基础知识。
- JavaEE是java分类中的企业版，是java中的高级，涉及到的知识广泛。
- JavaME中M是Micro的缩写，用在嵌入式等电子设备中。

————————————————
<a name="A5Hzk"></a>
# toCharArray() 方法

<a name="Ktiod"></a>
# String.charAt()方法
string为不可变类型，查找索引位置字符要用charAt方法

- **charAt(index)方法中，index为需要查找的索引值**
```java
public class Test{
    public static void test(){
        String s = "www.abcd";
        syso(s.charAt(4));
    } 
}
**g*g*g***gg
    a
```

<a name="LGOE2"></a>
# 前++和后++的区别
**i++是先使用再自加**<br />**++i是先自加再使用**
```java
int index = 0;
String[] names = {"令狐冲", "张无忌", "韦小宝", "杨过", "段誉", "乔峰"};
//        System.out.println(names[index++]); //打印出令狐冲
//        System.out.println(names[index]);   //打印出张无忌   
//以上两行代码注释掉，那么以下两行代码，就会打印出张无忌
//如果以上两行代码没有注释掉，那么以下两行代码，就会打印出韦小宝
System.out.println(names[++index]);  //打印出张无忌
System.out.println(names[index]);   //打印出张无忌
}
```
<a name="c8kIE"></a>
# 编程中的左闭右开思想[a,b)
<a name="nxyLR"></a>
## 什么是左闭右开
左闭右开是一种区间表示方式，例如在整数上[3,6)表示3，4，5三个数，闭代表取值取到那个数，开代表取值取不到那个数。
<a name="w0WtQ"></a>
## 左闭右开的好处
**对于一个左闭右开区间[l，r)来说：**

1. 能表示单独一个数
- 若区间内只有一个数我们可以用像[1，2)表示1，注意[x,x]不符合数学上区间的定义(左区间比右区间大)
2. 便于统计区间内个数
- r减l正好是区间内元素的个数，对于左闭右闭区间来说r-l+1才是区间内元素 
3. 便于表示空集
- 空集可以用[x，x)表示
4. 便于切割区间
- 例如我们要在区间内找到一个切割点x，并把x左边归为一个区间，x和x右边归为一个区间则切割后的区间就可以用[l，x)和[x，r)表示
5. 和数组下标相匹配
- 对于一个从0开始的长度为n的数组来说，[0，n）正好表示这个数组的所有下标，如果用闭区间则要用[0，n-1]来表示
<a name="kFtxU"></a>
# JAVA中的特殊的数
**正无穷大，负无穷大，与任何数都不相等的数**

- 当分子、分母都为整型，分母为 0 的时候，运行会报除0异常
- 当分子、分母中有一个为浮点型，且分母为 0 或 0.0 的时候，会得到 Infinity (正无穷大) 或 -Infinity（负无穷大）
- 个人理解：

如果分子、分母中有一个是浮点型时，Java会将整型自动转为浮点型；<br />也就是说 1.0 / 0 -> 1.0 / 0.0，1 / 0.0 -> 1.0 / 0.0；<br />大家都知道浮点型是不精确的，所以0.0 并不完全等于0，是无限趋近于0；<br />而根据高数中无穷大的理解，分母无限趋近于0，分子为常数，会得到无穷大<br />原文链接：[https://blog.csdn.net/qq_34732088/article/details/80927117](https://blog.csdn.net/qq_34732088/article/details/80927117)
```java
public static void test2() {
    double d_p_i = Double.POSITIVE_INFINITY;  // 1.0 / 0.0, 正无穷大
    double d_n_i = Double.NEGATIVE_INFINITY;  // -1.0 / 0.0，负无穷大
    float f_p_i = Float.POSITIVE_INFINITY;    // 1.0f / 0.0f，正无穷大
    float f_n_i = Float.NEGATIVE_INFINITY;    // -1.0f / 0.0f，负无穷大
    System.out.println(d_p_i == f_p_i);
    System.out.println(d_n_i == f_n_i);
}
----------------------------
/*输出结果
true
true
*/
```
```java
int result = Integer.MAX_VALUE;
```
<a name="mZNba"></a>
# 解释器/编译器
简单来理解
> 解释器是Java虚拟机非常重要的一部分，它的工作就是把字节码转化为机器码并运行
> **所谓编译器，可以简单地将其理解为“翻译器”。将程序中的每条语句翻译成对应的二进制指令**

解码：字符串--->字节码<br />编码：字节码---->字符串

<a name="u6hfI"></a>
# conver int[] to Integer[]
```java
// To boxed array
Integer[] what = Arrays.stream( data ).boxed().toArray( Integer[]::new );
Integer[] ever = IntStream.of( data ).boxed().toArray( Integer[]::new );

// To boxed list
List<Integer> you  = Arrays.stream( data ).boxed().collect( Collectors.toList() );
List<Integer> like = IntStream.of( data ).boxed().collect( Collectors.toList() );
```


<a name="Kx52q"></a>
# 生成随机数Random类
生成一个范围的随机数,如：[min,max]
```java
Random random = new Random();
int s = random.nextInt(max)%(max-min+1) + min;
System.out.println(s);
```
random.nextInt(max)表示生成[0,max）之间的随机数，然后对(max-min+1)取模。<br />以生成[10,20]随机数为例，首先生成0-20的随机数，然后对(20-10+1)---**区间长度取模**得到[0-10]---0~区间长度---的随机数，然后加上min=10，最后生成的是10-20的随机数。
<a name="yw0gP"></a>
# Integer数组常用方法

<a name="qRtdz"></a>
# 得到某个英文字母在字母表的位置

1. 通过编码asci表,由于26个字母是按顺序编码
2. 所以用该字符(char类型) - 'a'字母的大小即可得到对应的位置
3. 相关String类对应方法: `s.toCharArray();`
---
    layout:     post
    title:      2023-04-05-Java Grammer
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB.jpg
    catalog: 	 true
    tags:       javaWEB
---

---
    layout:     post
    title:      Java Grammer.md
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB'.jpg
    catalog: 	 true
    tags:       javaWEB
---

<a name="Nin9P"></a>
# JAVA类内部变量名
1. 成员变量/实例字段：作用范围整个类（定义在方法体和语句块之外）
   1. 实例变量：不用static修饰的成员变量，每个实例对象独有一份
   2. 静态变量/类变量：用static修饰的，每个类都有一份，可用类名.静态变量名
   3. 常量：static final修饰
2. 局部变量：作用范围方法内部
3. 属性：有独立的get/set方法的成员变量
<a name="IEErU"></a>
# 整除运算
java整除向下取整
```java
int a = 7;
int b = 2;
int c = a/b;
c = 3; c = 3.5--->3
```
<a name="kB9kL"></a>
# 概要
其实，Java最擅长的是编写如下类型的软件：<br />1、互联网：电商、P2P<br />2、企业级应用：ERP、CRM、BOS、OA

- JavaSE是java分类中的标准版，是刚接触java要学习的基础知识。
- JavaEE是java分类中的企业版，是java中的高级，涉及到的知识广泛。
- JavaME中M是Micro的缩写，用在嵌入式等电子设备中。

————————————————
<a name="A5Hzk"></a>
# toCharArray() 方法

<a name="Ktiod"></a>
# String.charAt()方法
string为不可变类型，查找索引位置字符要用charAt方法

- **charAt(index)方法中，index为需要查找的索引值**
```java
public class Test{
    public static void test(){
        String s = "www.abcd";
        syso(s.charAt(4));
    } 
}
**g*g*g***gg
    a
```

<a name="LGOE2"></a>
# 前++和后++的区别
**i++是先使用再自加**<br />**++i是先自加再使用**
```java
int index = 0;
String[] names = {"令狐冲", "张无忌", "韦小宝", "杨过", "段誉", "乔峰"};
//        System.out.println(names[index++]); //打印出令狐冲
//        System.out.println(names[index]);   //打印出张无忌   
//以上两行代码注释掉，那么以下两行代码，就会打印出张无忌
//如果以上两行代码没有注释掉，那么以下两行代码，就会打印出韦小宝
System.out.println(names[++index]);  //打印出张无忌
System.out.println(names[index]);   //打印出张无忌
}
```
<a name="c8kIE"></a>
# 编程中的左闭右开思想[a,b)
<a name="nxyLR"></a>
## 什么是左闭右开
左闭右开是一种区间表示方式，例如在整数上[3,6)表示3，4，5三个数，闭代表取值取到那个数，开代表取值取不到那个数。
<a name="w0WtQ"></a>
## 左闭右开的好处
**对于一个左闭右开区间[l，r)来说：**

1. 能表示单独一个数
- 若区间内只有一个数我们可以用像[1，2)表示1，注意[x,x]不符合数学上区间的定义(左区间比右区间大)
2. 便于统计区间内个数
- r减l正好是区间内元素的个数，对于左闭右闭区间来说r-l+1才是区间内元素 
3. 便于表示空集
- 空集可以用[x，x)表示
4. 便于切割区间
- 例如我们要在区间内找到一个切割点x，并把x左边归为一个区间，x和x右边归为一个区间则切割后的区间就可以用[l，x)和[x，r)表示
5. 和数组下标相匹配
- 对于一个从0开始的长度为n的数组来说，[0，n）正好表示这个数组的所有下标，如果用闭区间则要用[0，n-1]来表示
<a name="kFtxU"></a>
# JAVA中的特殊的数
**正无穷大，负无穷大，与任何数都不相等的数**

- 当分子、分母都为整型，分母为 0 的时候，运行会报除0异常
- 当分子、分母中有一个为浮点型，且分母为 0 或 0.0 的时候，会得到 Infinity (正无穷大) 或 -Infinity（负无穷大）
- 个人理解：

如果分子、分母中有一个是浮点型时，Java会将整型自动转为浮点型；<br />也就是说 1.0 / 0 -> 1.0 / 0.0，1 / 0.0 -> 1.0 / 0.0；<br />大家都知道浮点型是不精确的，所以0.0 并不完全等于0，是无限趋近于0；<br />而根据高数中无穷大的理解，分母无限趋近于0，分子为常数，会得到无穷大<br />原文链接：[https://blog.csdn.net/qq_34732088/article/details/80927117](https://blog.csdn.net/qq_34732088/article/details/80927117)
```java
public static void test2() {
    double d_p_i = Double.POSITIVE_INFINITY;  // 1.0 / 0.0, 正无穷大
    double d_n_i = Double.NEGATIVE_INFINITY;  // -1.0 / 0.0，负无穷大
    float f_p_i = Float.POSITIVE_INFINITY;    // 1.0f / 0.0f，正无穷大
    float f_n_i = Float.NEGATIVE_INFINITY;    // -1.0f / 0.0f，负无穷大
    System.out.println(d_p_i == f_p_i);
    System.out.println(d_n_i == f_n_i);
}
----------------------------
/*输出结果
true
true
*/
```
```java
int result = Integer.MAX_VALUE;
```
<a name="mZNba"></a>
# 解释器/编译器
简单来理解
> 解释器是Java虚拟机非常重要的一部分，它的工作就是把字节码转化为机器码并运行
> **所谓编译器，可以简单地将其理解为“翻译器”。将程序中的每条语句翻译成对应的二进制指令**

解码：字符串--->字节码<br />编码：字节码---->字符串

<a name="u6hfI"></a>
# conver int[] to Integer[]
```java
// To boxed array
Integer[] what = Arrays.stream( data ).boxed().toArray( Integer[]::new );
Integer[] ever = IntStream.of( data ).boxed().toArray( Integer[]::new );

// To boxed list
List<Integer> you  = Arrays.stream( data ).boxed().collect( Collectors.toList() );
List<Integer> like = IntStream.of( data ).boxed().collect( Collectors.toList() );
```


<a name="Kx52q"></a>
# 生成随机数Random类
生成一个范围的随机数,如：[min,max]
```java
Random random = new Random();
int s = random.nextInt(max)%(max-min+1) + min;
System.out.println(s);
```
random.nextInt(max)表示生成[0,max）之间的随机数，然后对(max-min+1)取模。<br />以生成[10,20]随机数为例，首先生成0-20的随机数，然后对(20-10+1)---**区间长度取模**得到[0-10]---0~区间长度---的随机数，然后加上min=10，最后生成的是10-20的随机数。
<a name="yw0gP"></a>
# Integer数组常用方法

<a name="qRtdz"></a>
# 得到某个英文字母在字母表的位置

1. 通过编码asci表,由于26个字母是按顺序编码
2. 所以用该字符(char类型) - 'a'字母的大小即可得到对应的位置
3. 相关String类对应方法: `s.toCharArray();`
