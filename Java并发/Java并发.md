# Java并发学习

## 1. Object类

### 1.1 wait方法

* `wait()`会使得当前线程会去等待直到另外一个线程去调用当前这个对象的`notify()`或者`notifyAll()`方法。换言之，这个方法与直接去执行`wait(0)`的行为结果是一样的。

* **在调用wait()方法时，当前的线程必须要拥有这个对象的锁(monitor)**，调用完`wait()`方法后线程会**释放这个锁的所有权**并且等待直到其他线程通过调用`notify()`或`notifyAll()`去通知在该对象锁上等待被唤醒的线程。线程会继续等待直到重新获得锁的所有权然后去恢复执行。**`wait()`只能够被持有这个对象锁的线程去调用**。
* 相比Thread的`sleep()`方法，一旦调用了`wait(`)方法之后，就会释放这个对象锁的所有权，而`sleep()`方法并不会释放这个锁的所有权。

* `wait()`方法会使得**当前线程将自身放置到当前对象的一个等待集合(wait set)中并且释放当前对象锁的所有权**，处于休眠状态直到以下四种情况之一发生：

  1. 其他线程调用这个对象的`notify()`方法并且恰好选择这个线程作为被唤醒的线程
  2. 其他线程调用这个对象的`notifyAll()`方法
  3. 其他线程中断这个线程
  4. 指定的时间超时，如果指定的timeout参数为0，则会一些等待直到被通知

* 当这个线程被唤醒后，就会被这个对象的等待集合所删除然后重新又可以进行线程的调度，接下来会按照正常的方式与其他线程对这个对象的同步权进行竞争。

* `wait()`方法总是在**循环中发生**

  ```java
   synchronized (obj) {
   	while (&lt;condition does not hold&gt;)
      	obj.wait(timeout);
          // Perform action appropriate to condition
  	}
  ```

### 1.2 notify方法

* **唤醒在这个对象锁(monitor)上正在等待的一个线程，如果有多个线程都在等待这个对象的锁，那么就会选择他们中的一个进行唤醒。这个选择是任意的,根据策略来去选择的**。

* 被唤醒的线程是无法继续执行的，直到当前的线程重新获取到这个对象的锁。被唤醒的线程会按照公平的方式与其他的线程对于这个对象的锁进行竞争。被唤醒的线程地位与其他线程是一样的，都有可能成为下一个这个对象锁的持有者。
* **notify()方法只能够被持有这个对象锁的线程调用**，一个线程会成为这个对象锁的持有者以如下三种方式之一:
  1. 通过执行这个对象的synchronized实例方法来获得
  2. 通过执行这个对象的synchronized的代码块来获得
  3. 对于Class类型的对象，通过执行static的synchronized方法就可以这个class对象的锁

### 1.3 notifyAll方法

* **唤醒在这个对象锁上正在等待的所有线程**。
* 被唤醒的线程是无法继续执行的，直到当前的线程重新获取到这个对象的锁。被唤醒的线程会按照公平的方式与其他的线程对于这个对象的锁进行竞争。被唤醒的线程地位与其他线程是一样的，都有可能成为下一个这个对象锁的持有者。
* **notifyAll()方法只能够被持有这个对象锁的线程调用**。

### 1.4 以上三个方法的总结

* 当调用`wait()`时，首先需要确保调用`wait()`方法的线程已经持有了对象的锁。
* 当调用`wait()`后，该线程就会释放掉这个对象的锁，然后进入到等待状态(wait set)。
* 当线程调用了`wait()`后进入到等待状态时，它就可以等待其他线程调用相同对象的`notify()`或`notifyAll()`方法来使得自己被唤醒。
* 一旦这个线程被其他线程唤醒后，该线程就会与其他线程一同开始竞争这个对象的锁(monitor)且是公平竞争；只有当该线程获取到这个对象的锁后，线程才会继续往下执行。
* 调用`wait()`方法的代码片段需要放在一个synchronized块或是synchronized方法中，这样才可以确保线程在调用`wait()`方法前已经获得到了对象的锁。
* 当调用对象的`notify()`方法时，它会随机唤醒该对象等待集合(wait set)中的任意一个线程，当某个线程被唤醒后，它就会与其他线程一同竞争对象的锁。
* 当调用对象的`notifyAll()`方法时，它会唤醒该对象等待集合(wait set)中的所有线程，这些线程被唤醒后，又会开始竞争对象的锁。
* 在某一时刻，只有唯一一个线程可以拥有对象的锁。

