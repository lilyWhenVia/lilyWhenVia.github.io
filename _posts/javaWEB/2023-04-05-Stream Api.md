---
    layout:     post
    title:      Stream Api
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB.jpg
    catalog: 	 true
    tags:       javaWEB
---

---
    layout:     post
    title:      Stream Api.md
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB'.jpg
    catalog: 	 true
    tags:       javaWEB
---

<a name="G6alI"></a>
# 简介
1. Stream 和 Collection 集合的区别：
   1. ：Collection 是一种静态的内存数据结构，而 Stream 是有关计算的。前者是主要面向内存，存储在内存中，后者主要是面向 CPU，通过 CPU 实现计算。
2. **Stream到底是什么**
   1. 是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。 
   2. **“集合讲的是数据，Stream讲的是计算！”**
   3. ①Stream 自己不会存储元素。 
   4. ②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。 
   5. ③Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。
<a name="vqA5a"></a>
# stream流的使用

-  **1- 创建 Stream **
   - 一个数据源（如：集合、数组），获取一个流 
-  **2- 中间操作 **
   - 一个中间操作链，对数据源的数据进行处理 
-  **3- 终止操作(终端操作) **
   - 一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用
<a name="ocJ9t"></a>
# 创建stream流

   1. **方式一：通过集合**
      1. **default Stream<E> stream() : 返回一个顺序流 **
      2. **default Stream<E>parallelStream() : 返回一个并行流**
```java
Arrays.asList(1, 2, 3, 4, 5);
```

   2. **方式二：通过数组**
      1. **static <T> Stream<T> stream(T[] array): 返回一个流**
      2. 重载形式，能够处理对应基本类型的数组：** **
         1.  public static IntStream stream(int[] array)
         2.  public static LongStream stream(long[] array) 
         3.  public static DoubleStream stream(double[] array)
   3. **方式三：通过Stream的of()**
      1. **public static<T> Stream<T> of(T... values) : 返回一个流**
   4. **方式四：创建无限流**
      1. **迭代 public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) **
      2.  **生成 public static<T> Stream<T>generate(Supplier<T> s) **
<a name="TpdLW"></a>
# 中间操作

- 多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止 操作，**在终止操作时一次性全部处理，称为“惰性求值”**。
- 中间操作方法链中调用的方法都有返回流.
1. **筛选与切片 filter**
   1. **filter(Predicate p) **接收 Lambda ， 从流中排除某些元素
   2. **distinct() **筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素
   3. **limit(long maxSize) **截断流，使其元素不超过给定数量
   4. **skip(long n) **跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一 个空流。与 limit(n) 互补
2. **映射Map**
   1. **map(Function f) **接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素
   2. **mapToDouble(ToDoubleFunction  f)**接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的DoubleStream。 
   3. **mapToInt(ToIntFunction f)**接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 IntStream。
   4. **mapToLong(ToLongFunction f) **接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 LongStream。 
   5. **flatMap(Function f) **接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流.
3. **排序 sort**
   1. **sorted() **产生一个新流，其中按自然顺序排序 
   2. **sorted(Comparator com) **产生一个新流，其中按比较器顺序排序
<a name="hA98r"></a>
# 终止操作

- 终端操作会从流的流水线生成结果。其结果可以是任何不是流的值，例如：**List、Integer，甚至是 void** 。 
- 流进行了终止操作后，不能再次使用。
- **想要重新对原始数据进行流操作,需要获得新流**
1. **匹配与查找**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884418547-8f9606da-b5db-44a3-9bde-595bf454cb26.png#averageHue=%23b2d0dd&clientId=ud5f187d6-a094-4&from=paste&height=147&id=u51d2b1aa&name=image.png&originHeight=220&originWidth=464&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52141&status=done&style=none&taskId=ud02183a6-5730-4b60-a646-623dd798a6e&title=&width=309.3333333333333)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884430068-5368d88f-7f81-4853-9cf0-8a3df750f547.png#averageHue=%23bdd0e4&clientId=ud5f187d6-a094-4&from=paste&height=139&id=u8f5e0c0f&name=image.png&originHeight=208&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55446&status=done&style=none&taskId=u98a14dab-39f1-432e-adda-7a26046b210&title=&width=402)

2. **归约**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884446704-8fc653f8-2c80-4a74-8a31-cffa9153ef48.png#averageHue=%23c8d0bc&clientId=ud5f187d6-a094-4&from=paste&height=179&id=u53c245df&name=image.png&originHeight=268&originWidth=592&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56602&status=done&style=none&taskId=ucafb3463-cb02-42e8-82a9-75d3baebaf8&title=&width=394.6666666666667)

3. **收集**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884463520-16213355-cd39-47d1-b55c-ff404738c1c8.png#averageHue=%23d2d89c&clientId=ud5f187d6-a094-4&from=paste&height=145&id=ua8734620&name=image.png&originHeight=218&originWidth=607&originalType=binary&ratio=1&rotation=0&showTitle=false&size=62684&status=done&style=none&taskId=ua52f922b-d40d-43bb-8c6f-6a17c1dd1ff&title=&width=404.6666666666667)

<a name="CSYPB"></a>
# IntStream 类

1. 给int数组按绝对值排序
```java
int[] nums = {-1, -2, 8, 2, 4};
IntStream.of(nums).
    boxed().
    sorted((o1, o2)->Math.abs(o1) - Math.abs(o2)).
    mapToInt(Integer::intValue).toArray();
```
---
    layout:     post
    title:      2023-04-05-Stream Api
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB.jpg
    catalog: 	 true
    tags:       javaWEB
