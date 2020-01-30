# Kotlin 学习

## 1.协程(coroutine)

> * **协程通过将复杂性放入库中来简化异步编程**。程序的逻辑可以在协程中顺序地表达而底层库会为我们解决其异步性。
> * 该库可以将用户代码的相关部分包装为回调、订阅相关事件、在不同线程（甚至不同机器）上调试执行，**而代码则保持如同顺序执行一样简单**。
> * **协程就像非常轻量级的线程**。线程是由系统调试的，线程切换或线程阻塞的开销都比较大。
> * 而协程依赖于线程，但是协程挂起时不需要阻塞线程，几乎是无代价的，协程是由开发者控制的。所以协程也像用户态的线程，非常轻量级，**一个线程中可以创建任意个协程**。
> * 总而言之：协程可以简化异步编程，可以顺序地表达程序，协程也提供了一种避免阻塞线程并用更廉价、更可控的操作替代线程阻塞的方法------协程挂起。

## 2. 协程重要概念

> * **CoroutineScope**：可以理解为协程本身，包含了CoroutineContext。
>
> * **CoroutineContext**：协程上下文，是一些元素的集合，主要包括Job和CoroutineDispatcher元素，可以代表一个协程的场景。
>
> * **EmptyCoroutineContext**：表示一个空的协程上下文。
>
> * **CoroutineDispatcher**：协程调度器(派发器)，决定协程所在的线程或线程池。它可以指定协程运行于特定的一个线程、一个线程池或者不指定任何线程（这样协程就会运行于当前线程）。
>
> * **Coroutines-core**中CoroutineDispatcher有三种标准实现：**Dispatchers.Default、Dispatchers.IO、Dispatchers.Main和Dispatchers.Unconfined,Unconfined就是不指定线程**。
>
> * **launch**函数定义如果不指定`CoroutineDispatcher`或者没有其他的`ContinuationInterceptor`,默认的协程调度器就是Dispatcher.Default,Default是一个协程调度器，其指定的线程为共有的线程池，线程数量至少为2，最大与CPU数相同。
>
> * **Job**：任务，封装了协程中需要执行的代码逻辑。Job可以取消并且有简单的生命周期，它有几种状态：
>
>   |                State                | isActive | isCompleted | isCancelled |
>   | :---------------------------------: | :------: | :---------: | :---------: |
>   |      New(optinal inital state)      |  false   |    false    |    false    |
>   |    Active(default inital state)     |   true   |    false    |    false    |
>   | Completing(optinal transient state) |   true   |    false    |    false    |
>   | Cancelling(optinal transient state) |  false   |    false    |    true     |
>   |       Cancelled(final state)        |  false   |    true     |    true     |
>   |       Completed(final state)        |  false   |    true     |    false    |
>
> * **Job完成时是没有返回值的，如果需要返回值的话，应该使用Deferred,它是Job的子类**
>
>   ````java
>   public interface Deferred <out T>: Job
>   ````
>
> * **CoroutineScope.launch**函数是属于协程构建器Coroutine builders,Kotlin中还有其他几种Builders，负责创建协程。
>
> * CoroutineScope.launch{}是最常用的Coroutine builders,**不阻塞当前线程，在后台创建一个新协程**，也可以指定协程调度器，如果不指定线程，则运行在当前线程上。
>
> * **runBlocking{}是创建一个新的协程同时阻塞当前线程**，直到协程结束。这个不应该在协程中使用，主要是主为main函数和测试设计的。
>
> * withContext{}不会创建新的协程，在指定协程上运行挂起代码块，并挂起该协程直至代码块运行完成。
>
> * CoroutineScope.async{}可以实现与launch builder一样的效果，在后台创建一个新协程，唯一的区别是**它有返回值，因为CoroutineScope.async{}返回的是Deferred类型**。

64