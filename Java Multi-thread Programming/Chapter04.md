#第4章 Lock 的使用
- **ReentrantLock 类的使用**
- **ReentrantReadWriteLock 类的使用**

##4.1 使用 ReentrantLock 类
- 关键字 synchronized 与 wait() 和 notify()/notifyAll() 方法相结合可以实现等待／通知模式； 类 **ReentrantLock **可以借助 **Condition** 对象实现同样的功能

- **使用 Condition 实现等待／通知**
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

- - -



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
- **公平锁与非公平锁**
  - 公平锁：线程获取锁的顺序是按照线程加锁的顺序来分配的
   Lock lock = new ReentrantLock(true);
  - 非公平锁：抢占机制，随机获得锁
   Lock lock = new ReentrantLock(false);
  - 在默认的情况下，ReentrantLock 类使用的是非公平锁

- - -


** **
- 方法 **int getHoldCount()** 的作用是查询当前线程保持此锁定的个数，也就是调用 lock() 方法的次数 lock.getHoldCount()

- 方法 **int getQueueLength()**的作用是返回正等待获取此锁定的线程估计数 lock.getQueueLength()

- 方法 **int getWaitQueueLength(Condition condition)**的作用是返回等待与此锁定相关的给定条件 Condition 的线程估计数 lock.getWaitQueueLength(condition)
** **
- - -
- - -
- - -
** **
- 方法** boolean hasQueuedThread(Thread thread)** 的作用是查询指定的线程是否正在等待获取此锁定。

- 方法 **boolean hasQueuedThreads()** 的作用是查询是否有线程正在等待获取此锁定。

- 方法 **boolean hasWaiters(Condition condition)** 的作用是查询是否有线程正在等待与此锁定有关的 condition 条件
** **
- - -
- - -
- - -
** **
- 方法 **boolean isFair()**的作用是判断是不是公平锁

- 方法 **boolean isHeldByCurrentThread()**的作用是查询当前线程是否保持此锁定

- 方法 **boolean isLocked() ** 的作用是查询此锁定是否由任意线程保持
** **
- - -
- - -
- - -
** **
- 方法 ** void lockInterruptibly()**的作用是：如果当前线程未被中断泽获取此锁定，如果已经被中断则出现异常

- 方法 ** boolean tryLock()** 的作用是，仅在调用时锁定未被另一个线程保持的情况下，才获取该锁定

- 方法 ** boolean tryLock(long timeout, TimeUnit unit)** 的作用是，如果锁定在等待时间内没有被另一个线程保持，且当前线程未被中断，则获取该锁定
** **
- - -
- - -
- - -
** **
- 方法 **awaitUninterruptibly()** 的作用是当前线程在被中断前一直处于等待状态

- 方法 ** awatUntil()** 的作用是在等待时间到达前一直处于等待状态，也可以被其他线程提前唤醒
** **

##4.2 使用 ReentrantReadWriteLock 类
- 类 ReentrantLock 具有完全互斥排他的效果，即同一时间只有一个线程在执行 ReentrantLock.lock()方法后面的任务

- ReentrantReadWriteLock 读写锁有两个锁，一个是读操作相关的锁，也称为共享锁；一个是写相关的锁，也称为排他锁

- **读读共享**
多个线程同时执行 lock()方法后面的代码
```java
private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
......
lock.readLock().lock();
......
lock.readLock().unlock();
```
- **写写互斥**
只要出现 ‘写操作’，就是互斥的
```java
private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
......
lock.writeLock().lock();
......
lock.writeLock().unlock();
```
##总结：
  	Lock 是 synchronized 关键字的进阶，掌握 Lock 有助于学习并发包中源代码的实现原理，在并发包中大量的类使用了 Lock 接口作为同步的处理方式