#第7章 拾遗补增
- ** 线程组的使用**
- ** 如何切换线程状态**
- ** SimoleDateFormat 类与多线程的解决办法**
- **如何处理线程的异常**

##7.1 线程的状态
** 线程对象在不同的运行时期有不同的状态，状态信息就存在于 State 枚举类中**

- NEW 状态是线程实例化后还从未执行 start() 方法时的状态；
- RUNNABLE 状态是线程进入运行的状态；
- TERMINATED 状态是线程被销毁时的状态
- TIMED_WAITING 状态是线程执行了 Thread.sleep() 方法，呈等待状态，等待时间到达，继续向下执行
- BLOCKED 状态出现在某一个线程在等待锁的时候
- WAITING 状态是线程执行了 Object.wait() 方法后所处的状态

##7.2 线程组
- 线程组中可以有线程对象，也可以有线程组

- 创建一个线程组，然后将部分线程归属到该线程组中
- ```java
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
- ```java
Thread.currentThread().getThreadGroup.enumerate(listGroup1, true);// 传入 true 是递归取得子组及子孙组，默认为递归
```

##7.3 使线程具有有序性

##7.4 SimpleDateFormat 非线程安全
**类 SimpleDateFormat 主要负责日期的转换与格式化，但并不是线程安全的 **
- 使用单例的 SimpleDateFormat 类在多线程的环境中处理日期，极易出现日期转换错误的情况

- ** 解决办法**
   - 创建多个类 SimpleDateFormat 的实例
   - 使用 ThreadLocal 类

##7.5 线程中出现异常的处理
- 使用 **UncaughtExceptionHandler** 类对多线程中的异常进行 ‘捕捉’

- 方法 **setUncaughtExceptionHandler（）**是给指定线程对象设置的异常处理器

- 方法 **setDefaultUncaughtExceptionHandler（）**是给指定线程类的所有线程对象设置默认的异常处理器

##7.6 线程组内异常处理
- 在默认的情况下，线程组中的一个线程出现异常不会影响其他线程的执行

- **注意**  使用自定义 java.lang.ThreadGroup 线程组，并且重写 uncaughtException 方法处理线程组内中断行为时，每个线程对象中的 run（）方法内部不要有异常 catch 语句，否则 public void uncaughtException(Thread t,Throwable e)方法不执行

##7.7 线程异常处理的传递
** 多种异常处理的方法结合在一起时会怎样？**