### 1.5 案例解析

```java
/**
  编写一个多线程程序，实现这样一个目标
  1.存在一个对象，该对象有一个int类型的成员变量counter，该成员变量初始值为0
  2.创建两个线程，其中一个线程对该对象的成员变量+1，另一个线程对该对象的成员变量-1
  3.输出该对象成员变量counter每次变化后的值
  4.最终输出的结果应为：10101010...
*/
	private int counter;

    public synchronized void increase() {
        //说明已经是1了
        while (1 == counter) {
            try{
                /**说明此处counter已经为1
                 * 需要释放掉锁，让其他线程去减1
                 */
                wait();
            }
            catch (InterruptedException e){
                e.printStackTrace();
            }
        }
        //为0，则加1
        counter++;
        System.out.println(counter);
        notify();
    }

   public synchronized void decrease(){
        while(0 == counter){
            try{
                /**说明此处counter已经为0
                 * 需要释放掉锁，让其他线程去加1
                 */
                wait();
            }
            catch (InterruptedException e){
                e.printStackTrace();
            }
        }
        //为1，则减1
        counter--;
       System.out.println(counter);
       notify();
   }
```

## 2. synchronized关键字

* 当一个线程去访问某个对象的`static synchronized`方法时，该线程获取的锁是当前对象对应的`Class`对象锁，这把锁只有一个。

* 当某个对象里有若干个普通的`synchronized`方法，那么这些普通的`synchronized`方法在某一时刻被多个线程所访问时，只能有其中一个方法被线程调用，因为当一个线程进入到`synchronized`方法时，就会去先获取这个对象的锁。而同一个对象的`synchronized`方法锁的是当前对象的锁，**此时只有一份**，就必须先等待已经获得对象锁的线程先执行完，才有机会获取锁去执行。

* 当我们使用`synchronized`来修饰代码块时，字节码层面上是通过`monitorenter`与`monitorexit`指令来实现的锁的获取与释放动作

* 当线程进入到`monitorenter`指令后，线程将会持有`Monitor`对象，退出`monitorexit`指令后，线程将会释放`Monitor`对象

* 如下图：程序在进入`synchronized`代码后，从字节码角度是通过`monitorenter`指令标识，而第一个`monitorexit`是代表程序正常退出后的代码块结束，释放掉锁，第二个`monitorexit`是代码当程序执行发生异常后，通过`monitorexit`释放掉锁。

* ![2](C:\Users\Administrator\Desktop\笔记\Java并发\images\2.JPG)

* 对于`synchronized`关键字修饰**方法**来说，并没有出现`monitorenter`与`monitorexit`指令，而是出现了一个`ACC_SYNCHRONIZED`标志，JVM使用了`ACC_SYNCHRONIZED`访问标志来区分一个方法是否为同步方法，当方法被调用时，调用指令会检查该方法是否拥有`ACC_SYNCHRONIZED`标志，如果有，那么执行线程将会先持有方法所在的对象的`Monitor`对象，然后再去执行方法体；在该方法执行期间，其他任何线程均无法再获取到这个`Monitor`对象，当线程执行完该方法后，它会释放掉这个`Monitor`对象，即便在发生异常时，也会释放这个`Monitor`对象

* ![捕获](C:\Users\Administrator\Desktop\笔记\Java并发\images\捕获.JPG)

* 当一个执行线程去调用一个`static`的`synchronized`方法时，调用指令会检查该方法是否拥有`ACC_STATIC`和`ACC_SYNCHRONIZED`标志，如果有，那么执行线程将会先持有方法所在的对象的`Class`对象的`Monitor`对象，然后再去执行方法体,**同时`args_size`也会被置为0，因为在`static`方法中不会使用到`this`关键字**

