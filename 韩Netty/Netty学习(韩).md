# Netty学习(韩)

## 1.Netty基本介绍

> * Netty 是由 JBOSS 提供的一个 Java 开源框架。Netty 提供异步的、基于事件驱动的网络应用程序框架，用以快速开发高性能、高可靠性的网络 IO 程序
> *  Netty 可以帮助你快速、简单的开发出一个网络应用，相当于简化和流程化了 NIO 的开发过程
> * Netty 是目前最流行的 NIO 框架，Netty 在互联网领域、大数据分布式计算领域、游戏行业、通信行业等获得了广泛的应用，知名的 Elasticsearch 、Dubbo 框架内部都采用了 Netty。
> * Netty 对 JDK 自带的 NIO 的 API 进行了封装。
> * 设计优雅：适用于各种传输类型的统一 API 阻塞和非阻塞 Socket；基于灵活且可扩展的事件模型，可以清晰地分离关注点；高度可定制的线程模型 - 单线程，一个或多个线程池.
> * 使用方便：详细记录的 Javadoc，用户指南和示例；没有其他依赖项，JDK 5（Netty 3.x）或 6（Netty 4.x）就足够了。
> * 高性能、吞吐量更高：延迟更低；减少资源消耗；最小化不必要的内存复制。
> * 安全：完整的 SSL/TLS 和 StartTLS 支持。
> * 社区活跃、不断更新：社区活跃，版本迭代周期短，发现的 Bug 可以被及时修复，同时，更多的新功能会被加入

![netty](C:\Users\Administrator\Desktop\笔记\韩Netty\images\netty.JPG)

## 2. Netty线程模型

> 不同的线程模式，对程序的性能有很大影响，为了搞清Netty 线程模式，我们来系统的讲解下 各个线程模式， 最后看看Netty 线程模型有什么优越性.
>
> 目前存在的线程模型有：
>
> 1. **传统阻塞 I/O 服务模型** 
>
> 2. **Reactor 模式**
>
>    根据 Reactor 的数量和处理资源池线程的数量不同，有 3 种典型的实现
>
>    1. 单 Reactor 单线程
>    2. 单 Reactor 多线程
>    3. 主从 Reactor 多线程 
>    4. **Netty 线程模式(Netty 主要基于主从 Reactor 多线程模型做了一定的改进，其中主从 Reactor 多线程模型有多个 Reactor)**

### 2.1 传统阻塞 I/O 服务模型

> **模型特点**
>
> * 采用阻塞IO模式获取输入的数据
>
> * 每个连接都需要独立的线程完成数据的输入，业务处理,数据返回
>
> **问题分析**
>
> * 当并发数很大，就会创建大量的线程，占用很大系统资源
> * 连接创建后，如果当前线程暂时没有数据可读，该线程会阻塞在read 操作，造成线程资源浪费

![image-20191207142245957](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191207142245957.png)

### 2.2 Reactor 模式

> * 基于 I/O 复用模型：多个连接共用一个阻塞对象，应用程序只需要在一个阻塞对象等待，无需阻塞等待所有连接。当某个连接有新的数据可以处理时，操作系统通知应用程序，线程从阻塞状态返回，开始进行业务处理。
> * 基于线程池复用线程资源：不必再为每个连接创建线程，将连接完成后的业务处理任务分配给线程进行处理，一个线程可以处理多个连接的业务。

![image-20191207142455235](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191207142455235.png)

#### 2.2.1 Reactor核心组成

> * Reactor：Reactor 在一个单独的线程中运行，负责监听和分发事件，分发给适当的处理程序来对 IO 事件做出反应。 它就像公司的电话接线员，它接听来自客户的电话并将线路转移到适当的联系人；
> * Handlers：处理程序执行 I/O 事件要完成的实际事件，类似于客户想要与之交谈的公司中的实际官员。Reactor 通过调度适当的处理程序来响应 I/O 事件，处理程序执行非阻塞操作。

#### 2.2.2 Reactor 模式分类

> **根据 Reactor 的数量和处理资源池线程的数量不同，有 3 种典型的实现**
>
> 1. 单 Reactor 单线程
> 2. 单 Reactor 多线程
> 3. 主从 Reactor 多线程

