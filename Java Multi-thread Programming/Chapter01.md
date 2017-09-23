#第一章 java多线程技能
- **线程的启动**
- **如何使线程暂停**
- **如何使线程停止**
- **线程的优先级**
- **线程安全相关的问题**

##1.1 进程和多线程的概念及线程的优点 
qq.exe  	好友视频	下载文件 	 传输数据 	发送表情
##1.2 使用多线程

###1.2.1 继承 Thread 类
- 实现多线程编程的方式主要有两种，一种是继承 Thread 类，另一种是实现 Runnable 接口


- **start()** 方法通知‘线程规划器’ 此线程已经准备就绪，等待调用线程的 run() 方法，具有异步执行的效果；如果调用 **thread.run()** 则是同步，此线程对象不交给 ‘线程规划器’来进行处理，而是由mian 主线程来调用，必须等run执行完才可以执行后面的代码。

###1.2.2 实现 Runnable 接口
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

###1.2.3 实例变量与线程安全
- 非线程安全 多个线程对同一个对象的同一实例变量进行操作

##1.3 currentThread()方法
- currentThread()方法可以返回代码段正在被哪个线程调用的信息

##1.4 isAlive()方法
- isAlive()方法就是判断当前的线程是否处于活动状态，活动状态就是线程处于运行或准备运行的状态

##1.5 sleep()方法
- 方法 sleep()的作用是在指定的毫秒数内让‘正在执行的线程’休眠(暂停执行)。这个正在执行的线程就是 this.currentThread()返回的线程。

##1.6 getId()方法
- getId()方法就是获得线程的唯一标志。

##1.7 停止线程
- 抛异常法实现程序的停止

- **interrupt()**方法仅仅是在当前线程中打了一个停止的标记，并不是真正的停止线程
- **interrupted()**                public static boolean interrupted()
测试当前线程是否已经中断，当前线程指运行 this.interrupted()方法的线程 interrupted()方法具有清楚状态的功能.
     public class Run() {
     	public static void main(String[], args) {
        Thread.currentThread().interrupt();
        System.out.println("是否停止1？=" + Thread.interrupted());
        System.out.println("是否停止2？=" + Thread.interrupted());
        }
      }
     是否停止1？=true
     是否停止2？=false

- **isInterrupted()**   public boolean isInterrupted()
测试线程Thread对象是否已经是中断状态，但不清除状态标志

- 如果在sleep状态下停止某一线程，会进入catch语句，并且清除停止状态值，使之变成false

##1.9 yield()方法
- yield()方法的作用是放弃当前的cpu资源，将它让给其他任务去占用cpu执行时间，但放弃的时间不确定。

##1.10 线程的优先级
- 设置线程的优先级用setPriority()方法

- **优先级具有规则性**：cpu尽量将执行资源让给优先级比较高的线程，高优先级的线程总是大部分先执行完，但并不代表全部先执行完，即**优先级具有随机性**

- 优先级高的运行得快

##1.11 守护线程
- 任何一个守护线程都是JVM中所有非守护线程的保姆，它为其他线程的运行提供便利服务，最典型的应用就是GC（垃圾回收器）
- thread.setDamon(true)设置为守护线程
  thread.setDamon(false)设置为用户线程，默认为用户线程