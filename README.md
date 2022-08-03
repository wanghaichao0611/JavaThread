# 多线程的基础知识和用法区别
1. 进程：进程是程序的一次执行的过程，是系统运行的基本单位。
1. 线程：与进程相似，但是线程比进程更小的执行单位。
1. java语言中实现多线程的方法有两种，一种是继承Java.lang包中的Thread类，另一个是用户自定定义的类中实现Runnable(callable有返回值)接口
1. 若直接适用Thread类，在类中this即指的是当前线程；若适用Runnable，要用Thread.currentThread()方法。
1. 对于一个线程，基于对象调用wait()或notify()/notifyall()[防止"假死"]方法，该线程必须已经获得对象x的互斥锁，换句话说就是在同步代码块中使用指定进行同步处理。
1. join()方法限制当前线程继续往下执行直到结束后再往下执行。
1. sleep()和wait()的区别:都能使线程的阻塞，但是wait()放弃CPU的资源的同时交出了资源的控制权，而sleep()并不放弃CPU的资源占有，还有在同步代码块之间的区别。
1. run()方法不启动新的线程。strat()启动新的线程。public static void main(String[] args)的解析: static类加载时初始化，main(名字不一定是main)也是一个线程，所以执行新的线程的时候要使用start()方法.
1. Java有三种方法可以使正在运行的线程终止运行：①使用退出标志使线程正常退出②stop(),suspend(),resume(),过期的方法。③interrupt()终止线程.
1. yield()方法的作用是放弃当前CPU的资源，让其它任务去占用CPU时间，放弃的时间不确定，有可能刚放弃，马上又获得CPU时间片.
# 多线程高级基础总结
1. Threadlocal类:变量值的共享可以使用public static变量的形式实现，而ThreadLocal为变量在每个线程中创建一个副本，每个线程都可以访问自己的局部的变量，Thread-ThreadLocal-Map起桥梁的作用，key对象不能重复，value保存值，并且不能实现值继承，而Inheritable ThreadLocal可以使子线程继承父线程的值.
1. Lock是一个接口，而ReentrantLock是它的实现类。,lock是synchronized的性能升级，lock()和unlock()成对出现,所以lock可以在两个方法中"加锁"或"解锁"，而synchronized只允许修饰一个方法
1. tryLock可以判断是否可以获得锁,也可以设置一段时间超过后重新尝试获得锁。
1. Lock有公平锁也有非公平锁 lock lock=new ReentrantLock(true/false)//公平锁和非公平锁.
1. Lock可以实现排队公平有序: try{lock();} finally{unlock();}
1. ReentrantReadWriteLock(读写锁取代synchronizedList<>),读写互斥，写读互斥，写写互斥，读读异步。
1. Condition是依赖Lock对象的，Condition定义了等待/通知两种类型的方法，Condition condition=lock.newContiditon.await/singnal.
   Condition包含等待队列，等待和通知
1. ConcurrentHashMap的实现原理和使用:
* HashMap在多线程中put操作会导致死循环，CPU接近100%，因为多线程会导致HashMap的Entry链表结构(JDK1.7之前)形成环形数据结构，意味着Entry的next节点永远不为空，所以会导致死循环。
* Hashtable读取都上了synchronized来保证线程安全，竞争激烈效率低。
* ConcurrentHashMap采用锁分段技术。首先将数据分成一段一段地存储，然后把每一段数据配一把一锁，当一个线程占用锁访问其中一个数据段地时候，其他段地数据也能被其他线程访问。
9. Java中的阻塞队列：JDK提供了7个阻塞队列但基本都应用于生产者/消费者模式中，但是7种底层的结构均不同。
10. Fork/Join框架：并行执行任务的框架，是把一个大任务分割成若干个小任务，最终汇总每个小任务结果后得到大任务的框架(类似二路归并算法)。
11. Java种的13个原子操作类：Java.util.concurrent.atomic包下的原子类，会用就行不用死记。
12. Java中的并发工具类:
* CountDownLatch:允许一个或者多个线程等待其他线程完成操作，类似一个"闭锁"卡住。相似倒计时器，当计数器的值为0的时候就会恢复等待的线程
* 同步屏障CyclicBarrier:"栅栏"，直到最后一个线程结束前都处于阻塞中。
* Semaphore:信号量就是指定数量的许可，当一个acquire()被调用，一个许可被调用线程取走，每调用一次release()，一个许可返还给信号量，其他线程继续使用，类似于锁Lock。控制公有资源的的场景，例如数据库连接。
* Exchanger(交换者)是一个用于线程间协作的工具类。一个线程先执行exchange()方法，它会一直等待第二个线程也执行exchange方法，当两个线程都到达同步点的时，这两个线程就可以交换数据.
13. Java中的线程池是几乎所有需要异步或并发执行任务的程序都可以使用线程池。由核心线程池和工作存储队列组成。future=submit(callable)这是带有返回值的用法。
14. CopyOnWriteArrayList:在多线程中写加锁，但是读不加锁。ArrayList-Vector-CopyOnWriteArrayList的进程.
15. AbstractQueuedSynchronized抽象同步队列：AQS也是基于volatile int state 来管理一个FIFO线程队列，上面的许多并发基于AQS的基础.
16. Future接口和FutureTask实现类，主要用来异步获得值，类似"闭锁"异步获得值，同样也实现了runnable接口。

