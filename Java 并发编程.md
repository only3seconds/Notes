# 一. 区分一些概念

### 1. 并行、并发 & 同步、互斥 & 异步、多线程

- 并行：多处理器的情况下，多个进程在同一时刻运行。

- 并发：对于同一个处理器，多个进程在某个时间段内，都处于已启动运行到运行完毕之间的状态。
	其中，进程间有两种并发关系：同步和互斥。
	
- 同步：并发的进程间是依赖关系，前一个进程的输出是后一个进程的输入，相互协同完成一些事情。当第一个进程没有结束时第二个进程必须等待。具有同步关系的一组进程并发时发送的消息称为消息或事件。

- 互斥：并发的进程间相互排斥使用临界资源

- 异步：并发的进程间是依赖关系，前一个进程的输入是后一个进程的输出，相互协同完成一些事情。当第一个进程没有结束时，第二个进程无需傻傻等待第一个进程的结果，而是可以继续做自己的事情。线程就是实现异步的一个方式。异步是让调用方法的主线程不需要同步等待另一个线程的完成，之间通过消息或事件来通知对方，从而可以让主线程干其他的事情。

- 多线程：多线程是程序设计的逻辑层概念，它是进程中并发运行的一段代码。多线程只是实现异步的一种手段。

### 2. 进程 & 线程

（1）进程：进程是操作系统分配资源的最小单位

（2）线程：线程是 CPU 调度的最小单位


# 二. 线程状态转换

![](https://img-blog.csdnimg.cn/20190313215936761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

- New（新建): 创建后尚未启动

- Runnable（可运行）:可能正在运行，也可能正在等待 CPU 时间片。包含了操作系统线程状态中的 运行（Running ） 和 就绪（Ready）。

- Blocked（阻塞）：这个状态下，是在多个线程有同步操作的场景，比如正在等待另一个线程的 synchronized 块的执行释放，或者可重入的 synchronized 块里别人调用 wait() 方法，也就是线程在等待进入临界区。

- Waiting（无限期等待）：等待其它线程显式地唤醒，否则不会被分配 CPU 时间片。
	- 没有设置 Timeout 参数的 Object.wait() 方法  -> Object.notify() / Object.notifyAll()

	- 没有设置 Timeout 参数的 Thread.join() 方法 -> 被调用的线程执行完毕
	
- Timed Waiting（限期等待）：无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。
	- Thread.sleep() 方法 -> 时间结束

	- 设置了 Timeout 参数的 Object.wait() 方法 -> 时间结束 / Object.notify() / Object.notifyAll()

	- 设置了 Timeout 参数的 Thread.join() 方法 -> 时间结束 / 被调用的线程执行完毕

- Terminated（死亡）：一是线程因为 run 方法正常退出而自然死亡；二是因为一个没有捕获的异常终止了 run 方法而意外死亡。

# 三. Java 实现多线程的方式

## 1. 继承 Thread 类

```java
public class MyThread extends Thread {
    public void run() {
        // ...
    }
}

public static void main(String[] args) {
    MyThread mt = new MyThread();
    mt.start();
}
```

## 2. 实现 Runnable 接口

```java
public class MyRunnable implements Runnable {
    public void run() {
        // ...
    }
}
public static void main(String[] args) {
    MyRunnable instance = new MyRunnable();
    Thread thread = new Thread(instance);
    thread.start();
}
```

## 3. 实现 Callable 接口
Callable 可以有返回值，返回值通过 FutureTask 进行封装。

```java
public class MyCallable implements Callable<Integer> {
    public Integer call() {
        return 123;
    }
}
public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyCallable myCallable = new MyCallable();
        FutureTask<Integer> futureTask = new FutureTask<>(myCallable);
        Thread thread = new Thread(futureTask);
        thread.start();
        System.out.println(futureTask.get());
    }
```

**总结：**实现接口会更好一些，因为 Java 不支持多重继承，因此继承了 Thread 类就无法继承其它类，但是可以实现多个接口。

⚠️ **start()** 方法通知‘线程规划器’ 此线程已经准备就绪，等待调用线程的 run() 方法，具有异步执行的效果；如果调用 **thread.run()** 则是同步，此线程对象不交给 ‘线程规划器’来进行处理，而是由 main 主线程来调用，必须等run执行完才可以执行后面的代码。

