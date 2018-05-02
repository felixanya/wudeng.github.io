# java thread

线程可以把一个进程的资源分配和执行调度分开。
共享进程资源（内存地址，文件I/O等），独立调度。

线程实现方式：
* 使用内核线程，Kernel Thread, 一对一模型，也是SUN JDK使用的模式
* 使用用户线程，User Thread, 一对N模型
* 使用用户线程 + 轻量级进程混合实现

JVM规范没有限定JVM要使用哪种线程模型。对于官方JDK来说，因为Windows和Linux操作系统（主流）
只提供一对一线程模型，所以SUN JDK采用了一对一线程模型。其他JDK可以有不同实现。

调度流程：
* 操作系统包括了一个线程调度器，内核可以操作调度器对内核线程进行调度，将线程任务映射到各个CPU上。
* 应用程序不能直接使用内核线程，只能使用内核线程的一种高级接口——轻量级进程（LWP）。对于变成语言来说，它们操作的是实际上是这个LWP。
* LWP和线程之间的映射是一对一的，所以这种方式叫做一对一模型。

## 线程调度
系统为线程分配处理器使用权的过程，主要的调度有两种方式：
* 协同式线程调度 Cooperative，线程执行完通知另一个线程，容易造成阻塞。
* 抢占式线程调度 Preemptive，主流方式，线程的切换不是由线程自己做主（Java可以通过Thread.yield()来让出执行时间，但是没有获取执行时间的方式）。

虽然抢占式调度室系统自动完成的，但是我们可以给出建议：即通过设置进程的优先级 - priority。但是不要依赖优先级，因为它并不靠谱。
最终结果仍取决于OS。比如Java有10中优先级，但是Windows只提供7种，无法一一对应。


## 线程状态

Java语言规范定义了如下6种线程状态：
* New 创建后尚未启动的线程
* Runable 正在JVM中执行的线程，对应两种OS线程状态：Running和Ready
* Blocked 等待进入同步区域
* Waiting 等待被其他线程唤醒
    - java.lang.Object#wait()
    - ...
* Timed Wating 在一定时间内会被系统自动唤醒
    - java.lang.Object#wait(long)
    - java.lang.Thread#sleep(long)
    - ...
* Terminated 已经终止的线程

## 创建线程

使用Java代码可以用两种方式（但最终都是创建一个Thread类实例）来创建线程：
* 继承Thread类
* 实现Runable接口

## 同步

synchronization同步，通过monitor监视器实现。
所有对象都关联着一个监视器。线程可以锁定或者解锁此监视器。
同一时间只能有一个线程可以锁定监视器，如果有其他线程想要尝试锁定的话它就会阻塞，直到它获得锁定权。

实现同步的方式：
* synchronized statement 语句被执行时，会尝试去锁定其指定对象上的监视器。直到成功为止。最后自动解锁。

```java
synchronized(Expression) {
    // Expression 显示指定一个对象，最好的方式是实例语句中为this
    // 类语句中为 类.class
}
```

* synchronized method 方法不同于语句的地方在于其充当监视器的对象不能指定，而是只能按照默认规则来：
实例语句中为this，类语句中为类.class

* volatile 变量，任何被volatile修饰的变量都不会拷贝副本到工作内存，任何修改直接操作主内存。
因此对于volatile变量的所有修改，所有线程可以马上看到。但是volatile不能保证线程们对变量的修改是有序的。
volatile使用场景：
- 对变量的写操作不依赖当前值
- 变量没有包含在具有其他变量的不变式中

* java.util.concurrent包中的类

## 线程安全

定义：
> A class is thread-safe if it behaves correctly when accessed from multiple threads,
regardless of the scheduling or interleaving of the execution of those threads by the runtime environment,
and with no additional synchronization or other coordinationg on the part of the calling code.

讨论线程安全有个前提：多个线程访问共享资源，如果没有共享资源，那么没有必要讨论。
Java中操作的共享数据分为5类：
* 不可变对象，一定是线程安全的。
    - 基本类型：使用final关键字修饰
    - 对象类型：String
* 绝对线程安全，开销太大，没有绝对线程安全数据
* 相对线程安全，就是我们通常说的线程安全，相对是指“对于一些特定情况的调用，可能还需要调用端使用额外手段来保证”
如Vector、Hashtable等。在特定情况下，还是会出现不安全的现象。
* 线程兼容，本身线程不安全，但是可以在调用端使用同步手段来保证线程安全。如ArrayList, HashMap等。
* 线程对立，调用端也不能保证线程安全，应避免出现这种代码。

线程安全的实现方法：
* 互斥同步。同步：多线程并发访问共享数据时，保证共享数据同一时刻只能被一个线程使用。
互斥：实现同步的一种手段。互斥是因，同步是果。互斥是方法，同步是目的。
    - synchronized 关键字，会在class中同步块的前后分别生成monitorenter和monitorexit两个字节码指令。
    这两个指令都需要一个refrence类型的参数来指明要锁定和解锁的对象。
    - java.util.concurrent.locks.ReentrantLock (重入锁)

* 非阻塞同步

* 无同步方案
    - 可重入代码
    - 线程本地安全

## 锁优化
* 自旋锁：让线程稍等一会再放弃CPU，看持有锁的线程是否很快就会释放锁
* 自适应锁
* 锁消除 即时编译
* 锁粗化
* 轻量级锁 对象自身的运行时数据Mark Word


## 参考文档
* https://blog.csdn.net/u010297957/article/details/51162058
