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

> * Netty 抽象出两组线程池，BossGroup 专门负责接收客户端连接，WorkerGroup 专门负责网络读写操作。
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
> * 调用者并不能立刻获得结果，而是通过 Future-Listener 机制，用户可以方便的主动获取或者通过通知机制获得 IO 操作结果
> * **Netty 的异步模型是建立在 future 和 callback 的之上的**。callback 就是回调。重点说 Future，它的核心思想是：假设一个方法 fun，计算过程可能非常耗时，等待 fun返回显然不合适。那么可以在调用 fun 的时候，立马返回一个 Future，后续可以通过 Future去监控方法 fun 的处理过程(即 ： Future-Listener 机制)

###  3.1 Future说明

> * 表示异步的执行结果, 可以通过它提供的方法来检测执行是否完成，比如检索计算等等.
>
> * ChannelFuture 是一个接口,我们可以添加监听器，当监听的事件发生时，就会通知到监听器.
>
>    ```java
>   public interface ChannelFuture extends Future<Void> {
>    ```
>
> * 在使用 Netty 进行编程时，拦截操作和转换出入栈数据只需要您提供 callback 或利用future 即可。这使得链式操作简单、高效, 并有利于编写可重用的、通用的代码。
>
> * Netty 框架的目标就是让你的业务逻辑从网络基础应用编码中分离出来、解脱出来

![image-20191224171326253](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20191224171326253.png)

### 3.1 Future-Listener机制

> 52