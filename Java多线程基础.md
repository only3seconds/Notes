---

# java多线程技能

---

## 1. 使用多线程
	实现多线程编程的方式主要有两种，一种是继承 Thread 类，另一种是实现 Runnable 接口.

### 继承 Thread 类
 **start()** 方法通知‘线程规划器’ 此线程已经准备就绪，等待调用线程的 run() 方法，具有异步执行的效果；如果调用 **thread.run()** 则是同步，此线程对象不交给 ‘线程规划器’来进行处理，而是由mian 主线程来调用，必须等run执行完才可以执行后面的代码。

### 实现 Runnable 接口
    Thread.java 的构造函数
    Thread()
    Thread(Runnable target)
    Thread(Runnable target, String name)
    Thread(String name)
    Thread(ThreadGroup group, String name)
    Thread(ThreadGroup group, Runnable target)
    Thread(ThreadGroup group, Runnable target, String name)
    Thread(ThreadGroup group, Runnable target, String name, long stackSize)
    (分配新的Thread对象，将target作为其运行对象，将指定的 name 作为其名称，并作为group所引用的线程组的一员,并具有指定的堆栈大小)


## 2. 一些方法

- currentThread()方法可以返回代码段正在被哪个线程调用的信息

- isAlive()方法就是判断当前的线程是否处于活动状态，活动状态就是线程处于运行或准备运行的状态

- 方法 sleep()的作用是在指定的毫秒数内让‘正在执行的线程’休眠(暂停执行)。这个正在执行的线程就是 this.currentThread()返回的线程。

- getId()方法就是获得线程的唯一标志。

- yield()方法的作用是放弃当前的cpu资源，将它让给其他任务去占用cpu执行时间，但放弃的时间不确定。

## 3. 停止线程
- 抛异常法实现程序的停止

- **interrupt()**方法仅仅是在当前线程中打了一个停止的标记，并不是真正的停止线程
- **interrupted()**                public static boolean interrupted()
测试当前线程是否已经中断，当前线程指运行 this.interrupted()方法的线程 interrupted()方法具有清楚状态的功能.

code:

```java
     public class Run() {
     	public static void main(String[], args) {
        Thread.currentThread().interrupt();
        System.out.println("是否停止1？=" + Thread.interrupted());
        System.out.println("是否停止2？=" + Thread.interrupted());
        }
      }
```
运行结果：

     是否停止1？=true
     是否停止2？=false

code解释：

- **isInterrupted()**   public boolean isInterrupted()
测试线程Thread对象是否已经是中断状态，但不清除状态标志

- 如果在sleep状态下停止某一线程，会进入catch语句，并且清除停止状态值，使之变成false


## 4. 线程的优先级
- 设置线程的优先级用setPriority()方法

- **优先级具有规则性**：cpu尽量将执行资源让给优先级比较高的线程，高优先级的线程总是大部分先执行完，但并不代表全部先执行完，即**优先级具有随机性**

- 优先级高的运行得快

## 5. 守护线程
- 任何一个守护线程都是JVM中所有非守护线程的保姆，它为其他线程的运行提供便利服务，最典型的应用就是GC（垃圾回收器）
- thread.setDamon(true)设置为守护线程
  thread.setDamon(false)设置为用户线程，默认为用户线程

---

#  对象及变量的并发访问

---

## 1. synchronized 同步方法

- 方法内部的私有变量不存在 ‘非线程安全’ 问题

- 如果多个线程共同访问1个对象中的实例变量，则有可能出现 ‘非线程安全’ 问题; 解决办法：在方法前加 synchronized。

- 关键字 synchronized 取得的锁是对象锁，哪个线程先执行带 synchronized 关键字的方法，哪个线程就持有该方法所属对象的锁 Lock，其他访问该对象的线程只能等待。如果多个线程访问多个对象，则 JVM 会创建多个锁.

- A 线程先持有 object 对象的 Lock 锁，B 线程可以以异步的方式调用 object 对象中的非 synchronized 类型的方法

- 发生脏读的情况是在读取变量时，此变量已经被其他线程更改过了. 脏读是通过 synchronized 关键字解决的

