# Netty学习(韩一)

## 1. I/O 模型

> I/O 模型简单的理解：就是用什么样的通道进行数据的发送和接收，很大程度上决定了程序通信的性能
> Java共支持3种网络编程模型/IO模式：BIO、NIO、AIO
> 
>
> * Java BIO ： 同步并阻塞(传统阻塞型)，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销 
> * Java NIO ： 同步非阻塞，服务器实现模式为一个线程处理多个请求(连接)，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求就进行处理 
> * Java AIO(NIO.2) ： 异步非阻塞，AIO 引入异步通道的概念，采用了 Proactor 模式，简化了程序编写，有效的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用

### 1.1  BIO,NIO,AIO的区别

> 1. Java BIO ： 同步并阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。
> 2. Java NIO ： 同步非阻塞，服务器实现模式为一个请求一个线程，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
> 3. Java AIO(NIO.2) ： 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理

### 1.2 BIO 基本介绍

> Java BIO 就是传统的java io 编程，其相关的类和接口在 java.io 
> BIO(blocking I/O) ： **同步阻塞**，服务器实现模式为**一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理**，如果这个连接不做任何事情会造成不必要的线程开销，可以通过线程池机制改善(实现多个客户连接服务器)。即：一个线程对应处理一个客户端。
>
> BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序简单易理解。

![bio](C:\Users\Administrator\Desktop\笔记\韩Netty\images\bio.jpg)

#### 1.2.1 BIO编程流程

> 1. 服务器端启动一个ServerSocket
> 2. 客户端启动Socket对服务器进行通信，默认情况下服务器端需要对每个客户建立一个线程与之通讯
> 3. 客户端发出请求后, 先咨询服务器是否有线程响应，如果没有则会等待，或者被拒绝
> 4. 如果有响应，客户端线程会等待请求结束后，在继续执行

#### 1.2.2 BIO的缺陷

> * 每个请求都需要创建独立的线程，与对应的客户端进行数据 Read，业务处理，数据 Write 。
> * 当并发数较大时，需要创建大量线程来处理连接，系统资源占用较大。
> * 连接建立后，如果当前线程暂时没有数据可读，则线程就阻塞在 Read 操作上，造成线程资源浪费

### 1.3 NIO 基本介绍

> 1. Java NIO 全称 java non-blocking IO，是指 JDK 提供的新 API。从 JDK1.4 开始，Java 提供了一系列改进的输入/输出的新特性，被统称为 NIO(即 New IO)，是同步非阻塞的
> 2. NIO 相关类都被放在 java.nio 包及子包下，并且对原 java.io 包中的很多类进行改写。
>
> 3. **NIO 有三大核心部分：Channel(通道)，Buffer(缓冲区), Selector(选择器)** 
> 4. NIO是 面向缓冲区 ，或者面向块编程的。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络
> 5. Java NIO的非阻塞模式，使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。 非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。
> 6. 通俗理解：NIO是可以做到用一个线程来处理多个操作的。假设有10000个请求过来,根据实际情况，可以分配50或者100个线程来处理。不像之前的阻塞IO那样，非得分配10000个。
> 7. HTTP2.0使用了多路复用的技术，做到同一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1大了好几个数量级。

#### 1.3.1 BIO与NIO的比较

> * BIO 以流的方式处理数据,而 NIO 以块的方式处理数据,块 I/O 的效率比流 I/O 高很多
> * BIO 是阻塞的，NIO 则是非阻塞的
> * **BIO基于字节流和字符流进行操作，而 NIO 基于 Channel(通道)和 Buffer(缓冲区)进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。**Selector(选择器)用于监听多个通道的事件（比如：连接请求，数据到达等），因此使用单个线程就可以监听多个客户端通道

#### 1.3.2 Channel,Buffer,Selector关联 

> 1. 每个channel 都会对应一个Buffer
> 2. Selector 对应一个线程， 一个线程对应多个channel(连接)
> 3. 该图反应了有三个channel 注册到 该selector //程序
> 4. 程序切换到哪个channel 是由事件决定的, Event 就是一个重要的概念Selector 会根据不同的事件，在各个通道上切换。
>    5. **Buffer 就是一个内存块 ， 底层是有一个数组。**
> 6. 数据的读取写入是通过Buffer, 这个和BIO , BIO 中要么是输入流，或者是输出流, 不能双向，但是NIO的Buffer 是可以读也可以写, 需要 flip 方法切换。
> 7. channel 是双向的, 可以返回底层操作系统的情况, 比如Linux底层的操作系统通道就是双向的.