##### 2.2.2.1 单 Reactor 单线程

> * Select 是前面 I/O 复用模型介绍的标准网络编程 API，可以实现应用程序通过一个阻塞对象监听多路连接请求
> * Reactor 对象通过 Select 监控客户端请求事件，收到事件后通过 Dispatch 进行分发
> * 如果是建立连接请求事件，则由 Acceptor 通过 Accept 处理连接请求，然后创建一个对应的Handler 对象处理连接完成后的后续业务处理
> * 如果不是建立连接事件，则 Reactor 会分发调用连接对应的 Handler 来响应
> * **Handler 会完成 Read→业务处理→Send 的完整业务流程**
>
> * **优点：模型简单，没有多线程、进程通信、竞争的问题，全部都在一个线程中完成**
> * **缺点：性能问题，只有一个线程，无法完全发挥多核 CPU 的性能。Handler 在处理某个连接上的业务时，整个进程无法处理其他连接事件，很容易导致性能瓶颈**
> * **缺点：可靠性问题，线程意外终止，或者进入死循环，会导致整个系统通信模块不可用，不能接收和处理外部消息，造成节点故障**
> * 使用场景：客户端的数量有限，业务处理非常快速，比如 Redis在业务处理的时间复杂度 O(1) 的情况

![image-20191207152912533](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191207152912533.png)

##### 2.2.2.2 单Reactor多线程

> * Reactor 对象通过select 监控客户端请求事件, 收到事件后，通过dispatch进行分发
> * 如果建立连接请求, 则右Acceptor 通过accept 处理连接请求, 然后创建一个对应的Handler对象处理完成连接后的各种事件
> * 如果不是连接请求，则由reactor分发调用连接对应的handler 来处理
> * **handler 只负责响应事件，不做具体的业务处理, 通过read 读取数据后，会分发给后面的worker线程池的某个线程处理业务**
> * **worker 线程池会分配独立线程完成真正的业务，并将结果返回给handler**
> * handler收到响应后，通过send 将结果返回给client
> * **优点：可以充分的利用多核cpu 的处理能力**
> * **缺点：多线程数据共享和访问比较复杂， reactor 处理所有的事件的监听和响应，在单线程运行， 在高并发场景容易出现性能瓶颈.**

![image-20191207153101162](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191207153101162.png)

##### 2.2.2.3 主从 Reactor 多线程

> * Reactor主线程 MainReactor 对象通过select 监听连接事件, 收到事件后，通过Acceptor 处理连接事件
> * **当 Acceptor  处理连接事件后，MainReactor 将连接分配给SubReactor** 
> * **subreactor 将连接加入到连接队列进行监听,并创建handler进行各种事件处理**
> * 当有新事件发生时， subreactor 就会调用对应的handler处理
> * handler 通过read 读取数据，分发给后面的worker 线程处理
> * worker 线程池分配独立的worker 线程进行业务处理，并返回结果
> * handler 收到响应的结果后，再通过send 将结果返回给client
> * **Reactor 主线程可以对应多个Reactor 子线程, 即MainRecator 可以关联多个SubReactor**
> * **优点：父线程与子线程的数据交互简单职责明确，父线程只需要接收新连接，子线程完成后续的业务处理。**
> * **优点：父线程与子线程的数据交互简单，Reactor 主线程只需要把新连接传给子线程，子线程无需返回数据。**
> * **缺点：编程复杂度较高**
> * 结合实例：这种模型在许多项目中广泛使用，包括 Nginx 主从 Reactor 多进程模型，Memcached 主从多线程，Netty 主从多线程模型的支持

![image-20191207155551377](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191207155551377.png)

##### 2.2.2.4 Reactor模式优点

> 1. 响应快，不必为单个同步时间所阻塞，虽然 Reactor 本身依然是同步的
> 2. 可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销
> 3. 扩展性好，可以方便的通过增加 Reactor 实例个数来充分利用 CPU 资源
> 4. 复用性好，Reactor 模型本身与具体事件处理逻辑无关，具有很高的复用性

### 2.3 Netty模型

