## 线程状态调度方法

wait\(\)：使一个线程处于等待（阻塞）状态，并且释放所持有的对象的锁；

sleep\(\)：使一个正在运行的线程处于睡眠状态，是一个静态方法，调用此方法要处理InterruptedException异常；

notify\(\)：唤醒一个处于等待状态的线程，当然在调用此方法的时候，并不能确切的唤醒某一个等待状态的线程，而是由JVM确定唤醒哪个线程，而且与优先级无关；

notityAll\(\)：唤醒所有处于等待状态的线程，该方法并不是将对象的锁给所有线程，而是让它们竞争，只有获得锁的线程才能进入就绪状态；

## Synchronized 与Lock {#8synchronized-与lock}

ReentrantLock 拥有Synchronized相同的并发性和内存语义，此外还多了 锁投票，定时锁等候和中断锁等候

线程A和B都要获取对象O的锁定，假设A获取了对象O锁，B将等待A释放对O的锁定，

如果使用 synchronized ，如果A不释放，B将一直等下去，不能被中断

如果 使用ReentrantLock，如果A不释放，可以使B在等待了足够长的时间以后，中断等待，而干别的事情



ReentrantLock获取锁定与三种方式：

a\) lock\(\), 如果获取了锁立即返回，如果别的线程持有锁，当前线程则一直处于休眠状态，直到获取锁

b\) tryLock\(\), 如果获取了锁立即返回true，如果别的线程正持有锁，立即返回false；

c\)tryLock\(long timeout,TimeUnit unit\)， 如果获取了锁定立即返回true，如果别的线程正持有锁，会等待参数给定的时间，在等待的过程中，如果获取了锁定，就返回true，如果等待超时，返回false；

d\) lockInterruptibly:如果获取了锁定立即返回，如果没有获取锁定，当前线程处于休眠状态，直到或者锁定，或者当前线程被别的线程中断



## Volatile和Synchronized {#9volatile和synchronized}

Volatile和Synchronized四个不同点：

1. 粒度不同，前者针对变量 ，后者锁对象和类

2. syn阻塞，volatile线程不阻塞

3. syn保证三大特性，volatile不保证原子性

4. syn编译器优化，volatile不优化要使 volatile 变量提供理想的线程安全，必须同时满足下面两个条件：

    1. 对变量的写操作不依赖于当前值。
    2. 该变量没有包含在具有其他变量的不变式中。