- 在一个 synchronized 方法／块的内部调用本类的其他 synchronized 方法／块时，是永远可以得到的; 子类是完全可以通过 ‘可重入锁’ 调用父类的同步方法的

- 出现异常，锁自动释放

- 同步不能继承，所以还需在子类的方法中添加 synchronized 关键字

## 2. synchronized 同步语句块
- synchronized 方法是对当前对象进行加锁，而 synchronized 代码块是对某一个对象进行加锁

- 和 synchronized 方法一样，synchronized（this) 代码块也是锁定当前对象的

- 使用 ‘synchronized（非 this 对象 x)同步代码块’格式进行同步操作时，对象监视器必须是同一个对象; 当其他线程执行x对象中 synchronized 同步方法时呈同步效果; 当其他线程执行x对象中 synchronized（this) 代码块时也呈同步效果.

- 同步的原因是使用了同一个‘对象监视器’

- 数据类型 String 的常量池特性. 大多数情况下，同步 synchronized 代码块都不使用 String 作为锁对象，可以用 new Object（）实例化一个Object对象，但它并不放入缓存中

- 锁对象的改变 : 只要对象不变，即使对象的属性被改变，运行的结果还是同步

-  内置类
	```java
	import test.PublicClass.PrivateClass;
	PrivateClass privateClass = PublicClass.new privateClass();
	
	//静态内置类
    import test.PublicClass.PrivateClass;
    PrivateClass privateClass = new privateClass();
	```
     内置类与同步本质还是看持有的锁是不是相同的



## 3. 静态同步 synchronized 方法与 synchronized（class)代码块
    （1）synchronized public static void fun() {} 是对当前的 *.java 文件对应
	    的 Class 类进行持锁
    （2）对象锁和 Class 锁是不同的锁
    （3）即使是不同的对象，静态的同步方法还是同步执行
    （4）同步 synchronized（class) 代码块的作用和 synchronized static 方法的作用
	    一样


## 4. volatile 关键字
-  volatile 的作用是强制从公共堆栈中取得变量的值，而不是从线程私有数据栈中取得变量的值。多线程读取共享变量时可以获取最新值

* 对于 volatile 修饰的变量， JVM只是保证从主存加载到线程工作内存的值是最新的

## 5. 比较 synchronized 和 volatile
	(1) 关键字 volatile 是线程同步的轻量级实现，volatile 性能更好，volatile 只能用
		来修饰变量，synchronized 用来修饰方法和代码块;
	(2) 多线程访问 volatile 不会发生阻塞;
	(3) volatile 保证数据的可见性，但不保证原子性； synchronized 既可以保证原子性，
		也可以保证可见性，因为它会将私有内存和公有内存的数据做同步;
	(4) volatile 解决的是变量在多个线程之间的可见性； 而synchronized 解决的是多个线
		程之间访问资源的同步性.

---

# 线程间通信

---


## 1. 等待／通知机制
- 只能在同步方法或同步块中调用 wait()方法和 notify() 方法，如果没有持有适当的锁，则抛出 IlleagalMonitorStateException

- wait 使线程停止运行，而 notify 使停止的线程继续运行

- 每个锁对象都有两个队列，分别是就绪队列和阻塞队列，一个线程被 wait 后就会进入阻塞队列

- 当方法 wait() 被执行后，锁被自动释放; 当方法 notify()被执行后，锁不自动释放，必须执行完 notify()方法所在的 synchronized同步代码块后才释放锁

- sleep()方法不释放锁

- 当线程呈 wait()状态时，调用线程对象的 interrupt()方法会出现 InterruptedException异常

- 调用 notify()方法一次只能随机通知一个线程进行唤醒，notifyAll() 可以唤醒全部线程

- wait(long) 等待某一时间内是否有线程对锁进行唤醒，如果超过这个时间则自动唤醒

- 通知过早，可设置一个标记 private boolean isFirstRun == false


- 通过管道进行线程间通信


## 2. 方法 join 的使用
- **方法 join 的作用**是使所属的线程对象 x 正常执行 run() 方法中的任务，而使当前线程 z 进行无限期的阻塞，等待线程 x 销毁后再继续执行线程 z 后面的代码

- join 在内部使用 wait() 方法进行等待，而 synchronized 使用的是 ‘对象监视器’ 原理作为同步