> Netty 主要基于主从 Reactors 多线程模型（如图）做了一定的改进，其中主从 Reactor 多线程模型有多个 Reactor
>
> 1. Netty抽象出两组线程池 BossGroup 专门负责接收客户端的连接, WorkerGroup 专门负责网络的读写
> 2. BossGroup 和 WorkerGroup 类型都是 NioEventLoopGroup
> 3. NioEventLoopGroup 相当于一个事件循环组, 这个组中含有多个事件循环 ，每一个事件循环是 NioEventLoop
> 4. NioEventLoop 表示一个不断循环的执行处理任务的线程， 每个NioEventLoop 都有一个selector , 用于监听绑定在其上的socket的网络通讯
> 5. NioEventLoopGroup 可以有多个线程, 即可以含有多个NioEventLoop
> 6. 每个Boss NioEventLoop 循环执行的步骤有3步
>    *  轮询accept 事件
>    * 处理accept 事件 , 与client建立连接 , 生成NioScocketChannel , 并将其注册到某个worker NIOEventLoop 上的 selector 
>    * 处理任务队列的任务 ， 即 runAllTasks
> 7. 每个 Worker NIOEventLoop 循环执行的步骤
>    * 轮询read, write 事件
>    * 处理i/o事件， 即read , write 事件，在对应NioScocketChannel 处理
>    * 处理任务队列的任务 ， 即 runAllTasks
> 8. 每个Worker NIOEventLoop  处理业务时，会使用pipeline(管道), pipeline 中包含了 channel , 即通过pipeline 可以获取到对应通道, 管道中维护了很多的处理器

![image-20191224153629835](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191224153629835.png)

### 2.4 任务队列

> 任务队列中的 Task 有 3 种典型使用场景:
>
> * 用户程序自定义的普通任务 ,如下代码
>
>   ```java
>   ctx.channel().eventLoop().execute(() -> {
>               try {
>                   Thread.sleep(10 * 1000);
>               } catch (InterruptedException e) {
>                   e.printStackTrace();
>               }
>               ctx.writeAndFlush(Unpooled.copiedBuffer("hello 我是用户程序自定义的普通任务",CharsetUtil.UTF_8));
>           });
>   ```
>
> * 用户自定义定时任务
>
>   ```java
>   ctx.channel().eventLoop().schedule(() -> {
>               try{
>                   Thread.sleep(10 * 1000);
>                   ctx.writeAndFlush(Unpooled.copiedBuffer("hello,我是用户自定义定时任务",CharsetUtil.UTF_8));
>               }
>               catch (Exception e){
>                   e.printStackTrace();
>               }
>                   },
>                   5,
>                   TimeUnit.SECONDS
>           );
>   ```
>
> * 非当前 Reactor 线程调用 Channel 的各种方法
>
> * 例如在推送系统的业务线程里面，根据用户的标识，找到对应的 Channel 引用，然后调用 Write 类方法向该用户推送消息，就会进入到这种场景。最终的 Write 会提交到任务队列中后被异步消费

### 2.5 Netty模型再说明

> * **Netty 抽象出两组线程池，BossGroup 专门负责接收客户端连接，WorkerGroup 专门负责网络读写操作。**
> * NioEventLoop 表示一个不断循环执行处理任务的线程，每个 NioEventLoop 都有一个 selector，用于监听绑定在其上的 socket 网络通道。
> * NioEventLoop 内部采用串行化设计，从消息的读取->解码->处理->编码->发送，始终由 IO 线程 NioEventLoop 负责
>
> ​      **NioEventLoopGroup 下包含多个 NioEventLoop**
>
> * 每个 NioEventLoop 中包含有一个 Selector，一个 taskQueue
> * 每个 NioEventLoop 的 Selector 上可以注册监听多个 NioChannel
> * 每个 NioChannel 只会绑定在唯一的 NioEventLoop 上
> * 每个 NioChannel 都绑定有一个自己的 ChannelPipeline

## 3. 异步模型