![1574592875103](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1574592875103.png)

#### 1.3.3 缓冲区(Buffer)

> 缓冲区（Buffer）：**缓冲区本质上是一个可以读写数据的内存块，可以理解成是一个容器对象(含数组)**，该对象提供了一组方法，可以更轻松地使用内存块，，缓冲区对象内置了一些机制，能够跟踪和记录缓冲区的状态变化情况。Channel 提供从文件、网络读取数据的渠道，但是读取或写入的数据都必须经由 Buffer

##### 1.3.3.1 Buffer中的属性

| **属性** |                           **描述**                           |
| :------: | :----------------------------------------------------------: |
| Capacity | 容量，即可以容纳的最大数据量；在缓冲区创建时被设定并且不能改变 |
|  Limit   | 表示缓冲区的当前终点，不能对缓冲区超过极限的位置进行读写操作。且极限是可以修改的 |
| Position | 位置，下一个要被读或写的元素的索引，每次读写缓冲区数据时都会改变改值，为下次读写作准备 |
|   Mark   |                             标记                             |

```java
public abstract class Buffer {
    private int mark = -1;
    private int position = 0;
    private int limit;
    private int capacity;
```

#### 1.3.4 通道(Channel)

> **NIO的通道类似于流，但有些区别如下：**
>
> * **通道可以同时进行读写，而流只能读或者只能写**
> * 通道可以实现异步读写数据
> * 通道可以从缓冲读数据，也可以从通道写数据到缓冲
>
> * BIO 中的 stream 是单向的，例如 FileInputStream 对象只能进行读取数据的操作，**而 NIO 中的通道(Channel)是双向的，可以读操作，也可以写操作。**
> * Channel在NIO中是一个接口, 常用的 Channel 类有：`FileChannel、DatagramChannel、ServerSocketChannel 和 SocketChannel。【ServerSocketChanne 类似 ServerSocket , SocketChannel 类似 Socket】`
> * **FileChannel 用于文件的数据读写，DatagramChannel 用于 UDP 的数据读写，ServerSocketChannel 和 SocketChannel 用于 TCP 的数据读写。**

```java
public interface Channel extends Closeable {
```

##### 1.3.4.1 FileChannel类

> FileChannel主要用来对本地文件进行 IO 操作，常见的方法有
> `public int read(ByteBuffer dst) ，从通道读取数据并放到缓冲区中`
> `public int write(ByteBuffer src) ，把缓冲区的数据写到通道中`
> `public long transferFrom(ReadableByteChannel src, long position, long count)，从目标通道中复制数据到当前通道`
> `public long transferTo(long position, long count, WritableByteChannel target)，把数据从当前通道复制给目标通道`

#### 1.3.5 选择器(Selector)

> * Java 的 NIO，用非阻塞的 IO 方式。可以用一个线程，处理多个的客户端连接，就会使用到Selector(选择器)
> * Selector 能够检测多个注册的通道上是否有事件发生(注意:多个Channel以事件的方式可以注册到同一个Selector)，如果有事件发生，便获取事件然后针对每个事件进行相应的处理。这样就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求。
> * 只有在 连接/通道 真正有读写事件发生时，才会进行读写，就大大地减少了系统开销，并且不必为每个连接都创建一个线程，不用去维护多个线程
> * 避免了多线程之间的上下文切换导致的开销
> * Netty 的 IO 线程 NioEventLoop 聚合了 Selector(选择器，也叫多路复用器)，可以同时并发处理成百上千个客户端连接。
> * 当线程从某客户端 Socket 通道进行读写数据时，若没有数据可用时，该线程可以进行其他任务。
>   线程通常将非阻塞 IO 的空闲时间用于在其他通道上执行 IO 操作，所以单独的线程可以管理多个输入和输出通道。
> * 由于读写操作都是非阻塞的，这就可以充分提升 IO 线程的运行效率，避免由于频繁 I/O 阻塞导致的线程挂起。
> * 一个 I/O 线程可以并发处理 N 个客户端连接和读写操作，这从根本上解决了传统同步阻塞 I/O 一连接一线程模型，架构的性能、弹性伸缩能力和可靠性都得到了极大的提升

##### 1.3.5.1 NIO网络编程原理

