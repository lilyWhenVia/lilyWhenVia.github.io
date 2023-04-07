---
    layout:     post
    title:      IO stream & api
    date:       2023-04-07
    author:     lily
    header-img: img/JAVASE.jpg
    catalog: 	 true
    tags:       JAVASE
---

1. file类：创建磁盘中指定文件或文件夹的java对象
2. 四个输入输出/字符字节流的抽象基类
   1. **InputStream/OutputStream**  **字节流byte**
   2. **Reader/Writer** **字符流char**
3. 节点流
   1. 文件节点流+File
4. 处理流
   1. 缓冲流Buffer：自带缓存区，加快流的速度
      1. BufferedInputStream
      2. BufferedOutputStream
      3. BufferedReader
      4. BufferedWriter
   2. 系统流System：标准的输出输入流
      1. sys.in：默认输入设备为键盘
      2. sys.out：默认输出设备为显示器
      3. sys.err
   3. 转换流InputStreamxxx
      1. 字符输入流InputStreamReader
      2. 字符输出流OutputStreamWirter
   4. 打印流：只有输出流
      1. PrintStream
      2. PrintWirter
   5. 数据流：传输java基本数据类型的流
      1. DataInputStream
      2. DataOutputStream
   6. 对象流（Object）：用于存储和读取基本数据类型数据或对象的处理流
      1. ObjectOutputStream ----> 实现对象的序列化
      2. ObjectInputStream    ----> 实现反序列化
   7. 任意存取对象流（RandomAccessFile）
      1. 该类实现了同时读写功能
5. NIO(new io)：新java IO类----原生io类的包装和扩展
<a name="cIH0S"></a>
## 缓冲流

1. 为了提高数据读写的速度，Java API提供了带缓冲功能的流类，在使用这些流类时，会创建一个内部缓冲区数组，缺省使用8192个字节(8Kb)的缓冲区缓冲流要“套接”在相应的节点流之上，根据数据操作单位可以把缓冲流分为： 
   1. **BufferedInputStream 和 BufferedOutputStream **
   2. **BufferedReader 和 BufferedWriter**
2.  当读取数据时，数据按块读入缓冲区，其后的读操作则直接访问缓冲区 
3.  当使用BufferedInputStream读取字节文件时，BufferedInputStream会一次性从文件中读取8192个(8Kb)，存在缓冲区中，直到缓冲区装满了，才重新从文件中读取下一个8192个字节数组。 
4.  向流中写入字节时，不会直接写到文件，先写到缓冲区中直到缓冲区写满， BufferedOutputStream才会把缓冲区中的数据一次性写到文件里。使用方法 flush()可以强制将缓冲区的内容全部写入输出流 。
5.  关闭流的顺序和打开流的顺序相反。只要关闭最外层流即可，关闭最外层流也 
6. 会相应关闭内层节点流
7.  flush()方法的使用：手动将buffer中内容写入文件 
8.  如果是带缓冲区的流对象的close()方法，不但会关闭流，还会在关闭流之前刷新缓冲区，关闭后不能再写出
<a name="sfeN8"></a>
## 转换流

- 转换流提供了在字节流和字符流之间的转换
- Java API提供了两个转换流： 
   - **InputStreamReader：将InputStream转换为Reader**
   - **OutputStreamWriter：将Writer转换为OutputStream**
-  构造器 
   - **public InputStreamReader(InputStream in) public InputSreamReader(InputStream in,String charsetName) **
   - 如： Reader isr = new InputStreamReader(System.in,”gbk”);
<a name="SdEXt"></a>
## 输入输出流

-  System.in和System.out分别代表了系统标准的输入和输出设备 
   - 默认输入设备是：键盘，输出设备是：显示器 
- System.in的类型是InputStream 
- System.out的类型是PrintStream，其是OutputStream的子类FilterOutputStream 的子类 
- 重定向：通过System类的setIn，setOut方法对默认设备进行改变。 
   - public static void **setIn**(InputStream in) 
   - public static void **setOut**(PrintStream out)
<a name="z24ad"></a>
## 打印流