# Java并发机制和底层实现原理

1.  Java中有两种线程:一种是用户线程，也称为非守护线程；另一种是守护线程。守护线程是一种特殊的线程，当进程种不再存在非守护线程了，则守护线程自动销毁。典型的守护线程就是垃圾回收线程。守护Daemon线程的作用是其他线程的运行提供便利服务。(垃圾回收器)
2.  volatile关键字:
* 可见性：B线程可以马上看到A线程更改的数据
* 原子性: 在32位系统中，针对为使用volatile声明的long或double数据类型没有实现原子性，入股哦系上volatile可以实现原子性，x86架构64位JDK写double或long是原子性的，volatile修饰int i的变量 i++复合操作时非原子的。
* 禁止代码重排序：代码之间有依赖关系就不会有重排序，没有依赖就会发生。
3. synchronized关键字的适用:同步语句或者同步方法。
4. synchronized关键字在字节码指令中的原理：①在方法中使用synchronized关键字同步是使用了falg标志ACC-SUNCHRONIZED，当调用方法时，调用指定会检查方法的ACC-SYNCHRONIZED访问标志时候设置了，如果设置了，执行线程现持有同步锁，然后再执行方法，最后再方法完成时释放锁。flags:ACC-PUBLIC,ACC-SYNCHRONIZED.②如果使用synchronized代码块时，则使用monitorenter和monitexit。
5. JVM中的CAS(Compare And Swap比较和交换)操作正是利用了处理器提供的CMPXCHG指定实现的。自旋CAS实现的基本思路就是循环进行CAS操作直到成功为止。CAS实现原子操作的三大问题:
* ABA的问题，A->B->A,中间发生了变化但是结果没变，需要增加版本号Version,1A->2B->3A就解决了。AtomicStampedReference也可以解决ABA，使用的这个类的compareAndSet判断当前标志是否等于预期标志。
* 循环时间长开销大，主要的问题还是对于CPU来说。
* 只能保证一个共享变量的原子操作
6. Java内存模式(JMM)
* JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存(Main Memory)中,每个线程都有一个私有的本地内存(Local Memory),本地内存中存储了该线程以读/写共享变量的副本。本地内存是JMM的一筹抽象概念，并不真实存在。它覆盖了缓存。写缓冲区，寄存器以及其他的硬件和编译器优化
* JMM通过控制主内存与每个线程的本地内存之间的交互，来为Java提供内存可见性保证
7. 从源代码到指令序列的重排序以及解决方案
* 编译器优化的重排序。编译器在不改变单线程程序语义(as-if-serial)的前提下，可以重新安排语句的执行顺序。
* 指令级的重排序。主要的原因是现代处理器，如果不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序。
* 内存系统的重排序。主要原因是处理器使用缓存和读/取缓冲区，加载的时候看上去可能是乱序执行。
* 内存屏障(Memory Barriers),通过内存屏障指令来禁止特定类型的处理器的重排序，也就是上面2，3条的处理器重排序。
* JMM属于语言级的内存模型，它确保在不同的编译器和不同的处理器平台之上，通过禁止特定类型的编译器重排序和处理器重排序，为Java提供一致的内存可见性保证。