* ![3](C:\Users\Administrator\Desktop\笔记\Java并发\images\3.JPG)

* JVM中的同步是基于进入与退出监视器对象(管程对象) (`Monitor`)来实现的。每个对象实例都会有一个`Monitor`对象，`Monitor`对象会和`Java`对象一同创建并销毁。`Monitor`对象是由`C++`(`ObjectMonitor`)来实现的。

* 当多个线程同时访问一段同步代码块时，这些线程会被放置到一个`EntryList`集合中，**处于阻塞状态的线程被放到该集合当中**。接下来，当线程获取到对象的`Monitor`时，`Monitor`是依赖于底层操作系统的`mutex lock`来实现互斥的，线程获取`mutex`成功，则会持有该`mutex`，这时其他线程就无法再获取到该`mutex`。

* 如果线程调用了`wait()`方法，那么该线程就会释放掉所持有的`mutex`,并且该线程会进入到`WaitSet`集合(等待集合)中，等待下一次被其他线程调用相同对象的`notify()`或`notifyAll()`方法，唤醒后则该线程就会从`WaitSet`转到`EntryList`集合中,等待获取到对象的`Monitor`去执行方法体。如果当前线程顺利执行完毕方法，那么它也会释放掉所持有的`mutex`

* 在`JDK 1.5`之前，我们若想实现线程同步，只能通过`synchronized`关键字这一种方式来达成；底层，`Java`也是通过`synchronized`关键字来做到数据的原子性维护；`synchronized`关键字是`JVM`实现的一种**内置锁，从底层角度来说，这种锁的获取与释放都是由`JVM`帮助我们隐式实现的**

* 从`JDK 1.5`开始，并发包引入了`Lock`锁，`Lock`这种同步锁是基于`Java`来实现的，因此锁的获取与释放都是通过`Java`代码来实现与控制的；然而，`synchronized`是基于底层操作系统的`Mutex Lock`来实现的，每次对锁的获取与释放动作都会带来用户态与内核态之间的切换，这种切换会极大地增加系统的负担；当并发量较高时，也就是说锁的竞争比较激烈时，`synchronized`锁在性能上的表现就非常差

* 从`JDK 1.6`开始，`synchronized`锁的实现发生了很大的变化；JVM引入了相应的优化手段来提升`synchronized`锁的性能，这种提升涉及到**偏向锁、轻量级锁、重量级锁**等，从而减少锁的竞争所带来的用户态与内核态之间的切换；这种锁的优化实际上是通过`Java`对象头中的一些标志位来去实现的；对于锁的访问与改变实际上都与`Java`对象头息息相关。

* 从`JDK 1.6`开始，对象实例在堆当中会被划分为三个组成部分**：对象头、实例数据、对齐填充**

* 对象头主要也是由三块内容构成：

  1. `Mark Word` 标记池
  2. 指向类的指针
  3. 数组长度

  其中`Mark Word`(它记录了对象、锁及垃圾回收的信息，在64位JVM中，其长度也是64bit)的位信息包括了如下组成部分：

  1. 无锁标记
  2. 偏向锁标记
  3. 轻量级锁标记
  4. 重量级锁标记
  5. GC垃圾回收标记

  对于`synchronized`锁来说，锁的升级主要都是通过`Mark Word`中锁标志位是否是偏向锁标志来达成的；`synchronized`关键字所对应的锁都是**先从偏向锁开始，随着锁竞争的不断升级中，逐步演化至轻量级锁，最后则变成了重量级锁**。

* 对于锁的演化来说，会经过如下阶段：**无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁**,自旋锁属性轻量级锁的一种实现方式

* **偏向锁**：针对于一个线程来说，它的主要作用就是**优化同一个线程多次获取同一个锁的情况**；如果一个`synchronized`方法被一个线程访问，那么这个`synchronized`方法所在的对象就会在其`Mark Word`中将偏向锁进行标记，同时还会有一个字段来存储该线程的`ID`；当这个线程再次访问同一个`synchronized`方法时，它会检查这个对象的`Mark Word`的偏向锁标记以及是否指向了其线程`ID`，如果当前线程的`ID`与`Mark Word`中存储的线程`ID`相等，那么该线程就无需再去进入管程(`Monitor`)了，而是直接进入到该方法体中。