> * 异步的概念和同步相对。当一个异步过程调用发出后，调用者不能立刻得到结果。实际处理这个调用的组件在完成后，通过状态、通知和回调来通知调用者。
> * Netty 中的 I/O 操作是异步的，包括 Bind、Write、Connect 等操作会简单的返回一个 ChannelFuture。
> * **调用者并不能立刻获得结果，而是通过 Future-Listener 机制**，用户可以方便的主动获取或者通过通知机制获得 IO 操作结果
> * **Netty 的异步模型是建立在 future 和 callback 的之上的**。callback 就是回调。重点说 Future，它的核心思想是：假设一个方法 fun，计算过程可能非常耗时，等待 fun返回显然不合适。那么可以在调用 fun 的时候，立马返回一个 Future，后续可以通过 Future去监控方法 fun 的处理过程(即 ： Future-Listener 机制)

###  3.1 Future说明

> * 表示异步的执行结果, 可以通过它提供的方法来检测执行是否完成，比如检索计算等等.
>
> * ChannelFuture 是一个接口,我们可以添加监听器，当监听的事件发生时，就会通知到监听器.
>
>    ```java
>   public interface ChannelFuture extends Future<Void> {
>   ```
>
> * 在使用 Netty 进行编程时，拦截操作和转换出入栈数据只需要您提供 callback 或利用future 即可。这使得链式操作简单、高效, 并有利于编写可重用的、通用的代码。
>
> * Netty 框架的目标就是让你的业务逻辑从网络基础应用编码中分离出来、解脱出来

![image-20191224171326253](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191224171326253.png)

### 3.1 Future-Listener机制

> 当 Future 对象刚刚创建时，处于非完成状态，调用者可以通过返回的 ChannelFuture 来获取操作执行的状态，注册监听函数来执行完成后的操作。
> 常见有如下操作：
>
> * 通过 isDone 方法来判断当前操作是否完成；
> * 通过 isSuccess 方法来判断已完成的当前操作是否成功；
> * 通过 getCause 方法来获取已完成的当前操作失败的原因；
> * 通过 isCancelled 方法来判断已完成的当前操作是否被取消；
> * 通过 addListener 方法来注册监听器，当操作已完成(isDone 方法返回完成)，将会通知指定的监听器；如果 Future 对象已完成，则通知指定的监听器
>
> **比如：绑定端口是异步操作，当绑定操作处理完，将会调用相应的监听器处理逻辑**
>
> ```java
> 			/**给channelFuture注册监听，监控我们所关心的事件*/
>             channelFuture.addListener((ChannelFutureListener) future -> {
>                 if(future.isSuccess()){
>                     System.out.println("监听端口6666成功");
>                 }
>                 else {
>                     System.out.println("端口6666监听失败");
>                 }
>             });
> ```
>
> * 相比传统阻塞 I/O，执行 I/O 操作后线程会被阻塞住, 直到操作完成
> * 异步处理的好处是不会造成线程阻塞，线程在 I/O 操作期间可以执行别的程序，在高并发情形下会更稳定和更高的吞吐量

## 4. Netty核心模块组件

### 4.1 Bootstrap、ServerBootstrap

> * Bootstrap 意思是引导，一个 Netty 应用通常由一个 Bootstrap 开始，主要作用是配置整个 Netty 程序，串联各个组件，Netty 中 Bootstrap 类是客户端程序的启动引导类，ServerBootstrap 是服务端启动引导类
> * 常见方法

```java
public ServerBootstrap group(EventLoopGroup parentGroup, EventLoopGroup childGroup)//该方法用于服务器端，用来设置两个 EventLoopGroup
    
public B group(EventLoopGroup group) //该方法用于客户端，用来设置一个 EventLoopGroup
    
public B channel(Class<? extends C> channelClass)//该方法用来设置一个服务器端的通道实现
    
public <T> B option(ChannelOption<T> option, T value)//用来给 ServerChannel 添加配置
    
public <T> ServerBootstrap childOption(ChannelOption<T> childOption, T value)//用来给接收到的通道添加配置
    
public ServerBootstrap childHandler(ChannelHandler childHandler)//该方法用来设置业务处理类（自定义的 handler）
    
public ChannelFuture bind(int inetPort) //该方法用于服务器端，用来设置占用的端口号
    
public ChannelFuture connect(String inetHost, int inetPort) //该方法用于客户端，用来连接服务器
```

