Netty学习(一)

## 1. RMI

> rmi: remote method invocatin,只针对Java
>
> client: stub
>
> server: skeleton
>
> 序列化与反序列化也叫做：编码与解码

## 2. RPC

> rpc: remote procedure call,远程过程调用，很多RPC框架是跨语言的
>
> * 定义一个接口说明文件：描述了对象（结构休）、对象成员、接口方法等一系统信息。
> * 通过RPC框架所提供的编译器，将接口说明文件编译成具体语言文件
> * 在客户端与服务器端分别引入RPC编译器所生成的文件，即可像调用本地方法一样去调用远程方法

## 3. Google ProtoBuf

## 4. Apache Thrift

> * Thrift最初由FaceBook研发，**主要用于各个服务之间的RPC通信，支持跨语言**，常用的语言比如C++,Java,Python,PHP,Ruby,Erlang,Perl,Haskell,C#,Cocoa,JavaScript,Node.js,Smalltalk,and OCami都支持
> * Thrift是一个典型的CS（客户端/服务端）结构，客户端和服务端可以使用不同的语言开发。既然客户端和服务端能使用不同的语言开发，那么一定就要有一种中间语言来关联客户端和服务端的语言，这种语言就是`IDL(Interface Description Language)`
> * Thrift不支持无符号类型，因为很多编程语言不存在无符号类型，比如Java

### 4.1 Thrift数据类型

> * byte: 有符号字节
> * i16: 16位有符号整数
> * i32: 32位有符号整数
> * i64: 64位有符号整数
> * double: 64位浮点数
> * string: 字符串

### 4.2 Thrift容器类型

> * 集合中的元素可以是除了service之外的任何类型，包括exception
> * list: 一系统由T类型的数据组成的有序列表，元素可以重复
> * set: 一系统由T类型的数据组成的无序集合，元素不可重复
> * map: 一个字典结构，key为K类型，value为V类型，相当于Java中的hashMap

### 4.3 Thrift工作原理

> * 如何实现多语言之间的通信？
>   * 数据传输使用socket(多种语言均支持)，数据再以特定的格式(String等)，发送，接收方语言进行解析
>   * 定义thrift的文件，由thrift文件(IDL)生成双方语言的接口、model，在生成model以及接口中会有解码编码的代码

### 4.4 结构体（struct）

> * 就像C语言一样，Thrift支持struct类型，目的就是将一些数据聚合到一起，方便传输管理。struct的定义形式如下：
>
>   ```c++
>   struct People{
>       1:string name;
>       2:i32 age;
>       3:string gender;
>   }
>   ```

### 4.5 枚举（enum）

> * 枚举的定义形式和Java的Enum定义类似
>
>   ```java
>   enum Gender{
>       MALE,
>       FEMALE
>   }
>   ```

### 4.6 异常（exception）

> * Thrift支持自定义异常，规则与struct一样
>
>   ```c++
>   exceptin RequestException{
>       1:i32 code;
>       2:string resason;
>   }
>   ```

### 4.7 服务（service）

> * Thrift定义服务相当于Java中创建Interface一样，创建的service经过代码生成命令之后就会生成客户端和服务端框架代码。定义形式如下：
>
>   ```java
>   service HelloWorldService{
>       //service中定义的函数，相当于Java interface中定义的方法//
>       string doAction(1:string name,2:i32 age);
>   }
>   ```

### 4.8 类型定义

> * Thrift支持类似C++一样和typedef 定义：
>
>   ```c++
>   typedef i32 int
>   typedef i64 long
>   ```

### 4.9 常量（const）

> * thrift也支持常量定义，使用const关键字：
>
>   ```c++
>   const i32 MAX_RETRIES_TIME=10
>   const string MY_WEBSITE="http://facebook.com"
>   ```

### 4.10 命名空间

> * Thrift的命名空间相当于Java中的package的意思，主要目的是组织代码。thrift使用关键字namespace定义命名空间：
>
>   ```c++
>   namespace java com.test.thrift.demo
>   ```
>
> * 格式是： namespace 语言名  路径

