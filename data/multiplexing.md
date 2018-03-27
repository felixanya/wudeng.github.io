

一个读操作，分为两个阶段：
- 等待数据准备好
- 将数据从内核拷贝到进程


blocking I/O
nonblocking I/O
I/O multiplexing (select and poll)
signal driven I/O (SIGIO)
asynchronous I/O (the POSIX aio_ functions)


POSIX define：
    A synchronous I/O operation causes the requesting process to be blocked until that I/O operation completes.
    An asynchronous I/O operation does not cause the requesting process to be blocked.

Using these definitions, the first four I/O models—blocking, nonblocking, I/O multiplexing, and
signal-driven I/O—are all synchronous because the actual I/O operation (recvfrom) blocks the
process. Only the asynchronous I/O model matches the asynchronous I/O definition.

select
    FD_SETSIZE 32位1024,64位2048
    writefds, readfds, exceptfds
    线性遍历，需要拷贝到用户空间
poll
    线性遍历，需要拷贝到用户空间
    使用链表存储，没有最大连接数限制
epoll
    2.6
    fd回调，通过内核和用户空间共享内存实现
    LT水平触发level trigger，默认，不做任何操作，内核还是会继续通知。
        同时支持block和non-block socket
    ET边缘触发edge trigger，只支持non-block socket， only-once
        高速工作方式，效率更高        
    `int epoll_create(int size)`
        创建epoll文件描述符
    `int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)`
        op 添加、修改、删除监控文件描述符
            EPOLL_CTL_ADD 红黑树，event:文件描述符，回调函数
            EPOLL_CTL_MOD
            EPOLL_CTL_DEL
    `int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout)`
        timeout = 0 立刻返回
        timeout = -1 一直等下去，直到有事件发生
        timeout > 0 等待这么长时间，如果没有时间则返回
事件触发回调。




* Unix Network Programming Chapter 6
* http://blog.csdn.net/tennysonsky/article/details/45745887