- 在 **join()** 过程中，如果当前线程对象被中断 **interrupt()**，则当前线程出现异常

- ** join(long)** 设置等待时间，内部通过 wait(long) 来实现，所以 join(long）方法具有释放锁的特点

## 3. 类 ThreadLocal 的使用
- 类 ThreadLocal 解决的是**变量在不同线程间的隔离性**，也就是不同线程拥有自己的值，不同线程中的值是可以放入 ThreadLocal类中进行保存的

- 第一次调用 ThreadLocal 类对象的 get() 方法时返回的值是 null ，可以通过继承 ThreadLocal 类，覆盖 intialValue() 方法赋予初始值

## 4. 类 InheritableThreadLocal 的使用
- **值继承**使用 InheritableThreadLocal 类可以让子线程从父线程中取得值

- **值继承再修改** 添加 childValue(Object parentValue) 方法

- **注意** 如果子线程在取得值的同时，主线程将 InheritableThreadLocal 中的值进行更改，那么子线程取到的值还是旧值

---

# Lock 的使用

---

## 1. 使用 ReentrantLock 类
- 关键字 synchronized 与 wait() 和 notify()/notifyAll() 方法相结合可以实现等待／通知模式； 类 **ReentrantLock **可以借助 **Condition** 对象实现同样的功能

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
- **公平锁与非公平锁** 公平锁：线程获取锁的顺序是按照线程加锁的顺序来分配的 Lock lock = new ReentrantLock(true); 非公平锁：抢占机制，随机获得锁. 在默认的情况下，ReentrantLock 类使用的是非公平锁

## 2. 一些方法
- 方法 **int getHoldCount()** 的作用是查询当前线程保持此锁定的个数，也就是调用 lock() 方法的次数 lock.getHoldCount()

- 方法 **int getQueueLength()**的作用是返回正等待获取此锁定的线程估计数 lock.getQueueLength()

- 方法 **int getWaitQueueLength(Condition condition)**的作用是返回等待与此锁定相关的给定条件 Condition 的线程估计数 lock.getWaitQueueLength(condition)

- 方法** boolean hasQueuedThread(Thread thread)** 的作用是查询指定的线程是否正在等待获取此锁定。

- 方法 **boolean hasQueuedThreads()** 的作用是查询是否有线程正在等待获取此锁定。

- 方法 **boolean hasWaiters(Condition condition)** 的作用是查询是否有线程正在等待与此锁定有关的 condition 条件

- 方法 **boolean isFair()**的作用是判断是不是公平锁

- 方法 **boolean isHeldByCurrentThread()**的作用是查询当前线程是否保持此锁定

- 方法 **boolean isLocked() ** 的作用是查询此锁定是否由任意线程保持

- 方法 ** void lockInterruptibly()**的作用是：如果当前线程未被中断泽获取此锁定，如果已经被中断则出现异常

- 方法 ** boolean tryLock()** 的作用是，仅在调用时锁定未被另一个线程保持的情况下，才获取该锁定

- 方法 ** boolean tryLock(long timeout, TimeUnit unit)** 的作用是，如果锁定在等待时间内没有被另一个线程保持，且当前线程未被中断，则获取该锁定

- 方法 **awaitUninterruptibly()** 的作用是当前线程在被中断前一直处于等待状态

- 方法 **awatUntil()** 的作用是在等待时间到达前一直处于等待状态，也可以被其他线程提前唤醒


## 3. 使用 ReentrantReadWriteLock 类
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
```

---

# 定时器 Timer

---
	Timer 类主要负责计划任务的功能,也就是在指定的时间开始执行某一个任务;封装任务的类是
	TimerTask 类，它是一个抽象类，执行计划任务的代码要放在 TimerTask 的子类中.

## 1.  与计划任务有关的方法

#### schedule(TimerTask task, Date date)
在指定的日期执行一次某一任务

- 创建一个 Timer 就是启动一个新的线程，这个新的线程是非守护线程，一直在运行 Timer timer = new Timer(true); // 守护线程
- 如果执行任务的时间早于当前时间，则立即执行任务
- 一个 TimerTimerTask中可以运行多个 TimerTask 任务，kTimerTask 是以队列的方式一个一个被顺序地执行

#### schedule(Timetask task, Date firstTime, long period) 
在指定的日期之后按指定的间隔周期，无限循环地执行某一任务

- 任务执行时间被延时，还得一个一个运行，运行周期增长
- TimerTask 类的 cancel() 方法是将自身从任务队列中移除，其他任务不受影响
- Timer 类的 cancel() 方法是将任务队列中的全部任务请空。**注意**： Timer 类的 cancel()方法有时并没有争抢到 queue 锁，则任务正常运行

#### schedule(TimerTask task, long delay)
作用是在以执行 schedule(TimerTask task, long delay 方法当前时间为参考时间，在此时间基础上延迟指定的毫秒数后执行一次 TimerTask 任务

#### schedule(TimerTask task, long delay，long period)
作用是在以执行 schedule(TimerTask task, long delay 方法当前时间为参考时间，在此时间基础上延迟指定的毫秒数 delay 后以某一时间间隔 period 无限次数地执行 TimerTask 任务

#### scheduleAtFixedRate(TimerTask task, Date firstTime, long period)
在指定的日期之后按指定的间隔周期，无限循环地执行某一任务

## 2. 小结
- 凡是使用方法中带有 period 参数的，都是无限循环执行 TimerTask 中的人物
- 方法 schedule() 和 scheduleAtFixedRate() 都会按顺序执行，所以不需要考虑非线程安全问题
- 方法 schedule() 和 scheduleAtFixedRate()主要区别在于有没有追赶性，方法 schedule()没有追赶性；方法 scheduleAtFixedRate()具有追赶性，计划执行时间早于当前时间时，将两个时间段内的时间所对应的 Task 任务被补‘充性地’执行

---

# 单例模式与多线程

---
	单例模式
	Java中单例模式定义：“一个类有且仅有一个实例，并且自行实例化向整个系统提供。”
	3要素：一是单例模式的类只提供私有的构造函数，二是类定义中含有一个该类的静态私有对
	象，三是该类提供了一个静态的公有的函数用于创建或获取它本身的静态私有对象。


## 1. 立即加载 ／ “饿汉模式”
立即加载就是在使用类的时候已经将对象创建完毕，常见的实例办法就是直接 new 实例化
```java
public class Singleton{
    //在自己内部定义自己的一个实例，只供内部调用
    private static final Singleton instance = new Singleton();
    private Singleton(){
        //do something
    }
    //这里提供了一个供外部访问本class的静态方法，可以直接访问
    public static Singleton getInstance(){
        return instance;
    }
}
```
## 2. 延迟加载 ／ “懒汉模式”
延迟加载就是在调用 get() 方法时实例才被创建，常见的办法就是在 get() 方法中进行 new 实例化
```java
public class SingletonClass{
    private static SingletonClass instance=null;
    private SingletonClass(){
    //do something
    }
    public static SingletonClass getInstance(){
        if(instance==null){
               instance=new SingletonClass();
        }
        return instance;
    }
}

```
缺点：创建出 “多例”; 解决办法使用DCL双检查锁机制
```java
public class Singleton{
    private static volatile Singleton instance=null;
    private Singleton(){
        //do something
    }
    public static  Singleton getInstance(){
        if(instance==null){ // lock1
            synchronized(SingletonClass.class){
                if(instance==null){ // lock2
                    instance=new Singleton();
                }
            }
        }
        return instance;
     }
}

```

## 3. 使用静态内置类实现单例模式
```java
public class SingletonClass{

	private static class SingletonHandler {
    	private static SingletonClass instance = new SingletonClass();
    }

    private SingletonClass(){
    //do something
    }

    public static SingletonClass getInstance(){
        return SingletonHandler.instance;
    }
}

```

## 4. 序列化和反序列化的单例模式实现
静态内置类可以达到线程安全，但如果遇到序列化对象，使用默认的方式运行得到的结果还是多例的
```java
public class SingletonClass implements Serializable {
	private static final long serialVersionUID = 888L;

    private static class SingletonHandler {
    	private static final SingletonClass instance=new SingletonClass();
    }

    private SingletonClass(){
    //do something
    }

    public static SingletonClass getInstance(){
        return SingletonHandler.instance;
    }

    protected SingletonClass readResolve() throws ObjectStreamException {
    	return SingletonHandler.instance;
    }
}
```
保证单列的方法就是在反序列化中使用 readResolve() 方法


## 5. 使用 static 代码块实现单列模式
静态代码块中的代码在使用类的时候就已经执行了
```java
public class SingletonClass{
    private static SingletonClass instance=null;
    private SingletonClass(){
    //do something
    }
    static {
    	instance=new SingletonClass();
    }
    public static SingletonClass getInstance(){
        return instance;
    }
}

```
## 6. 使用枚举数据类型 enum 实现单例模式
在使用枚举类时，构造方法会被自动调用
```java
public class SingletonClass {
	public enum EnumSingleton {
    connectionFactory;
    private Connection connection;
    private EnumSingleton() {
    	try {
        	String url = "jdbc.sqlserver://localhost:1079;databaseName = y2";
            String username = "sa";
            String password = "";
            String driveName = "com.microsoft.sqlserver.jdbc.SQLServerDriver";
            Class.forName(driveName);
            connection = DriverManager.getConnection(url, username, password);
        } catch (ClassNotFoundException e){
        	e.printStackTrace();
        } catch (SQLException e) {
        	e.printStackTrace();
        }
    }
    public Connection getConnection() {
    	return connection;
    }
}
	public static Connection getConnection () {
	return EnumSingleton.connectionFactory.getConnection()
	}
}
```

---

# 其他

---


## 1. 线程的状态
线程对象在不同的运行时期有不同的状态，状态信息就存在于 State 枚举类中

- NEW 状态是线程实例化后还从未执行 start() 方法时的状态；
- RUNNABLE 状态是线程进入运行的状态；
- TERMINATED 状态是线程被销毁时的状态
- TIMED_WAITING 状态是线程执行了 Thread.sleep() 方法，呈等待状态，等待时间到达，继续向下执行
- BLOCKED 状态出现在某一个线程在等待锁的时候
- WAITING 状态是线程执行了 Object.wait() 方法后所处的状态

## 2. 线程组
- 线程组中可以有线程对象，也可以有线程组

- 创建一个线程组，然后将部分线程归属到该线程组中
```java
ThreadA aRunnable = new ThreadA();
ThreadB bRunnable = new ThreadB();
ThreadGroup group = new ThreadGroup("PPP的线程组");
Thread aThread = new Thread(group, aRunnable);
Thread bThread = new Thread(group, bRunnable);
```
- **线程组的自动归属：自动归到当前线程组中**
  在实例化一个 ThreadGroup 线程组 x 时如果不指定所属的线程组，则 x 线程组自动归到当前线程对象所属的线程组中，也就是隐式地在一个线程组中添加了一个子线程组
  
- JVM 的根线程组就是 system，再取其父线程组 getParent() 则出现空异常

- **组内的线程批量停止** 调用线程组 ThreadGroup 的 interrupt() 方法 

- **递归与非递归取得组内对象**
```java
Thread.currentThread().getThreadGroup.enumerate(listGroup1, true);// 传入 true 是递归取得子组及子孙组，默认为递归
```

## 3. SimpleDateFormat 非线程安全
类 SimpleDateFormat 主要负责日期的转换与格式化，但并不是线程安全的; 使用单例的 SimpleDateFormat 类在多线程的环境中处理日期，极易出现日期转换错误的情况;  解决办法: (1) 创建多个类 SimpleDateFormat 的实例; (2) 使用 ThreadLocal 类

## 4. 线程中出现异常的处理
- 使用 **UncaughtExceptionHandler** 类对多线程中的异常进行 ‘捕捉’

- 方法 **setUncaughtExceptionHandler（）**是给指定线程对象设置的异常处理器

- 方法 **setDefaultUncaughtExceptionHandler（）**是给指定线程类的所有线程对象设置默认的异常处理器

## 5. 线程组内异常处理
- 在默认的情况下，线程组中的一个线程出现异常不会影响其他线程的执行

- **注意**  使用自定义 java.lang.ThreadGroup 线程组，并且重写 uncaughtException 方法处理线程组内中断行为时，每个线程对象中的 run（）方法内部不要有异常 catch 语句，否则 public void uncaughtException(Thread t,Throwable e)方法不执行




  