### 4.11 文件包含

> * Thrift也支持文件包含，相当于C/C++中的include,Java中的import。使用关键字include定义：
>
>   ```c++
>   include "global.thrift"
>   ```

### 4.12 注释

> * Thrift注释方式支持shell风格的注释，支持C/C++风格的注释，即#和//开头的语句都当做注释，/**/包裹的语句也是注释。

### 4.13 可选和必选

> * Thrift提供了两个关键字required,optional,分别用于表示对应的字段是必填的还是可选的
>
>   ```c++
>   struct People{
>       1:required string name;
>       2:optional i32 age;
>   }
>   ```

### 4.14  生成代码

> * 了解了如何定义thrift文件之后，我们需要用定义好的thrift文件生成我们需要的目标语言的源码
>
> * 首先需要定义thrift接口描述文件
>
> * 参见 data.thrift
>
>   ```c++
>   namespace java thrift.generated
>   typedef i16 short
>   typedef i32 int
>   typedef i64 long
>   typedef bool boolean
>   typedef string String
>   
>   struct Person{
>       1: optional String username,
>       2: optional int age,
>       3: optional boolean married
>   }
>   exception DataException{
>       1: optional String message,
>       2: optional String callStack,
>       3: optional String date
>   }
>   service PersonService{
>       Person getPersonByUserName(1: required String username) throws (1: DataException dataException),
>       void savePerson(1: required Person person) throws (1: DataException dataException)
>   }
>   ```
>
> * `thrift --gen java data.thrift`

### 4.15 Thrift传输协议

> * `TBinaryProtocal`  二进制格式
> * `TCompactProtocol`   压缩格式 
> * `TJSONProtocol`   JSON格式 
> * `TSimpleJSONProtocol`  提供JSON只写协议，生成的文件很容易通过脚本语言解析
> * `TDebugProtocol`  使用易懂的可读的文本格式，以便于debug

### 4.16 Thrift数据传输方式

> * `TSocket`  阻塞式socket
> * `TFramedTransport`  以frame为单位进行传输，非阻塞式服务中使用
> * `TFileTransport`  以文件形式进行传输
> * `TMemoryTransport`  将内存用于I/O，Java实现时内部实际使用了简单的ByteArrayOutputStream
> * `TZlibTransport`  使用zlib进行压缩，与其他传输方法联合使用。当前无Java实现

### 4.17 Thrift支持的服务模型

> * `TSimpleServer`  简单的单线程服务模型，常用于测试
> * `TThreadPoolServer`  多线程服务模型，使用标准的阻塞式IO
> * `TNonblockingServer` 多线程服务模型，使用非阻塞式ＩＯ（需使用`TFramedTransport`数据传输方式）
> * `THsHaServer`  THsHa引入线程池去处理，其模型把读写任务放到线程池去处理;Half-sync/Half-async的处理模式，Half-aysnc是在处理IO事件上（accept/read/write io）,Half-sync用于handler对rpc的同步处理,需使用`TFramedTransport`数据传输方式）

### 4.18 Thrift对多语言的支持

> * Python作为Client
> * Java作为Server

## 5. gRPC

## 6. Java IO回顾

### 6.1 流的概念

> * Java程序通过流来完成输入/输出。流是生产或消费信息的抽象。流通过Java的输入/输出系统与物理设备链接。尽管与他们链接的物理设备不尽相同，所有流的行为具有同样的方式。这样，相同的输入/输出类和方法适合于所有类型的外部设备。这意味着一个输入流能够抽象多种不同类型的输入：从磁盘文件，从键盘或从网络套接字。同样，一个输出流可以输出到控制台，磁盘文件或相连的网络。流是处理输入/输出的一个洁净的方法，例如它不需要代码理解键盘和网络的不同。Java中流的实现是在java.io包定义的类层次结构内部的。

### 6.2 输入/输出流概念