---

---
    layout:     post
    title:      Stream Api.md
    date:       2023-04-05
    author:     lily
    header-img: img/javaWEB'.jpg
    catalog: 	 true
    tags:       javaWEB
---

<a name="G6alI"></a>
# 简介
1. Stream 和 Collection 集合的区别：
   1. ：Collection 是一种静态的内存数据结构，而 Stream 是有关计算的。前者是主要面向内存，存储在内存中，后者主要是面向 CPU，通过 CPU 实现计算。
2. **Stream到底是什么**
   1. 是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。 
   2. **“集合讲的是数据，Stream讲的是计算！”**
   3. ①Stream 自己不会存储元素。 
   4. ②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。 
   5. ③Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。
<a name="vqA5a"></a>
# stream流的使用

-  **1- 创建 Stream **
   - 一个数据源（如：集合、数组），获取一个流 
-  **2- 中间操作 **
   - 一个中间操作链，对数据源的数据进行处理 
-  **3- 终止操作(终端操作) **
   - 一旦执行终止操作，就执行中间操作链，并产生结果。之后，不会再被使用
<a name="ocJ9t"></a>
# 创建stream流

   1. **方式一：通过集合**
      1. **default Stream<E> stream() : 返回一个顺序流 **
      2. **default Stream<E>parallelStream() : 返回一个并行流**
```java
Arrays.asList(1, 2, 3, 4, 5);
```

   2. **方式二：通过数组**
      1. **static <T> Stream<T> stream(T[] array): 返回一个流**
      2. 重载形式，能够处理对应基本类型的数组：** **
         1.  public static IntStream stream(int[] array)
         2.  public static LongStream stream(long[] array) 
         3.  public static DoubleStream stream(double[] array)
   3. **方式三：通过Stream的of()**
      1. **public static<T> Stream<T> of(T... values) : 返回一个流**
   4. **方式四：创建无限流**
      1. **迭代 public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) **
      2.  **生成 public static<T> Stream<T>generate(Supplier<T> s) **
<a name="TpdLW"></a>
# 中间操作

- 多个中间操作可以连接起来形成一个流水线，除非流水线上触发终止 操作，**在终止操作时一次性全部处理，称为“惰性求值”**。
- 中间操作方法链中调用的方法都有返回流.
1. **筛选与切片 filter**
   1. **filter(Predicate p) **接收 Lambda ， 从流中排除某些元素
   2. **distinct() **筛选，通过流所生成元素的 hashCode() 和 equals() 去除重复元素
   3. **limit(long maxSize) **截断流，使其元素不超过给定数量
   4. **skip(long n) **跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一 个空流。与 limit(n) 互补
2. **映射Map**
   1. **map(Function f) **接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素
   2. **mapToDouble(ToDoubleFunction  f)**接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的DoubleStream。 
   3. **mapToInt(ToIntFunction f)**接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 IntStream。
   4. **mapToLong(ToLongFunction f) **接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的 LongStream。 
   5. **flatMap(Function f) **接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流.
3. **排序 sort**
   1. **sorted() **产生一个新流，其中按自然顺序排序 
   2. **sorted(Comparator com) **产生一个新流，其中按比较器顺序排序
<a name="hA98r"></a>
# 终止操作

- 终端操作会从流的流水线生成结果。其结果可以是任何不是流的值，例如：**List、Integer，甚至是 void** 。 
- 流进行了终止操作后，不能再次使用。
- **想要重新对原始数据进行流操作,需要获得新流**
1. **匹配与查找**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884418547-8f9606da-b5db-44a3-9bde-595bf454cb26.png#averageHue=%23b2d0dd&clientId=ud5f187d6-a094-4&from=paste&height=147&id=u51d2b1aa&name=image.png&originHeight=220&originWidth=464&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52141&status=done&style=none&taskId=ud02183a6-5730-4b60-a646-623dd798a6e&title=&width=309.3333333333333)![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884430068-5368d88f-7f81-4853-9cf0-8a3df750f547.png#averageHue=%23bdd0e4&clientId=ud5f187d6-a094-4&from=paste&height=139&id=u8f5e0c0f&name=image.png&originHeight=208&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&size=55446&status=done&style=none&taskId=u98a14dab-39f1-432e-adda-7a26046b210&title=&width=402)

2. **归约**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884446704-8fc653f8-2c80-4a74-8a31-cffa9153ef48.png#averageHue=%23c8d0bc&clientId=ud5f187d6-a094-4&from=paste&height=179&id=u53c245df&name=image.png&originHeight=268&originWidth=592&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56602&status=done&style=none&taskId=ucafb3463-cb02-42e8-82a9-75d3baebaf8&title=&width=394.6666666666667)

3. **收集**

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1238904/1669884463520-16213355-cd39-47d1-b55c-ff404738c1c8.png#averageHue=%23d2d89c&clientId=ud5f187d6-a094-4&from=paste&height=145&id=ua8734620&name=image.png&originHeight=218&originWidth=607&originalType=binary&ratio=1&rotation=0&showTitle=false&size=62684&status=done&style=none&taskId=ua52f922b-d40d-43bb-8c6f-6a17c1dd1ff&title=&width=404.6666666666667)

<a name="CSYPB"></a>
# IntStream 类

1. 给int数组按绝对值排序
```java
int[] nums = {-1, -2, 8, 2, 4};
IntStream.of(nums).
    boxed().
    sorted((o1, o2)->Math.abs(o1) - Math.abs(o2)).
    mapToInt(Integer::intValue).toArray();
```
