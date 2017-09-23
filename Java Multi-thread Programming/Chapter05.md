#第5章 定时器 Timer
- **如何实现指定时间执行任务**
- **如何实现按指定周期执行任务**

- [ ] **Timer 类主要负责计划任务的功能,也就是在指定的时间开始执行某一个任务**

- - -


- [ ] **封装任务的类是 TimerTask 类，它是一个抽象类，执行计划任务的代码要放在 TimerTask 的子类中**

- - -


##** 与计划任务有关的方法**


- **schedule(TimerTask task, Date date)** 在指定的日期执行一次某一任务
   - 创建一个 Timer 就是启动一个新的线程，这个新的线程是非守护线程，一直在运行 Timer timer = new Timer(true); // 守护线程
   
   - 如果执行任务的时间早于当前时间，则立即执行任务
   
   - 一个 TimerTimerTask中可以运行多个 TimerTask 任务，kTimerTask 是以队列的方式一个一个被顺序地执行

- **schedule(Timetask task, Date firstTime, long period) ** 在指定的日期之后按指定的间隔周期，无限循环地执行某一任务
   - 任务执行时间被延时，还得一个一个运行，运行周期增长
   
   - **TimerTask 类的 cancel()**方法是将自身从任务队列中移除，其他任务不受影响
   
   - **Timer 类的 cancel()**方法是将任务队列中的全部任务请空。** 注意**： Timer 类的 cancel()方法有时并没有争抢到 queue 锁，则任务正常运行

- **schedule(TimerTask task, long delay)** 作用是在以执行 schedule(TimerTask task, long delay 方法当前时间为参考时间，在此时间基础上延迟指定的毫秒数后执行一次 TimerTask 任务

- **schedule(TimerTask task, long delay，long period)** 作用是在以执行 schedule(TimerTask task, long delay 方法当前时间为参考时间，在此时间基础上延迟指定的毫秒数 delay 后以某一时间间隔 period 无限次数地执行 TimerTask 任务

- ** scheduleAtFixedRate(TimerTask task, Date firstTime, long period)** 在指定的日期之后按指定的间隔周期，无限循环地执行某一任务

##** 总结**
- 凡是使用方法中带有 period 参数的，都是无限循环执行 TimerTask 中的人物

- 方法 schedule() 和 scheduleAtFixedRate() 都会按顺序执行，所以不需要考虑非线程安全问题

- 方法 schedule() 和 scheduleAtFixedRate()主要区别在于有没有追赶性
  - 方法 schedule()没有追赶性
  - 方法 scheduleAtFixedRate()具有追赶性，计划执行时间早于当前时间时，将两个时间段内的时间所对应的 Task 任务被补‘充性地’执行