* 如果是另外一个线程访问这个`synchronized`方法，那么实际情况会如何呢？结果：偏向锁会被取消掉

* **轻量级锁**: 若第一个线程已经获取到了当前对象的锁，这时第二个线程又开始尝试争抢该对象的锁，由于该对象的锁已经被第一个线程获取到，因次它是偏向锁，而第二个线程在争抢时，会发现该对象头中的`Mark Word`已经是偏向锁，但里面存在的线程`ID`并不是自己（是第一个线程的`ID`），那么它会进行`CAS(Compare and Swap)`，从而获取到锁，这里面存在两种情况：

  * 获取锁成功：那么它会直接将`Mark Word`中的线程`ID`由第一个线程`ID`变成自己的`ID`（**偏向锁标记位保持不变），这样该对象依然会保存偏向锁的状态**
  * 获取锁失败：则表示这时可能会有多个线程同时在尝试争抢该对象的锁，那么这时**偏向锁就会进行升级，升级为轻量级锁**

* **自旋锁**：**若自旋失败(依然无法获取到锁)，那么锁就会转化为重量级锁**，在这种情况下，无法获取到锁的线程都会进入到`Monitor`（即内核态）,**自旋最大的特点就是避免了线程从用户态进入到内核态**。

* **重量级锁**：**线程最终从用户态进入到了内核态**

### 2.1 总结

* 同步锁在基于`mutex lock`的实现方式当中，因为`Monitor`是依赖于底层操作系统实现的，这样就存在**用户态与内核态之间的切换，所以会增加性能开销**。通过对象互斥锁的概念来保证共享数据操作的完整性。每个对象都对应一个可称为【互斥锁】的标记，**这个标记用于保证在任何时刻，只能一个线程访问该对象。**
* 那些处于`EntryList`与`WaitSet`中的线程均处于阻塞状态，阻塞操作是由操作系统来完成的，在`linux`下是通过`pthread_mutex_lock`函数实现的。**线程被阻塞后便会进入到内核调度状态，这会导致系统在用户态与内核态之间来回切换，严重影响锁的性能**。
* 解决上述问题的办法便是**自旋(Spin)**。其原理是：当发生对`Monitor`的争用时，若`Owner`(**当前拥有Monitor的线程**)能够在很短的时间内释放掉锁，则那些正在争用线程就可以稍微等待一下(即所谓的自旋)，在`Owner`线程释放锁之后，争用线程可能会立刻获取到锁，从而避免了系统阻塞。不过，当`Owner`运行的时间超过了临界值后，争用线程自旋一段时间后依然无法获取到锁，这时争用线程则会停止自旋而进入到阻塞状态。所以总体的思想是：先自旋，不成功再进行阻塞，尽量降低阻塞的可能性(**尽量避免用户态与内核态之间的切换**)，这对那些执行时间很短的代码块来说有极大的性能提升。显然，自旋在多处理器(多核心)上才有意义。

## 3. 互斥锁的属性

* `PTHREAD_MUTEX_TIMED_NP`:**这是缺省值，也就是普通锁**。当一个线程加锁以后，其余请求锁的线程将会形成一个等待队列，并且在解锁后按照优先级获取到锁。这种策略可以确保资源分配的公平性。
* `PTHREAD_MUTEX_RECURSIVE_NP`:**嵌套锁**。允许一个线程对同一个锁成功获取多次，并通过`unlock`解锁。如果是不同线程请求，则在加锁的线程解锁时重新进行竞争，对应Java中的可重入锁。
* `PTHREAD_MUTEX_ERRORCHECK_NP`:**检错锁**。如果一个线程请求同一个锁，则返回`EDEADLK`,否则与`PTHREAD_MUTEX_TIMED_NP`类型动作相同，这样就保证了当不允许多次加锁时不会出现最简单的情况下的死锁。
* `PTHREAD_MUTEX_ADAPTIVE_NP:`**适应锁**，动作最简单的锁类型，仅仅等待解锁后重新竞争

