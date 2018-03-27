# Java Nio

JDK 1.4引入，支持异步读写，数据从Channel读入Buffer，从Buffer写入Channel。
面向块IO。

## Channel

* FileChannel, 从文件中读写数据，不能切换到非阻塞模式，无法跟Selector一起使用，而套接字通道可以。
* DatagramChannel, 通过UDP读写网络数据
* SocketChannel, 通过TCP读写网络数据
* ServerSocketChannel, 监听新进来的TCP连接，每个新进来的连接都会创建一个SocketChannel.

如果两个通道有一个是FileChannel, 那么直接直接Channel之间传输数据。
* toChannel.transferFrom(position, count, fromChannel)
* fromChannel.tranferTo(position, count, toChannel)

## Buffer

* ByteBuffer
* MappedByteBuffer
* CharBuffer
* ShortBuffer
* IntBuffer
* LongBuffer
* DoubleBuffer
* FloatBuffer

使用Buffer读写数据一般流程：
- 写入数据到Buffer
- 调用flip，切换到读模式
- 从Buffer中读取数据
- 调用clear()方法清除整个缓存区或者compact()方法清除已读数据

Buff的三个属性：
* capacity, 最大能容纳capacity个byte，long，char等类型，一旦满了需要清空才能继续写数据。
* position,
    * 写模式：当前位置，初始为0，写入数据position会后移，最大为capacity - 1。
    * 读模式：从当前位置开始读数据，当从写模式切换到读模式，position重置为0。
* limit
    * 写模式等于capacity，读模式表示最多能读多少个数据。
    * 读模式下表示最多能读多少个数据

![]()

方法
* ByteBuffer.allocate(1024)
* inChannel.read(buf) 从channel中读数据到buff
* buf.put(127)
* buf.flip() 写模式切换到读模式，limit=position,position=0
* inChannel.write(buf) 从Buffer读取数据到Channel
* byte aByte = buf.get()
* rewind()
* clear()
* compact()
* mark(), reset()

## Scatter & Gather

Scattering Read: 将Channel中的数据读到多个Buffer中，必须是固定大小的格式。
Gathering Write: 可以处理动态消息。只有position和limit之间的数据会被写入。

## Selector

一个线程处理多个通道。Channel必须处于非阻塞模式，无法与FileChannel一起使用。
可以监听四种不同类型的事件：
* Connect, SelectionKey.OP_CONNECT
* Accept, SelectionKey.OP_ACCEPT
* Read, SelectionKey.OP_READ
* Write, SelectionKey.WRITE


## 参考文档

* http://www.sizeofvoid.net/goroutine-under-the-hood/
* http://tutorials.jenkov.com/java-nio/index.html
* https://www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html
