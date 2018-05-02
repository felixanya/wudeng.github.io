# Java


## String

String.ValueOf(N)

s.length()

## LinkedList

LinkedList<Integer> s = new LinkedList<>();
s.add(i)
s.isEmpty()
s.getLast()
s.pollLast()

## Arrays
sort()

## PriorityQueue
PriorityQueue<Integer>
offer(val)
poll()


## Math
max()


## Java Questions

### final, finally, finalize



## 垃圾回收

* 程序计数器
* Java虚拟机栈
* 本地方法栈
* Java堆，线程共享，存放对象实例、数组（不绝对，如栈上分配，标量替换优化技术）
    - 可以物理上不连续
    - -Xmx -Xms
* 方法区，各个线程共享（Non-Heap）Permanant Generation (for HotSpot)
    - 运行时常量池
* 直接内存
    - NIO，Channel/Buffer, DirectByteBuffer

对象访问：
* 句柄
* 直接指针，速度更快，HotSpot


对象存活：
* 引用技术，难以解决对象的相互循环引用的问题
* 根搜索算法， GC Roots Tracing
    - 虚拟机栈中引用的对象
    - 方法区中的类静态属性引用的对象
    - 方法区中的常量引用的对象
    - 本地方法栈中JNI的引用的对象

引用
* 强引用，只要强引用还在，永远不会回收
* 软引用，系统将要发生内存溢出异常之前，将会把这些对象列进范围之中进行第二次回收
* 弱引用，只能生存到下一次垃圾回收之前
* 虚引用，唯一目的就是希望在这个对象呗收集器回收时收到一个系统通知
    - finalize函数本来是设计用来在对象被回收的时候做一些操作的，但是对象被GC的时间是不确定的，这样finalize很尴尬
    - 创建虚引用的时候必须传入一个引用队列，在一个对象的finalize函数被调用的时候，这个对象的虚引用会被加入到引用队列中
    通过检查队列的内容就知道对象是不是要准备被回收了
    - 细粒度的内存控制


分代收集算法
* 新生代 8:1:1
    - Eden
    - From Survivor
    - To Survivor
* 老生代 Handle Promotion
    - Tenured

收集算法
* 标记-清除，效率不高，不连续
* 复制算法，新生代
* 标记-整理，老年代


对象内存分配：
* 优先堆上分配（Eden），JIT拆散成标量类型间接栈上分配
* 如果启动了本地线程分配缓存，将按线程优先在TLAB上分配
* 少数情况下直接进入老年代
    - 大对象，需要大量连续内存空间的Java对象，如很长的字符串、数组
        - -XX:PretenureSizeThreshold
    - 长期存活的对象
        - -XX:MaxTenuringThreshold

工具：
* jps JVM Process Status
* jstat
* jinfo
* jmap
* jhat
* jstack

* JConsole
* VisualVM


字节码ByteCode
Class文件，动态加载
* 魔数
* 版本
* 常量值
    - 类和接口的全限定名
    - 字段的名称和描述
    - 方法的名称和描述



类加载机制
运行期动态加载，动态扩展
* 加载
    - 通过全限定名获取此类的二进制字节流
    - 将字节流代表的静态存储结构转化为方法区的运行时数据结构
    - 在Java堆中生成一个代表这个类的java.lang.Class对象，作为方法区这些数据的访问入口
* 连接：验证、准备、解析
* 初始化，对类进行主动调用前必须初始化，主要四种情况：
    - new/getstatic/putstatic/invokestatic
    - java.lang.reflect
    - 父类
    - 主类 main()
* 使用
* 卸载


类加载器
用于实现类的加载动作。对于任意一个类，都需要由加载它的类加载器和这个类本身一同确立其在Java虚拟机中的唯一性。
类加载器不同，那么两个类必定不相等。（equals, isAssignableFrom, isInstance）

双亲委派模型：懒惰的子加载器
Java类随着它的类加载器一起具备了一种带有优先级的层次关系。只要加载Object类，最终都是委派给启动类加载器。
所以都是同一个类。如果没有双亲委派机制，将会出现多个不同的Object类。


类加载器之间的层次关系。Parents Delegation Model
除了启动类加载器，其他类加载器都应当有自己的父类加载器。使用组合来复用父加载器的代码。

一个类加载器收到类加载的请求，首先不会看自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成。每一个层次的类加载器都是如此。
因此所有的加载请求最终都应该传递到顶层的启动类加载器中。只有当父加载器反馈自己无法完成这个加载请求时（搜不到），子加载器才会尝试自己去加载。


双亲委派模型被破坏的情况：
* 出现之前。JDK1.2发布之前。
* JNDI，线程上下文类加载器
* 追求程序动态性，代码热替换。OSGi，Bundle


虚拟机的角度：
* 启动类加载器，C++实现，虚拟机的一部分
* 其他的类加载器，Java语言实现，独立于虚拟机，继承自java.lang.ClassLoader

开发人员的角度：
* 启动类加载器
* 扩展类加载器
* 应用程序类加载器

创建对象时构造器的调用顺序：
- 初始化静态成员
- 调用父类构造器
- 初始化非静态成员
- 最后调用自身构造器


栈帧

局部变量表
操作数栈
动态连接


内存模型 JMM
八大操作：
* lock
* unlock
* read
* load
* use
* assign
* store
* write


volatile
* 保证变量对所有线程的可见性（synchronized, final也可以实现可见性）
* 禁止指令重排序

happens before



java/c/c++/python
tcp/ip,http
linux


netty:
flask: a python microframework
webapp2: python web framework

mongodb/mysql

分布式、缓存、消息机制