## 4. Monitor对象

* `Monitor`对象对应于底层的`C++`是`ObjectMonitor`对象，并且`WaitSet、EntryList、Owner`都是它的成员变量。

* ![4](C:\Users\Administrator\Desktop\笔记\Java并发\images\4.JPG)

* `ObjectWaiter`是当前执行线程的一个代理，并且是一个双向的链表结构

* ![5](C:\Users\Administrator\Desktop\笔记\Java并发\images\5.JPG)

* `_owner`:是一个指针，指向当前拥有`Monitor`线程的指针

* ```c++
  void *  volatile _owner;    // pointer to owning thread OR BasicLock
  ```

* `_WaitSet`: 所有在`Monitor`上等待的线程集合,它是一个`Objectwait`类型，即一个双向链表

* ```c++
  ObjectWaiter * volatile _WaitSet; // LL of threads wait()ing on the monitor
  ```

* `_EntryList`: 在线程阻塞时存放线程的一个集合,它是一个`Objectwait`类型，即一个双向链表

* ```c++
  ObjectWaiter * volatile _EntryList;  // Threads blocked on entry or reentry.
  ```

* 将调用`wait()`方法后的线程放置到`WaitSet`中的源代码实现

*  ![6](C:\Users\Administrator\Desktop\笔记\Java并发\images\6.JPG)

* 调用 `wait()`方法后线程释放`monitor`的源代码实现

* ```c++
  exit (true, Self) ;  // exit the monitor
  ```

* `notify()`方法的源代码实现,**从`WaitSet`中根据不同的策略获得到对应的线程，将其放入到`EntryList`中**

* ```c++
  void ObjectMonitor::notify(TRAPS) {
      	//如果waitset为空，则什么也不做
          if (_WaitSet == NULL) {
          TEVENT (Empty-Notify) ;
          return ;
          }
          int Policy = Knob_MoveNotifyee ;
          Thread::SpinAcquire (&_WaitSetLock, "WaitSet - notify") ;
          ObjectWaiter * iterator = DequeueWaiter() ;
      	//根据不同的策略，唤醒对应的线程，将线程加入到EntryList中
          if (Policy == 0) {       // prepend to EntryList
          ...
          } else {
              ...
          }
          if (Policy == 1) {      // append to EntryList
              ...
          } else
          if (Policy == 2) {      // prepend to cxq
              ...
          } else{
          if(Policy==3){      // append to cxq
          ...
          }
          else
          if(Policy< 4){
              ...
        }
  ```
  
* `WaitSet`和`EntryList`本质上就是一个**双向链表**

## 5. 锁的优化

### 5.1 编译器对于锁的优化

* **锁消除**：`JIT`(编译器)(`Just In Time`编译器) 可以在动态编译同步代码时，使用一种叫做**逃逸分析**的技术，来通过**该项技术判别程序中所使用的锁对象是否只被一个线程所使用**，而没有散布到其他线程当中；如果情况就是这样的话，**那么JIT编译器在编译这个同步代码时就不会生成synchronized关键字所标识的锁的申请与释放机器码，从而消除了锁的使用流程**。如下代码

* ```java
  public void method(){
      //object锁是method()方法中的一个局部变量
      Object object = new Object();
      synchronized (object){
          System.out.println("hello world");
      }
  }
  ```

* **锁粗化**：`JIT`编译器在执行动态编译时，**若发现前台相邻的`synchronized`块使用的是同一个锁对象，那么它就会把这几个`synchronized`块给合并为一个较大的同步块**，这样做的好处在于线程在执行这些代码时，就无需频繁申请与释放锁了，从而达到申请与释放锁一次，就可以执行完全部的同步代码块，从而提升了性能，如下代码

* ```java
  Object object = new Object();//object是类的成员变量
  //method()方法中synchronized块锁的都是同一个对象
  public void method(){
      synchronized (object){
          System.out.println("hello world");
      }
      synchronized (object){
          System.out.println("welcome");
      }
      synchronized (object){
          System.out.println("person");
      }
  }
  ```

23