- 实现将**基本数据类型**的数据格式转化为**字符串**输出 
- 打印流：**PrintStream**和**PrintWriter **提供了一系列重载的print()和println()方法，用于多种数据类型的输出 
- PrintStream和PrintWriter的输出不会抛出IOException异常 
- PrintStream和PrintWriter有自动flush功能 
- PrintStream 打印的所有字符都使用平台的默认字符编码转换为字节。 在需要写入字符而不是写入字节的情况下，应该使用 PrintWriter 类。 
-  System.out返回的是PrintStream的实例
<a name="dnpct"></a>
## 数据流

- 操作Java语言的基本数据类型和String的数据
- 数据流有两个类：(用于读取和写出基本数据类型、String类的数据） 
-  **DataInputStream **和 **DataOutputStream **
-  **分别“套接”在 InputStream 和 OutputStream 子类的流上 **
- **DataInputStream中的方法 **
- boolean readBoolean()     byte readByte() 
- char readChar()                 float readFloat() 
- double readDouble()        short readShort() 
- long readLong()                int readInt() 
- String readUTF()                void readFully(byte[] b) 
- **DataOutputStream中的方法 **
-  将上述的方法的read改为相应的write即可
<a name="HCjaF"></a>
## 对象流

- **ObjectInputStream和OjbectOutputSteam **
-  用于存储和读取**基本数据类型**数据或**对象**的处理流。它的强大之处就是可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。 
- **序列化：**用ObjectOutputStream类**保存**基本类型数据或对象的机制 
- **反序列化：**用ObjectInputStream类**读取**基本类型数据或对象的机制 
- ObjectOutputStream和ObjectInputStream不能序列化static和transient修饰的成员变量
<a name="rwNcv"></a>
### 序列化
**_说明：_**

1. **对象序列化机制**允许把内存中的Java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。//当其它程序获取了这种二进制流，就可以恢复成原来的Java对象 
2. 序列化的好处在于可将任何实现了Serializable接口的对象转化为**字节数据**，使其在保存和传输时可被还原 
3. 序列化是 RMI（Remote Method Invoke – 远程方法调用）过程的参数和返回值都必须实现的机制，而 RMI 是 JavaEE 的基础。因此序列化机制是JavaEE 平台的基础 
4. 如果需要让某个对象支持序列化机制，则必须让对象所属的类及其属性是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一。否则，会抛NotSerializableException异常 
   1. **Serializable **
   2. Externalizable
5. 凡是实现Serializable接口的类都有一个表示序列化版本标识符的静态变量：**private static final long serialVersionUID; **
6. serialVersionUID用来表明类的不同版本间的兼容性。简言之，其目的是以序列化对象进行版本控制，有关各版本反序列化时是否兼容。 
7. 如果类没有显示定义这个静态常量，它的值是Java运行时环境根据类的内部细节自动生成的。若类的实例变量做了修改，serialVersionUID 可能发生变化。建议显式声明。 

**序列化过程**

- **创建一个 ObjectOutputStream **
- **调用 ObjectOutputStream 对象的 writeObject(对象) 方法输出可序列化对象 **
- **注意写出一次，操作flush()一次**
<a name="dCUB1"></a>
## **随机存取文件流**

1. 它实现了DataInput、DataOutput这两个接口，也就意味着这个类既可以读也可以写。 
2. RandomAccessFile 类支持 “随机访问” 的方式，程序可以直接跳到文件的任意地方来**读、写文件 **
3. 支持只访问文件的部分内容 
4. 可以向已存在的文件后追加内容 
5. RandomAccessFile 对象包含一个记录指针，用以标示当前读写处的位置。 
6. RandomAccessFile 类对象可以自由移动记录指针： 
   1. long getFilePointer()：获取文件记录指针的当前位置 
   2. void seek(long pos)：将文件记录指针定位到 pos 位置

**构造器 **

1. public **RandomAccessFile**(File file, String mode) 
2. public **RandomAccessFile**(String name, String mode) 
3. 创建 RandomAccessFile 类实例需要指定一个 mode 参数，该参数指定 RandomAccessFile 的访问模式： 
   1. **r: 以只读方式打开 **
   2. **rw：打开以便读取和写入 **
   3. **rwd:打开以便读取和写入；同步文件内容的更新 **
   4. **rws:打开以便读取和写入；同步文件内容和元数据的更新 **
4. 如果模式为只读r。则不会创建文件，而是会去读取一个已经存在的文件，如果读取的文件不存在则会出现异常。 如果模式为rw读写。如果文件不存在则会去创建文件，如果存在则不会创建
