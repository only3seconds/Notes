# 深入理解 Synchronized 

## 一. synchronized 基本使用

### 1. synchronized 修饰普通方法

**问题：多个线程操作 i++ 会出现什么情况？**

```java
package synchronized_study;

public class SynchronizedTest1 implements Runnable {
    // 线程共享资源 i
    public static int i = 0;

    public void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        for (int i = 0; i < 1000000; i++) {
            increase();
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        SynchronizedTest1 instance = new SynchronizedTest1();
        Thread t1 = new Thread(instance);
        Thread t2 = new Thread(instance);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```
结果：i 的值小于2000000

- i++ 操作不保证原子性，不是线程安全的。

**如何保证 i++ 操作的线程安全性？**

```java
package synchronized_study;

public class SynchronizedTest2 implements Runnable {
    // 线程共享资源 i
    public static int i = 0;

    public synchronized void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        for (int i = 0; i < 1000000; i++) {
            increase();
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        SynchronizedTest1 instance = new SynchronizedTest1();
        // t1 和 t2 争夺同一个对象锁 (即instace对象锁）
        Thread t1 = new Thread(instance);
        Thread t2 = new Thread(instance);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```

结果：i 的值总是等于2000000.

- synchronied 修饰普通方法时，多线程争夺的锁是当前实例对象的锁。
- 多个线程同时竞争同一个对象锁，只有一个线程会获得锁，其他线程将被阻塞，等待对象锁被释放。

**问题：当多个线程竞争不同的对象锁时会发生什么？**

```java
package synchronized_study;

public class SynchronizedTest3 implements Runnable {
    // 线程共享资源 i
    public static int i = 0;

    public synchronized void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        for (int i = 0; i < 1000000; i++) {
            increase();
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        // t1 和 t2 争夺不同的对象锁
        Thread t1 = new Thread(new SynchronizedTest1());
        Thread t2 = new Thread(new SynchronizedTest1());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```

结果：i 的值小于2000000.

- 不同的对象有不同的对象锁。
- 多个线程在同一时刻争夺不同的对象锁不会发生阻塞，不能保证线程安全。

解决办法：synchronized 修饰静态方法。

### 2. synchronized 修饰静态方法

```java
package synchronized_study;

public class SynchronizedTest4 implements Runnable {
    // 线程共享资源 i
    public static int i = 0;

    public static synchronized void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        for (int i = 0; i < 1000000; i++) {
            increase();
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        // t1 和 t2 争夺不同的对象锁
        Thread t1 = new Thread(new SynchronizedTest1());
        Thread t2 = new Thread(new SynchronizedTest1());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```
结果：i 的值总是等于2000000.

- synchronized 修饰静态方法时，多线程之间争夺的是对象的 Class对象锁。
- java中每个类只有一个 Class对象锁，类的不同对象共享这个 Class对象锁。
- 多个线程在同一时刻争夺 Class对象锁时，只有一个线程将会获得 Class对象锁，其他线程将被阻塞，等待 Class对象锁被释放。

### 3. synchronized 修饰代码块

**问题：synchronized 修饰方法已经可以解决线程同步问题，为什么还需要修饰代码块的功能呢?**

	synchronized 修饰代码块与 synchronized 修饰方法相比，可以提高线程并发的粒度。有时需要
	进行同步的地方只是方法体的一小部分，同步整个方法的话，线程等待的时间较长，性能较低。
	
```java
package synchronized_study;

public class SynchronizedTest5 implements Runnable {
    private static SynchronizedTest1 instance = new SynchronizedTest1();

    // 线程共享资源 i
    public static int i = 0;

    public void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        // 省略其他耗时的操作

        // instace 作为多线程争夺的对象锁
        synchronized (instance) {
            for (int i = 0; i < 1000000; i++) {
                increase();
            }
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new SynchronizedTest1());
        Thread t2 = new Thread(new SynchronizedTest1());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```
结果：i 的值总是等于 2000000.

```java
package synchronized_study;

public class SynchronizedTest6 implements Runnable {
    private static SynchronizedTest1 instance = new SynchronizedTest1();

    // 线程共享资源 i
    public static int i = 0;

    public void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        // 省略其他耗时的操作

        // instace 作为多线程争夺的对象锁
        synchronized (this) {
            for (int i = 0; i < 1000000; i++) {
                increase();
            }
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new SynchronizedTest1());
        Thread t2 = new Thread(new SynchronizedTest1());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```
结果：i 的值小于等于 2000000.

```java
package synchronized_study;

public class SynchronizedTest7 implements Runnable {
    private static SynchronizedTest1 instance = new SynchronizedTest1();

    // 线程共享资源 i
    public static int i = 0;

    public void increase() {
        // i++ 操作不保证原子性
        i++;
    }

    @Override
    public void run() {
        // 省略其他耗时的操作

        // instace 作为多线程争夺的对象锁
        synchronized (Synchronized.Class) {
            for (int i = 0; i < 1000000; i++) {
                increase();
            }
        }
    }

    /**
     * @param args
     * @throws InterruptedException join内部是通过 wait() 实现的，wait() 中的线程被 interrupt 会报 InterruptedException 异常
     */
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new SynchronizedTest1());
        Thread t2 = new Thread(new SynchronizedTest1());
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(i);
    }
}
```
结果: i 的值总是等于2000000.

**总结：**

- synchronized(instance) 表示多线程争夺的锁就是指定的 instace对象锁；
- synchronized(this) 表示多线程争夺的锁是 当前对象锁；
- synchronized(SynchronizedTest.class) 表示多线程争夺的锁是 SynchronizedTest类的 Class对象锁。

## 二. Synchronized 特性

### 1. 可重入性

- synchronized 的可重入性是对于同一个线程而言的。
- 当一个线程拥有某个对象锁后，再次请求该对象锁时，是一定可以获得的。这就是 synchronized的可重入性。
- 例如，一个线程调用对象instace的同步方法A,成功获取了 instance对象锁，当该线程在方法A中调用instance对象的同步方法B时，线程是可以直接获得 instance对象锁的。

### 2. 注意点

- 当一个线程执行的代码出现异常时，该线程持有的锁会自动释放。
- synchronized 不能继承的理解：父类有 synchronized方法 test(),子类继承父类，如果子类没有重写 test，那么子类对象调用 test方法是同步的；如果子类重写 test方法，必须加上 synchronized 关键字才能实现同步。

## 三. Synchronized 实现原理

### 1. 操作系统中的 Monitor

操作系统在面对进程/线程间同步问题的时候，信号量(semaphore)和互斥量（mutex)是最重要的同步原语。为了方便程序员并发开发，操作系统在 semphore 和 mutex 的基础上提出了更高级的同步原语 monitor.

### 2. Java 管程(Monitor)机制

Java monitor 的实现方式是在语法上提供语法糖，而具体如何实现monitor机制是由java编译器实现的。


[管程 — Java同步的基本思想](https://blog.csdn.net/qq_33873431/article/details/80348205)

### 3. Java 对象头

### 4. 

- 一般而言，synchronized 使用的锁对象是存储在Java对象头里的；


## 参考资料

[深入理解Java并发之synchronized实现原理](https://blog.csdn.net/javazejian/article/details/72828483)

[monitor的概念](https://www.jianshu.com/p/7f8a873d479c)