> * 输入/输出时，数据在通信通道中流动。所谓”数据流(stream)“指的是所有数据通信通道中，数据的起点和终点。信息的通道就是一个数据流。只要是数据从一个地方流到另外一个地方，这种数据流动的通道都可以称为数据流。
> * 输入/输出是相对于程序来说的。程序在使用数据时所扮演的角色有两个：一个是源，一个是目的。**若程序是数据流的源，即数据的提供者，这个数据对程序来说就是一个”输出数据流“（数据从程序流出）。若程序是数据流的终点，这个数据流对程序而言就是一个”输入数据流“（数据从程序外流向程序）**

### 6.3 输入/输出类

> * 在java.io包中提供了60多个类(流).
> * 从功能上分为两个大类：输入流和输出流
> * 从流结构上可分为**字节流（以字节为处理单位或称面向字节）和字符流(以字符为处理单位或称面向字符)。**
> * **字节流的输入流和输出流是InputStream和OutputStream这类两个抽象类**，**字节流的输入输出操作由这两个类的子类实现。**
> * 字符流是Java 1.1版后新增的以字符为单位进行输入输出处理的流。**字符流输入输出的基础是抽象类Reader和Writer。**

### 6.4 字节流和字符流

> * Java2 定义了两种类型的流：**字节流和字符流**。字节流(byte stream)为处理字节的输入和输出提供了方便的方法。例如使用字节流读取或写入二进制数据。字符流(character stream)为字符的输入和输出提供了方便。它们采用了统一的编码标准。**因而可以国际化。当然，在某些场合，字符流比字节流更有效**。
> * Java的原始版本(Java 1.0)不包括字符流，因此所有的输入和输出都是以字节为单位的。Java 1.1中加入了字符流的功能
> * 需要声明：**在最底层，所有的输入/输出都是字节形式的。基于字符的流只为处理字符提供方便有效的方法。**

| Byte Streams | Character Streams |
| :----------: | :---------------: |
| InputStream  |      Reader       |
| OutputStream |      Writer       |

### 6.5 流的分类 

> * 两种基本的流是：输入流(InputStream)和输出流(OutputStream)。可从中读出一系列字节的对象称为输入流。而能向其中写入一系统字节的对象称为输出流。
> * **节点流：从特定的地方读写的流类，例如：磁盘或一块内存区域**
> * **过滤流：使用节点流作为输入或输出。过滤流是使用一个已经存在的输入或输出流连接创建的**

### 6.6 输入流/输出流的使用

> * 读数据的逻辑为：
>
>   * open a stream 
>
>   * while more information
>
>   * add information
>
>   * ose the stream
>
> * 写数据的逻辑为:
>
>   * open a  stream
>   * while more information
>   * write information
>   * close the stream

### 6.7 java.io包中 InputStream的类层次

> * InputStream中包含一套字节输入流需要的方法，可以完成最基本的**从输入流读入数据的功能**。当Java程序需要外设的数据时，可根据数据的不同形式，创建一个适当的InputStream子类类型的对象来完成与该外设的连接，然后再调用执行这个流类对象的特定输入方法来实现对相应外设的输入操作。
> * InputStream类子类对象自然也继承了InputStream类的方法，常用的方法有:读数据的方法read(),获取输入流字节数的方法available(),定位输入位置指针的方法skip(),reset(),mark()等。

![](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200218154130897.png)

### 6.8 OutputStream

> * 三个基本的写方法
>   * `abstact void write(int b)`：往输出流中写拉一个字节
>   * `void write(byte[] b)`：往输出流中写入数组b中所有的字节
>   * `void write(byte[] b,int off,int len)`：往输出流中写入数组b中从偏移量off开始的len个字节的数据
>   * void flush()：刷新输出流，强制缓冲区中的输出字节被写出
>   * void close()：关闭输出流，释放和这个流相关的系统资源
> * OutputStream是定义了流式字节输出模式的抽象类。该类的所有方法返回一个void值，并且在出错的情况下引发一个IOException 异常.。

![image-20200218155838330](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200218155838330.png)