> 1. 当客户端连接时，会通过ServerSocketChannel 得到 SocketChannel
> 2. Selector 进行监听  select 方法, 返回有事件发生的通道的个数.
> 3. 将socketChannel注册到Selector上, register(Selector sel, int ops), 一个selector上可以注册多个SocketChannel
>
> 4. 注册后返回一个 SelectionKey, 会和该Selector 关联(集合)进一步得到各个 SelectionKey (有事件发生)
> 5. 在通过 SelectionKey  反向获取 SocketChannel , 方法 channel()
> 6. 可以通过得到的 channel  , 完成业务处理

##### 1.3.5.2 SelectionKey

> 表示 Selector 和网络通道(Channel)的注册关系, 共四种:
>
> * `int OP_ACCEPT：有新的网络连接可以 accept，值为 16`
> * `int OP_CONNECT：代表连接已经建立，值为 8`
> * `int OP_READ：代表读操作，值为 1` 
> * `int OP_WRITE：代表写操作，值为 4`
>
> 源码
>
> ```java
> public static final int OP_READ = 1 << 0; 
> public static final int OP_WRITE = 1 << 2;
> public static final int OP_CONNECT = 1 << 3;
> public static final int OP_ACCEPT = 1 << 4;
> ```

#### 1.3.6 零拷贝

> * 零拷贝是网络编程的关键，很多性能优化都离不开。
> * 在 Java 程序中，常用的零拷贝有 mmap(内存映射) 和 sendFile。那么，他们在 OS 里，到底是怎么样的一个的设计？我们分析 mmap 和 sendFile 这两个零拷贝
> * 另外我们看下NIO 中如何使用零拷贝
> * 我们说零拷贝，是从操作系统的角度来说的。因为内核缓冲区之间，没有数据是重复的（只有 kernel buffer 有一份数据）。
> * 零拷贝不仅仅带来更少的数据复制，还能带来其他的性能优势，例如更少的上下文切换，更少的 CPU 缓存伪共享以及无 CPU 校验和计算。

##### 1.3.6.1 mmap优化

> * mmap 通过内存映射，将文件映射到内核缓冲区，同时，用户空间可以共享内核空间的数据。这样，在进行网络传输时，就可以减少内核空间到用户控件的拷贝次数。
>
> * mmap是一种内存映射文件的方法，即将一个文件或者其它对象映射到进程的地址空间，实现文件磁盘地址和进程虚拟地址空间中一段虚拟地址的一一对映关系。实现这样的映射关系后，进程就可以采用指针的方式读写操作这一段内存，而系统会自动回写脏页面到对应的文件磁盘上，即完成了对文件的操作而不必再调用read,write等系统调用函数。相反，内核空间对这段区域的修改也直接反映用户空间，从而可以实现不同进程间的文件共享。如下图所示：
>
>   ![mmap](C:\Users\Administrator\Desktop\笔记\韩Netty\images\mmap.JPG)

##### 1.3.6.2  sendFile 优化

> * Linux 2.1 版本 提供了 sendFile 函数，其基本原理如下：数据根本不经过用户态，直接从内核缓冲区进入到 Socket Buffer，同时，由于和用户态完全无关，就减少了一次上下文切换
> * 提示：零拷贝从操作系统角度，是没有cpu 拷贝
> * Linux 在 2.4 版本中，做了一些修改，避免了从内核缓冲区拷贝到 Socket buffer 的操作，直接拷贝到协议栈，从而再一次减少了数据拷贝。
> * 这里其实有 一次cpu 拷贝kernel buffer -> socket buffer但是，拷贝的信息很少，比如
>   lenght , offset , 消耗低，可以忽略。

##### 1.3.6.3 mmap 和 sendFile 的区别

> * mmap 适合小数据量读写，sendFile 适合大文件传输。
> * mmap 需要 4 次上下文切换，3 次数据拷贝；sendFile 需要 3 次上下文切换，最少 2 次数据拷贝。
> * sendFile 可以利用 DMA 方式，减少 CPU 拷贝，mmap 则不能（必须从内核拷贝到 Socket 缓冲区）。

### 1.4 AIO基本介绍

> * JDK 7 引入了 Asynchronous I/O，即 AIO。在进行 I/O 编程中，常用到两种模式：Reactor和 Proactor。Java 的 NIO 就是 Reactor，当有事件触发时，服务器端得到通知，进行相应的处理
> * AIO 即 NIO2.0，叫做异步不阻塞的 IO。AIO 引入异步通道的概念，采用了 Proactor 模式，简化了程序编写，有效的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用
> * 目前 AIO 还没有广泛应用，Netty 也是基于NIO, 而不是AIO， 