### 4.2 Future 与 ChannelFuture

> Netty 中所有的 IO 操作都是异步的，不能立刻得知消息是否被正确处理。但是可以过一会等它执行完成或者直接注册一个监听，具体的实现就是通过 Future 和 ChannelFutures，他们可以注册一个监听，当操作执行成功或失败时监听会自动触发注册的监听事件
>
> **常见的方法有**:
> Channel channel()，返回当前正在进行 IO 操作的通道
> ChannelFuture sync()，等待异步操作执行完毕

### 4.3 Channel

> * Netty 网络通信的组件，能够用于执行网络 I/O 操作。
>
> * 通过Channel 可获得当前网络连接的通道的状态
>
> * 通过Channel 可获得 网络连接的配置参数 （例如接收缓冲区大小）
>
> * Channel 提供异步的网络 I/O 操作(如建立连接，读写，绑定端口)，异步调用意味着任何 I/O 调用都将立即返回，并且不保证在调用结束时所请求的 I/O 操作已完成
>
> * 调用立即返回一个 ChannelFuture 实例，通过注册监听器到 ChannelFuture 上，可以 I/O 操作成功、失败或取消时回调通知调用方
>
> * 支持关联 I/O 操作与对应的处理程序
>
> * 不同协议、不同的阻塞类型的连接都有不同的 Channel 类型与之对应，常用的 Channel 类型:
>
>   `NioSocketChannel，异步的客户端 TCP Socket 连接。`
>   `NioServerSocketChannel，异步的服务器端 TCP Socket 连接。`
>   `NioDatagramChannel，异步的 UDP 连接。`
>   `NioSctpChannel，异步的客户端 Sctp 连接。`
>   `NioSctpServerChannel，异步的 Sctp 服务器端连接，这些通道涵盖了 UDP 和 TCP 网络 IO 以及文件 IO。`

### 4.4 Selector

> * Netty 基于 Selector 对象实现 I/O 多路复用，通过 Selector 一个线程可以监听多个连接的 Channel 事件。
> * 当向一个 Selector 中注册 Channel 后，Selector 内部的机制就可以自动不断地查询(Select) 这些注册的 Channel 是否有已就绪的 I/O 事件（例如可读，可写，网络连接完成等），这样程序就可以很简单地使用一个线程高效地**管理多个 Channel**  

### 4.5 ChannelHandler 及其实现类

> * ChannelHandler 是一个接口，处理 I/O 事件或拦截 I/O 操作，并将其转发到其 ChannelPipeline(业务处理链)中的下一个处理程序。
>
> * ChannelHandler 本身并没有提供很多方法，因为这个接口有许多的方法需要实现，一般使用到时，可以继承它的子类
>
> * ChannelHandler 及其实现类一览图
>
>   * ChannelInboundHandler 用于处理入站 I/O 事件。
>
>   * ChannelOutboundHandler 用于处理出站 I/O 操作。
>     **//适配器**
>
>   * ChannelInboundHandlerAdapter 用于处理入站 I/O 事件。
>
>   * ChannelOutboundHandlerAdapter 用于处理出站 I/O 操作。
>
>   * ChannelDuplexHandler 用于处理入站和出站事件,因为它既继承了`ChannelInboundHandlerAdapter`也实现了`ChannelOutboundHandler`
>
>     ```java
>     public class ChannelDuplexHandler extends ChannelInboundHandlerAdapter implements ChannelOutboundHandler {
>     ```

![image-20191225201604928](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191225201604928.png)

