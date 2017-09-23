# 第2章 对象及变量的并发访问
- **synchronized 对象监视器为 Object 时的使用**
- **synchronized 对象监视器为 Class 时的使用**
- **非线程安全是如何出现的**
- **关键字 volatile 的主要作用**
- **关键字 volatile 与 synchronized 的区别及使用情况**

## 2.1 synchronized 同步方法
###1. 方法内的变量为线程安全
- 方法内部的私有变量不存在 ‘非线程安全’ 问题

### 2 实例变量非线程安全
- 如果多个线程共同访问1个对象中的实例变量，则有可能出现 ‘非线程安全’ 问题
- 解决办法：在方法前加 synchronized。 两个线程访问同一个对象的同步方法时一定是线程安全的

### 3 多个对象多个锁
- 关键字 synchronized 取得的锁是对象锁，哪个线程先执行带 synchronized 关键字的方法，哪个线程就持有该方法所属对象的锁 Lock，其他访问该对象的线程只能等待。
- 如果多个线程访问多个对象，则 JVM 会创建多个锁

### 4 synchronized 方法与锁对象
- 调用关键字 synchronized 声明的方法一定是排队运行的
- 只有共享资源的读写访问才需要同步化
- A 线程先持有 object 对象的 Lock 锁，B 线程可以以异步的方式调用 object 对象中的非 synchronized 类型的方法
- A 线程先持有 object 对象的 Lock 锁，B 线程如果在这时调用 object 对象中的 synchronized 类型的方法则须等待，即同步

### 5 脏读
- 发生脏读的情况是在读取变量时，此变量已经被其他线程更改过了
- 脏读是通过 synchronized 关键字解决的

### 6 锁重入
- 在一个 synchronized 方法／块的内部调用本类的其他 synchronized 方法／块时，是永远可以得到的
- 子类是完全可以通过 ‘可重入锁’ 调用父类的同步方法的

### 7 出现异常，锁自动释放

### 8 同步不具有继承性
- 同步不能继承，所以还需在子类的方法中添加 synchronized 关键字

## 2.2 synchronized 同步语句块
- **synchronized 方法**是对当前对象进行加锁，而 **synchronized 代码块**是对某一个对象进行加锁

- 和 synchronized 方法一样，synchronized（this) 代码块也是锁定当前对象的

- **synchronized 同步方法 或 synchronized（this)同步代码块的作用**

     * 对其他 synchronized 同步方法或synchronized（this)同步代码块调用呈阻塞状态，同一时间只有一个线程可以执行 synchronized 同步方法或 synchronized（this) 同步代码块中的代码
     * 其他线程仍然可以访问该 object 对象中非 synchronized 方法或非 synchronized（this)同步代码块

- 将任意对象作为对象监视器 **synchronized（非 this 对象 x)同步代码块**
     * 使用 ‘synchronized（非 this 对象 x)同步代码块’格式进行同步操作时，对象监视器必须是同一个对象
     * 当其他线程执行x对象中 synchronized 同步方法时呈同步效果
     * 当其他线程执行x对象中 synchronized（this) 代码块时也呈同步效果

- 同步的原因是使用了同一个**‘对象监视器’**
- **静态同步 synchronized 方法与 synchronized（class)代码块**
     * synchronized public static void fun() {} 是对当前的 *.java 文件对应的 Class 类进行持锁
     * 对象锁和 Class 锁是不同的锁
     * 即使是不同的对象，静态的同步方法还是同步执行
     * 同步 synchronized（class) 代码块的作用和 synchronized static 方法的作用一样

- **数据类型 String 的常量池特性**
     * 大多数情况下，同步 synchronized 代码块都不使用 String 作为锁对象，可以用 new Object（）实例化一个Object对象，但它并不放入缓存中

- **内置类与静态内置类**
     * 内置类
        import test.PublicClass.PrivateClass;
        PrivateClass privateClass = PublicClass.new privateClass();
     * 静态内置类
        import test.PublicClass.PrivateClass;
        PrivateClass privateClass = new privateClass();
     * 内置类与同步 本质还是看持有的锁是不是相同的

- **锁对象的改变** 只要对象不变，即使对象的属性被改变，运行的结果还是同步

##2.3 volatile 关键字
- 关键字 **volatile 的作用**是强制从公共堆栈中取得变量的值，而不是从线程私有数据栈中取得变量的值。多线程读取共享变量时可以获取最新值

- **比较 synchronized 和 volatile**
  ** **
  * 关键字 volatile 是线程同步的轻量级实现，volatile 性能更好，volatile 只能用来修饰变量，synchronized 用来修饰方法和代码块
  * 多线程访问 volatile 不会发生阻塞
  * volatile 保证数据的可见性，但不保证原子性； synchronized 既可以保证原子性，也可以保证可见性，因为它会将私有内存和公有内存的数据做同步
  * volatile 解决的是变量在多个线程之间的可见性； 而synchronized 解决的是多个线程之间访问资源的同步性
  ** **
  
* 对于 volatile 修饰的变量， JVM只是保证从主存加载到线程工作内存的值是最新的

* 使用原子类 java.util.concurrent.atomic.AtomicInteger 也并不完全安全，因为方法是原子的，但方法的调用却不是原子的。仍然需要同步机制