# 四. 线程常用操作

## 1. 常用方法

- getId()方法就是获得线程的唯一标志。

- currentThread()方法可以返回代码段正在被哪个线程调用的信息

- isAlive()方法就是判断当前的线程是否处于活动状态，活动状态就是线程处于运行或准备运行,即Runnable 状态

- 方法 sleep()的作用是在指定的毫秒数内让‘正在执行的线程’休眠(暂停执行)。这个正在执行的线程就是 this.currentThread()返回的线程。sleep() 可能会抛出 InterruptedException，因为异常不能跨线程传播回 main() 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。

- 对静态方法 Thread.yield() 的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换
给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。

**为什么 Thread 的 sleep() 和 yield() 方法是静态的?**

	Thread 类的 sleep() 和 yield() 方法将在正在执行的线程上运行, 所以在其他处于等待状态的
	线程上调用这些方法是没有意义的。这就是为什么这些方法是静态的。它们可以在当前正在执行的线程中
	工作，避免程序员错误的认为可以在其他非运行线程调用这些方法。
	
- setDamon(true)设置为守护线程

**什么是守护线程？**

	当我们在java程序中创建一个线程的时候，它默认就是用户线程。

	任何一个守护线程是在后台运行的线程，它是所有用户线程的保姆，它为其他线程的运行提供便利服
	务，最典型的就是GC(垃圾回收器）。
	守护线程不会阻止 java 虚拟机的终止，当没有用户线程运行的时候，jvm 虚拟机关闭程序并且退
	出。
	
## 2. 中断

一个线程执行完毕之后会自动结束，如果在运行过程中发生异常也会提前结束。

 - interrupt()
 
	通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于Blocked、Waitting或者
	Timed Waitting 状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能
	中断 I/O 阻塞和 synchronized 锁阻塞。
	
- interrupted()

	如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 
	InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。
	但是调用 interrupt() 方法会设置线程的中断标记，此时调用 interrupted() 方法会返回 
	true。因此可以在循环体中使用 interrupted() 方法来判断线程是否处于中断状态，从而提前结束
	线程。
	
```java

public class InterruptExample {

    private static class MyThread extends Thread {
        @Override
        public void run() {
            while (!interrupted()) {
                System.out.println("PPP");
            }
            System.out.println("Thread end");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread = new MyThread();
        thread.start();
        thread.interrupt();
    }
}
```
# 五. 线程安全

## 1. 什么是线程安全？

Java语言中各种操作**共享的数据**是分为以下5类：

- 不可变： 不可变的对象一定是线程安全的，无论是对象的方法实现还是方法的调用者，都不需要采取任何的线程安全保障措施。

	- final
	- String

- 绝对线程安全：当多个线程访问一个对象时，如果不用考虑这些线程在运行时环境下的调度和交替执行，也不需要进行额外的同步，或者在调用方进行任何的协调操作，调用这个对象的行为都可以获得正确的结果，那这个对象就是线程安全的。

- 相对线程安全：相对线程安全就是我们通常意义上所讲的线程安全，它需要保证对这个对象单独的操作是线程安全的，我们在调用的时候不需要额外的保障，但是对于特定顺序的连续调用，就可能需要在调用端使用额外的同步手段来保证调用的正确性。

- 线程兼容：对象本身并不是线程安全的，但是可以通过在调用端正确地使用同步手段来保证对象在并发环境中可以安全地使用。

- 线程对立：指调用端无论是否采取了同步措施，都无法在多线程环境中使用的代码。

## 2. 如何实现线程安全？

线程安全的实现方法有以下几种：

### 2.1 互斥同步(阻塞同步）

同步是指多个线程并发访问共享数据时，保证共享数据在同一时刻只被一个（或者是一些，使用信号量的时候）使用，而互斥是实现同步的一种手段。

互斥同步属于一种悲观的并发策略，最主要的问题就是进行线程阻塞和唤醒所带来的性能问题。

Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock。

### 2.1.1 synchronized

#### （1）基本使用

**同步一个普通方法**

- 关键字 synchronized 取得的锁是对象锁，作用于同一个对象。哪个线程先执行带 synchronized 关键字的方法，哪个线程就持有该方法所属对象的锁 Lock，其他访问该对象的线程只能等待。如果多个线程访问多个对象，则 JVM 会创建多个锁.

- 在一个 synchronized 方法／块的内部调用本类的其他 synchronized 方法／块时，是永远可以得到的; 子类是完全可以通过 ‘可重入锁’ 调用父类的同步方法的

- 出现异常，锁自动释放

- 同步不能继承，所以还需在子类的方法中添加 synchronized 关键字

**同步一个代码块**：和 synchronized 方法一样，synchronized（this) 代码块也是锁定当前对象的