### 6.9 过滤流

> * 在`InputStream`类和`OutputStream`类子类中，`FilterInputStream` 和`FilterOutputStream`过滤流抽象类又派生出`DataInputStream`和`DataOutputStream`数据输入输出流类等子类。

### 6.10 I/O流的链接 

> ![image-20200218160313624](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200218160313624.png)

### 6.11 Reader的类层次

![image-20200218161734721](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200218161734721.png)

### 6.12 Writer的类层次

![image-20200218161807333](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200218161807333.png)

### 6.13 Java I/O库的设计原则

> * Java的I/O库提供了一个称做链接的机制，可以将一个流与另一个流首尾相接，形成一个流管道的链接。这种机制实际上是一种被称为**Decorator（装饰）设计模式的应用**。
> * 通过流的链接，可以动态的增加流的功能，而这种功能的增加是通过组合一些流的基本功能而动态获取的。
> * 我们要获取一个I/O对象,往往需要产生多个I/O对象，这也是Java I/O库不太容易掌握的原因，但在I/O库中Decorator模式的运用，给我们提供了实现上的灵活性。

## 7. 装饰模式(Decorator)

> * 装饰模式又名包装（Wrapper）模式
> * 装饰模式以对客户端透明的方式**扩展对象的功能**，是继承关系的一个替代方案
> * 装饰模式以对客户透明的方式**动态**的给一个对象加上更多的责任。换言之，客户端并不会觉得对象在装饰前和装饰后有什么不同 
> * **装饰模式可能在不创建更多子类的情况下，将对象的功能加以扩展**
> * 装饰模式**把客户端的调用委派到被装饰类**。装饰模式的关键在于这种扩展完全是透明的。
> * 装饰模式是在不必改变原类文件和使用继承的情况下，动态的扩展一个对象的功能。它是通过创建一个包装对象，也就是装饰来包裹真实的对象。

### 7.1 装饰模式的角色

> * 抽象构建角色（Componment）:给出一个抽象接口，以规范准备接收附加责任的对象
> * 具体构建角色（Concrete Componment）:定义一个将要接收附加责任的类
> * 装饰角色（Decorator）:持有一个构建(Componment)对象的引用，并定义一个与抽象构件接口一致的接口
> * 具体装饰角色（Concrete Decorator）:负责给构件对象”贴上“附加的责任

### 7.2 装饰模式的特点

> * **装饰对象和真实对象有相同的接口。这样客户端对象就可以以和真实对象相同的方式和装饰对象交互**
> * 装饰对象包含一个**真实对象的引用**（reference）
> * 装饰对象接收所有来自客户端的请求。它把这些请求转发给真实的对象
> * **装饰对象可以在转发这些请求以前或以后增加一些附加功能**。这样就确保了在运行时，不用修改给定对象的结构就可以在外部增加附加的功能。在面向对象的设计中，通常是通过继承来实现对给定类的功能扩展。

## 8. NIO

> * java.io中最为核心的一个概念是流（Stream），面向流的编程。Java中，一个流要么是输入流，要么是输出流，不可能同时既是输入流又是输出流。
> * java.nio中拥有三个核心概念：Selector、Channel和Buffer。在 java.nio中，我们是面向块（block）或是缓冲区（buffer）编程的。Buffer本身就是一块内存，底层实现上，它实际上是个数组。数据的读、写都是通过Buffer来实现的。
> * 除了数组之外，Buffer还提供了对于数据 的结构化访问方式，并且可以追踪到系统的读写过程。
> * Java中的7种原生数据类型都有各自对应的Buffer类型，如IntBuffer,LongBuffer,ByteBuffer及CharBuffer等,但并没有BooleanBuffer类型。
> * Channel指的是可以向其写入数据或是从中读取数据的对象，它类似于在java.io中的Stream。
> * 所有数据的读写都是通过Buffer来进行的，永远不会出现直接向Channel写入数据的情况，或是直接从Channel读取数据的情况。
> * 与Stream不同的是，Channel是双向的，一个流只能是InpuStream或是OutputStream ,Channel打开后则可以进行读取、写入或是读写。
> * 由于Channel是双向的，因此它能更好的反映出底层操作系统的真实情况；在LInux系统中，底层操作系统的通道就是双向的。