> * 我们经常需要自定义一个 Handler 类去继承 `ChannelInboundHandlerAdapter`，然后通过重写相应方法实现业务逻辑，我们接下来看看一般都需要重写哪些方法
>
>   ```java
>   public class ChannelInboundHandlerAdapter extends ChannelHandlerAdapter implements ChannelInboundHandler {
>       @Override
>       public void channelRegistered(ChannelHandlerContext ctx) throws Exception {
>           ctx.fireChannelRegistered();
>       }
>       @Override
>       public void channelUnregistered(ChannelHandlerContext ctx) throws Exception {
>           ctx.fireChannelUnregistered();
>       }
>       //通道就绪事件
>       @Override
>       public void channelActive(ChannelHandlerContext ctx) throws Exception {
>           ctx.fireChannelActive();
>       }
>       @Override
>       public void channelInactive(ChannelHandlerContext ctx) throws Exception {
>           ctx.fireChannelInactive();
>       }
>      //通道读取数据事件
>       @Override
>       public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
>           ctx.fireChannelRead(msg);
>       }
>       //数据读取完毕事件
>       @Override
>       public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
>           ctx.fireChannelReadComplete();
>       }
>       @Override
>       public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
>           ctx.fireUserEventTriggered(evt);
>       }
>       @Override
>       public void channelWritabilityChanged(ChannelHandlerContext ctx) throws Exception {
>           ctx.fireChannelWritabilityChanged();
>       }
>       //通道发生异常事件
>       @Override
>       public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
>               throws Exception {
>           ctx.fireExceptionCaught(cause);
>       }
>   }
>   ```

### 4.6 Pipeline和ChannelPipeline

> * ChannelPipeline 是一个 Handler 的集合，它负责处理和拦截 inbound 或者 outbound 的事件和操作，相当于一个贯穿 Netty 的链。(**也可以这样理解：ChannelPipeline 是 保存 ChannelHandler 的 List，用于处理或拦截 Channel 的入站事件和出站操作**)
> * ChannelPipeline 实现了一种高级形式的拦截过滤器模式，使用户可以完全控制事件的处理方式，以及 Channel 中各个的 ChannelHandler 如何相互交互
> * 在 Netty 中每个 Channel 都有且仅有一个 ChannelPipeline 与之对应
> * 一个 Channel 包含了一个 ChannelPipeline，而 ChannelPipeline 中又维护了一个由 ChannelHandlerContext 组成的双向链表，并且每个 ChannelHandlerContext 中又关联着一个 ChannelHandler
> * 入站事件和出站事件在一个双向链表中，入站事件会从链表 head 往后传递到最后一个入站的 handler，出站事件会从链表 tail 往前传递到最前一个出站的 handler，两种类型的 handler 互不干扰

![image-20191226142115836](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191226142115836.png)

### 4.7 ChannelHandlerContext

> * 保存 Channel 相关的所有上下文信息，同时关联一个 ChannelHandler 对象
>
> * 即ChannelHandlerContext 中 包 含 一 个 具 体 的 事 件 处 理 器 ChannelHandler ， 同 时ChannelHandlerContext 中也绑定了对应的 pipeline 和 Channel 的信息，方便对 ChannelHandler进行调用.
>
> * 常用方法
>
>   ```java
>   ChannelFuture close()//关闭通道
>   ChannelOutboundInvoker flush()//刷新
>   ChannelFuture writeAndFlush(Object msg) // 将 数 据 写 到 ChannelPipeline 中 当 前
>   							//ChannelHandler 的下一个 ChannelHandler 开始处理（出站）
>   ```

### 4.8 ChannelOption

> * Netty 在创建 Channel 实例后,一般都需要设置 ChannelOption 参数
>
> * ChannelOption 参数如下:
>
>   ```java
>   ChannelOption.SO_BACKLOG   对应 TCP/IP 协议 listen 函数中的 backlog 参数，用来初始化服务器可连接队列大小。服务端处理客户端连接请求是顺序处理的，所以同一时间只能处理一个客户端连接。多个客户端来的时候，服务端将不能处理的客户端连接请求放在队列中等待处理，backlog 参数指定了队列的大小。
>   
>   ChannelOption.SO_KEEPALIVE     一直保持连接活动状态
>   ```

### 4.9 EventLoopGroup及其实现类NioEventLoopGroup

> * EventLoopGroup 是一组 EventLoop 的抽象，Netty 为了更好的利用多核 CPU 资源，一般会有多个 EventLoop 同时工作，每个 EventLoop 维护着一个 Selector 实例。
> * EventLoopGroup 提供 next 接口，可以从组里面按照一定规则获取其中一个 EventLoop来处理任务。在 Netty 服务器端编程中，我们一般都需要提供两个 EventLoopGroup，例如：BossEventLoopGroup 和 WorkerEventLoopGroup。

61