**同步一个类**：用于整个类，也就是说两个线程调用同一个类的不同对象上的这种同步语句，也会进行同步

**同步一个静态方法**：作用于整个类

#### （2）线程间通信

**等待／通知 机制**

- 只能在同步方法或同步块中调用 wait()方法和 notify() 方法，如果没有持有适当的锁，则抛出 IlleagalMonitorStateException

- wait 使线程停止运行，而 notify 使停止的线程继续运行

- 每个锁对象都有两个队列，分别是就绪队列和阻塞队列，一个线程被 wait 后就会进入阻塞队列

- 当方法 wait() 被执行后，锁被自动释放; 当方法 notify()被执行后，锁不自动释放，必须执行完 notify()方法所在的 synchronized 同步代码块后才释放锁

- sleep()方法不释放锁

- 当线程呈 wait()状态时，调用线程对象的 interrupt()方法会出现 InterruptedException 异常

- 调用 notify()方法一次只能随机通知一个线程进行唤醒，notifyAll() 可以唤醒全部线程

- wait(long) 等待某一时间内是否有线程对锁进行唤醒，如果超过这个时间则自动唤醒

**wait() 和 sleep() 有什么区别 ？**

	wait 是 Object类的方法，sleep 是 Thread 类的方法
	sleep 休眠一定时间后自动唤醒，无参数的 wait 需要其他线程 notify
	wait 释放锁，sleep 不释放锁
	
**方法 join 的使用**

	方法 join 的作用是使所属的线程对象 x 正常执行 run() 方法中的任务，而使当前线程 z 进行无限期的阻塞，等待线程 x 销毁后再继续
	执行线程 z 后面的代码

	join 在内部使用 wait() 方法进行等待

	在join()过程中，如果当前线程对象被中断 interrupt()，则当前线程出现异常

#### （3）synchronize 和 volatile

**volatile 关键字在 java 中有什么作用？**

	volatile 的作用是强制从公共堆栈中取得变量的值，而不是从线程私有数据栈中取得变量的值。多线
	程读取共享变量时可以获取最新值.
	对于 volatile 修饰的变量， JVM只是保证从主存加载到线程工作内存的值是最新的; 访问
	volatile变量并不会执行加锁操作，因此不会使执行线程阻塞; volatile 变量解决的是变量在多个
	线程之间的可见性。
	
**比较 synchronized 和 volatile**

	(1) 关键字 volatile 是线程同步的轻量级实现，volatile 性能更好，volatile 只能用
		来修饰变量，synchronized 用来修饰方法和代码块;
	(2) 多线程访问 volatile 不会发生阻塞;
	(3) volatile 保证数据的可见性，但不保证原子性； synchronized 既可以保证原子性，
		也可以保证可见性，因为它会将私有内存和公有内存的数据做同步;
	(4) volatile 解决的是变量在多个线程之间的可见性； 而synchronized 解决的是多个线
		程之间访问资源的同步性.
		
#### （4）synchronized 是怎么实现的？

**Java 对象头与 Monitor**

	在JVM中，对象在内存中的布局分为三块区域：对象头、实例数据和对齐填充。
	
对象头的结构如图：