### 8.1 NIO Buffer中的3个重要属性

> 1. position：
> 2. limit：
> 3. capacity：
> 4. 0 <= mark <= position <= limit <= capacity

### 8.2 NIO读取文件三个步骤

> 1. 从FileInputStream获取到FileChannel对象
> 2. 创建Buffer
> 3. 将数据从Channel读取到Buffer

### 8.3 绝对方法与相对方法的含义

> 相对方法：limit值与position值会在操作时被考虑到。
>
> 绝对方法：完全忽略limit值与position值

## 9. 字符集编码

> * ASCII (American Standard Code For Information Interchange,美国信息交换标准代码),7 bit 来表示一个字符，共计可以表示128种字符
> * ISO-8859-1 8 bit 表示 一个字符，即用一个字节(byte),(8 bit)来表示一个字符，共计可以表示256个字符,**在ASCII基础上扩展**
> * **gb2312 两个字节表示一个汉字**
> * gbk 是gb2312的超集
> * gb18030 最完整的汉字表示形式
> * big5 针对于繁体中文的编码
> * **unicode 表示全世界语言的编码,采用了两个字节来表示一个字符,但不适合于存储，存在空间浪费，特别是针对英美国家**
> * utf (Uniocde Translation Format) 
> * **unicode是一种编码方法，而utf则是一种存储方式;UTF-8,UTF-16是Unicode的实现方式之一**
> * UTF-16LE (littel endian 小端),定长字节表示，使用两个字节表示 
> * UTF-16BE (big endian 大端),定长字节表示，使用两个字节表示 
> * Zero Width No-Break Space ,0xFEFF(BE) ,0xFFFE(LE)
> * **UTF-8 变长的字节表示形式,兼容于ASCII和ISO-8859-1,一般来说UTF-8会通过三个字节来表示一个中文**
> * BOM(Byte Order Mark) 字节序标记

## 10. 零拷贝

### 10.1 传统IO的拷贝

> * **会有四次的上下文空间切换(即从用户空间到内核空间，内核空间到用户空间)和四次的数据拷贝**
> * 用户空间的buffer实际上是一个中转，或者临时数据的载体而已。并不对数据进行任何修改
>
> ![image-20200301191713708](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200301191713708.png)

### 10.2 nio中的拷贝

> * **两次的上下文空间切换且并没有从内核空间到用户空间的数据拷贝，但在内核空间会有三次的数据拷贝，是从kernel buffer到目标socket buffer的拷贝**
>
> ![image-20200301192059837](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200301192059837.png)

**改进**

> * **通过scatter/gather方式避免了一次内核空间的一次数据拷贝**
> * Linux 2.1 版本 提供了 sendFile 函数，其基本原理如下：**数据根本不经过用户态，直接从内核缓冲区进入到 Socket Buffer**，同时，由于和用户态完全无关，就减少了一次上下文切换
> * Linux 在 2.4 版本中，做了一些修改，**避免了从内核缓冲区拷贝到 Socket buffer 的操作，直接拷贝到协议栈，从而再一次减少了数据拷贝**
>
> ![image-20200301192923251](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200301192923251.png)
>
> * 当文件从hard drive通过DMA copy 方式拷贝到内核缓冲，就不需要再从内核缓存拷贝到socket buffer当中去，而是通过写一份**文件描述符信息(包含 内核缓冲的地址和内核缓存中数据的长度)**到socket buffer当中，所以当协议引擎读取数据的时候，会从两个地方同时读取即(kernel buffer和socket buffer)中读取，而kernel buffer中真正存放数据的地方，socket buffer中描述的是数据存放地方的地址和长度。即这是一种gather操作，实现了真正的零拷贝。

![image-20200302200639255](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200302200639255.png)

53

