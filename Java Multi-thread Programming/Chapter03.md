#第3章 线程间通信
- **使用 wait/notify 实现线程间的通信**
- **生产者／消费者模式的实现**
- **方法 join 的使用**
- **ThreadLocal类的使用**

##3.1 等待／通知机制
- **只能在同步方法或同步块中调用 wait()方法和 notify() 方法**，如果没有持有适当的锁，则抛出 IlleagalMonitorStateException

- wait 使线程停止运行，而 notify 使停止的线程继续运行

- 每个锁对象都有两个队列，分别是就绪队列和阻塞队列，一个线程被 wait 后就会进入阻塞队列

- **wait()锁释放与 notify() 锁不释放**
  -** **
  - 当方法 wait() 被执行后，锁被自动释放
  - 当方法 notify()被执行后，锁不自动释放，必须执行完 notify()方法所在的 synchronized同步代码块后才释放锁
  - sleep()方法不释放锁
  -** **

- 当线程呈 wait()状态时，调用线程对象的 interrupt()方法会出现 InterruptedException异常

- 调用 notify()方法一次只能随机通知一个线程进行唤醒，**notifyAll() 可以唤醒全部线程**

- **wait(long)** 等待某一时间内是否有线程对锁进行唤醒，如果超过这个时间则自动唤醒

- 通知过早，可设置一个标记 private boolean isFirstRun == false

- **生产者／消费者模式实现**
  ** **
  - ‘假死’ 的现象其实就是全部线程都进入 WAITNG 等待状态
  - ‘假死’ 出现的主要原因是有可能连续唤醒同类
  - 解决 ‘假死’ 的情况是使用 notifyAll()保证唤醒异类
  ** **

- **通过管道进行线程间通信**
 ** **
  - **字节流**
   - PipedInputStream (read) / PipedOutputStream (write)
   - outputStream.connect(inputStream)使两个 Stream 之间产生通信链接，然后进行数据的输入和输出
   
  - **字符流**
   - PipedReader / PipedWriter
 ** **
 
##3.2 方法 join 的使用
- **方法 join 的作用**是使所属的线程对象 x 正常执行 run() 方法中的任务，而使当前线程 z 进行无限期的阻塞，等待线程 x 销毁后再继续执行线程 z 后面的代码

- join 在内部使用 wait() 方法进行等待，而 synchronized 使用的是 ‘对象监视器’ 原理作为同步

- 在 **join()** 过程中，如果当前线程对象被中断 **interrupt()**，则当前线程出现异常

- ** join(long)** 设置等待时间，内部通过 wait(long) 来实现，所以 join(long）方法具有释放锁的特点

##3.3 类 ThreadLocal 的使用
- 类 ThreadLocal 解决的是**变量在不同线程间的隔离性**，也就是不同线程拥有自己的值，不同线程中的值是可以放入 ThreadLocal类中进行保存的

- 第一次调用 ThreadLocal 类对象的 get() 方法时返回的值是 null ，可以通过继承 ThreadLocal 类，覆盖 intialValue() 方法赋予初始值

## 3.4 类 InheritableThreadLocal 的使用
- **值继承**使用 InheritableThreadLocal 类可以让子线程从父线程中取得值

- **值继承再修改** 添加 childValue(Object parentValue) 方法

- **注意** 如果子线程在取得值的同时，主线程将 InheritableThreadLocal 中的值进行更改，那么子线程取到的值还是旧值