![](https://img-blog.csdnimg.cn/20190415164923950.png)

	synchronized的对象锁，锁标识位为10，其中指针指向的是monitor对象（也称为管程或监视器锁）
	的起始地址。
	
	每个对象都存在着一个 monitor 与之关联，当一个 monitor 被某个线程持有后，它便处于锁定状
	态。
	
	在Java虚拟机(HotSpot)中，monitor是由ObjectMonitor实现的。ObjectMonitor中有两个队列，_WaitSet 和 _EntryList，用来保存ObjectWaiter对象列表( 每个等待锁的线程都会被封装成ObjectWaiter对象)，_owner指向持有ObjectMonitor对象的线程，当多个线程同时访问一段同步代码时，首先会进入 _EntryList 集合，当线程获取到对象的monitor 后进入 _Owner 区域并把monitor中的owner变量设置为当前线程同时monitor中的计数器count加1，若线程调用 wait() 方法，将释放当前持有的monitor，owner变量恢复为null，count自减1，同时该线程进入 WaitSe t集合中等待被唤醒。若当前线程执行完毕也将释放monitor(锁)并复位变量的值，以便其他线程进入获取monitor(锁)。	
**synchronized 代码块底层原理：**

	synchronized语句块的实现使用的是 monitorenter 和 monitorexit 指令。
	
	monitorenter 指令指向同步代码块的开始位置，monitorexit指令则指明同步代码块的结束位置。
	当执行monitorenter指令时，当前线程将试图获取 objectref(即对象锁) 所对应的 monitor 的
	持有权，当 objectref 的 monitor 的进入计数器为 0，那线程可以成功取得 monitor，并将计数
	器值设置为 1，取锁成功。如果当前线程已经拥有 objectref 的 monitor 的持有权，那它可以重入
	这个 monitor，重入时计数器的值也会加 1。倘若其他线程已经拥有 objectref 的 monitor 的所
	有权，那当前线程将被阻塞，直到正在执行线程执行完毕，即 monitorexit 指令被执行，执行线程将
	释放 monitor(锁)并设置计数器值为0 ，其他线程将有机会持有 monitor 。
	
	值得注意的是编译器将会确保无论方法通过何种方式完成，方法中调用过的每条 monitorenter 指令
	都有执行其对应 monitorexit 指令，而无论这个方法是正常结束还是异常结束。
	
**synchronized 方法底层原理:**

	方法级的同步是隐式，即无需通过字节码指令来控制的，它实现在方法调用和返回操作之中。
	
	JVM可以从方法常量池中的方法表结构(method_info Structure) 中的 ACC_SYNCHRONIZED 访问
	标志区分一个方法是否同步方法。当方法调用时，调用指令将会检查方法的 ACC_SYNCHRONIZED 访问
	标志是否被设置，如果设置了，执行线程将先持有monitor（虚拟机规范中用的是管程一词）， 然后再
	执行方法，最后在方法完成(无论是正常完成还是非正常完成)时释放monitor。
	
	在方法执行期间，执行线程持有了monitor，其他任何线程都无法再获得同一个monitor。如果一个同
	步方法执行期间抛出了异常，并且在方法内部无法处理此异常，那这个同步方法所持有的monitor将在异
	常抛到同步方法之外时自动释放。
		
### 2.1.2 ReentrantLock

#### (1) 基本使用

- 关键字 synchronized 与 wait() 和 notify()/notifyAll() 方法相结合可以实现等待／通知模式； 类**ReentrantLock**可以借助 **Condition** 对象实现同样的功能

- 使用 Condition 实现等待／通知

```java
  private Lock lock = new ReentrantLock();
  public Condition condition = lock.newCondition();
  ......
  lock.lock(); //获取同步监视器
  ......
  condition.await(); / condition.await(long time, TimeUnit unit);
  ......
  condition.signal(); / condition.signalAll();
  ......
  lock.unlock(); //释放锁
```

- 使用多个 Condition 对象实现唤醒指定种类的线程

```java
private Lock lock = new ReentrantLock();
public Condition conditionA = lock.newCondition();
public Condition conditionB = lock.newCondition();
  	......
public void awaitA(){
	lock.lock();
    ......
    conditionA.await();
    ......
    lock.unlock();
  	}
public void awaitB(){
    lock.lock();
    ......
    conditionB.await();
    ......
    lock.unlock();
  	}
public void signalAll_A(){
	lock.lock();
    ......
    conditionA.signalAll();
    ......
    lock.unlock();
}
public void signalAll_B(){
	lock.lock();
    ......
    conditionB.signalAll();
    ......
    lock.unlock();
}
```

#### （2）高级功能

相比 synchronized, ReentrantLock 主要增加了以下个高级功能：

- 等待可中断：当持有锁的线程长期不释放锁的时候，正在等待的线程可以放弃等待，改为处理其他事情。

- 可实现公平锁：公平锁是线程获取锁的顺序是按照线程加锁的顺序来分配的 Lock lock = new ReentrantLock(true);非公平锁是抢占机制，随机获得锁. 在默认的情况下，ReentrantLock 类使用的是非公平锁，synchronized 是非公平锁。

- 锁可以绑定多个条件：ReentrantLock 对象可以绑定多个 Condition 对象。


	
### 2.1.3 synchronized 和 ReentrantLock 比较

1. 锁的实现： synchronized 是 JVM 实现的，而 ReentrantLock 是 JDK 实现的。

2. 性能： 新版本 Java 对 synchronized 进行了很多优化，例如自旋锁等。目前来看它和 ReentrantLock 的性能基本持平了，因此性能因素不再是选择 ReentrantLock 的理由。synchronized 有更大的性能优化空间，应该优先考虑 synchronized。

3. 使用选择：除非需要使用 ReentrantLock 的高级功能，否则优先使用 synchronized。这是因为 synchronized 是 JVM 实现的一种锁机制，JVM 原生地支持它，而 ReentrantLock 不是所有的 JDK 版本都支持。并且使用 synchronized 不用担心没有释放锁而导致死锁问题，因为 JVM 会确保锁的释放。ReentrantLock在加锁和解锁处需要通过 lock() 和 unlock() 显示指出。所以一般会在 finally 块中写 unlock() 以防死锁。

4. 机制：synchronized 原始采用的是 CPU 悲观锁机制，即线程获得的是独占锁，独占锁是一种悲观锁，共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程。

**悲观锁和乐观锁💕**

乐观锁对应于生活中乐观的人总是想着事情往好的方向发展，悲观锁对应于生活中悲观的人总是想着事情
往坏的方向发展。这两种人各有优缺点。

	悲观锁：假定会发生并发冲突，独占锁，屏蔽一切可能违反数据完整性的操作。悲观锁大多数情况下依靠
	数据库的锁机制实现，以保证操作最大程度的独占性。但随之而来的就是数据库性能的大量开销，特别是
	对长事务而言，这样的开销往往无法承受。
	
	乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。乐观锁不能解决脏读的问
	题。 乐观锁，大多是基于数据版本（ Version ）记录机制实现。
	何谓数据版本？即为数据增加一个版本标识，在基于数据库表的版本解决方案中，一般是通过为数据库表
	增加一个 “version” 字段来实现。读取出数据时，将此版本号一同读出，之后更新时，对此版本号加
	一。此时，将提交数据的版本数据与数据库表对应记录的当前版本信息进行比对，如果提交的数据版本号
	大于数据库表当前版本号，则予以更新，否则认为是过期数据。

### 2.2 非阻塞同步

非阻塞同步属于一种了乐观的并发策略。

#### 2.2.1 CAS (Compare And Swap)

![](https://img-blog.csdn.net/20170701155737036?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamF2YXplamlhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

	CAS 有三个操作数，内存中的值是 V, 我一直以为的值是 E, 我现在想把它修改成的值 N。首先我会比较 E 和 V 的值，如果相同，说明没
	人改过这个值，则直接用 N 覆盖 V；如果不同，说明有人改过这个值，则返回内存中的值 V。

### 2.3 无同步方案

无同步方案就是避免共享数据，天生就是线程安全的。

#### 2.3.1 可重入代码

	如果一个方法，它的结果是可以预测的，只要输入了相同的数据，就都能返回相同的结果，那它就满足可重入性的要求，就是线程安全的。

#### 2.3.2 线程本地存储

**java.lang.ThreadLocal的使用**
 
- 类 ThreadLocal 解决的是**变量在不同线程间的隔离性**，也就是不同线程拥有自己的值，不同线程中的值是可以放入 ThreadLocal类中进行保存的

- 第一次调用 ThreadLocal 类对象的 get() 方法时返回的值是 null ，可以通过继承 ThreadLocal 类，覆盖 intialValue() 方法赋予初始值	
	
# 六.  Executor 线程池框架

	Executor 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个
	任务的执行互不干扰，不需要进行同步操作。
	
**主要有以下 Executor 线程池:**

- CachedThreadPool：创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
- FixedThreadPool：所有任务只能使用固定大小的线程，超出的线程会在队列中等待；

- ScheduledThreadPool：创建一个定长线程池，支持定时及周期性任务执行；

- SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

**为什么引入Executor线程池框架？**

- 调用 new Thread() 创建的线程缺乏管理，而且可以无限制创建，之间相互竞争，会导致过多占用系统资源。

- 线程池可以重用存在的线程，减少对象创建、消亡的开销。

- 线程池提供定时执行、定期执行、单线程、并发数控制等功能。

# 常见问题

## 1.什么是线程池？如果让你设计一个动态大小的线程池，如何设计，应该有哪些方法？线程池创建的方式？

**什么是线程池？**

	线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不
	用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销。

**设计一个动态大小的线程池，如何设计，应该有哪些方法？**

	一个线程池包括以下四个基本组成部分：

	（1）线程管理器 (ThreadPool)：用于创建并管理线程池，包括创建线程，销毁线程池，添加新任
		 务；
	（2）工作线程 (PoolWorker)：线程池中线程，在没有任务时处于等待状态，可以循环的执行任务；
	（3）任务接口 (Task)：每个任务必须实现的接口，以供工作线程调度任务的执行，它主要规定了任务
	    的入口，任务执行完后的收尾工作，任务的执行状态等；
	（4）任务队列 (TaskQueue)：用于存放没有处理的任务。提供一种缓冲机制。

**线程池四种创建方式**

	Java 通过 Executors 提供四种线程池。
	
## 2. 线程安全相关问题
	
**既然锁和 synchronized 即可保证原子性也可保证可见性，为何还需要 volatile？**

	synchronized和锁需要通过操作系统来仲裁谁获得锁，开销比较高，而 volatile 开销小很多。
	
**既然锁和 synchronized 可以保证原子性，为什么还需要 AtomicInteger 这种的类来保证原子操作？**

	synchronized 需要通过操作系统来仲裁谁获得锁，开销比较高，而 AtomicInteger 是通过CPU级
	的 CAS 操作来保证原子性，开销比较小。所以使用 AtomicInteger 的目的还是为了提高性能。
	
	
## 3. 如何减少线程上下文切换？

线程不是越多就越好的，因为线程上下文切换是有性能损耗的

减少线程上下文切换的方法：

	（1）无锁并发编程。多线程竞争时，会引起上下文切换，所以多线程处理数据时，可以用一些办法来避
	    免使用锁，如将数据的 ID 按照Hash取模分段，不同的线程处理不同段的数据
	    
	（2）CAS算法。Java 的 Atomic 包使用 CAS 算法来更新数据，而不需要加锁。
	
	（3）控制线程数量。如果是IO密集型的话，线程可以多一些。如果是CPU密集型的话，线程不宜太多。

	（4）协程。在单线程里实现多任务的调度，并在单线程里维持多个任务间的切换。协程可以看成是用户
	    态自管理的“线程”。不会参与CPU时间调度，没有均衡分配到时间。非抢占式的
	    
## 4. 线程间通信和进程间通信

**线程间通信：**

- synchronized 同步，质上就是 “共享内存” 式的通信；

- while 轮询的方式，线程A不断地改变条件，线程 ThreadB 不停地通过 while 语句检测这个条件是否成立；

- wait/notify 机制；

- 管道通信，java.io.PipedInputStream 和 java.io.PipedOutputStream 进行通信。

**进程间通信：**

- 管道（Pipe）、命名管道（named pipe）

- 信号（Signal）

- 消息（Message）队列：消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺

- 共享内存 

- 内存映射（mapped memory）每一个使用该机制的进程通过把一个共享的文件映射到自己的进程地址空间来实现通信。

- 信号量（semaphore）

- 套接口（Socket），可用于不同机器之间的进程间通信

## 5. 并发包(J.U.C)下面，都用过什么？

![](https://img-blog.csdnimg.cn/20190315104742997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2pvdXJuZXlfVHJpcGxlUA==,size_16,color_FFFFFF,t_70)

## 6. 从volatile说到,i++原子操作,线程安全问题

[从volatile说到,i++原子操作,线程安全问题](https://blog.csdn.net/zbw18297786698/article/details/53420780)


### 参考资料

- [深入理解Java并发之synchronized实现原理](https://blog.csdn.net/javazejian/article/details/72828483)



	



	









