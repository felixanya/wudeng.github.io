# linux process


僵尸进程:


子进程退出，父进程没有调用wait/waitpid获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中。这种进程称之为僵尸进程。


linux提供了一种机制，让父进程随时能够获取子进程的状态，就算子进程退出，系统也只会释放它占用的一些资源，如内存，打开的文件等等。
但是仍为这些进程保留了进程pid，运行时间等等信息。这些残留的信息直到父进程调用wait/waitpid的时候才会释放。这样如果父进程创建了子进程，
但是没有调用wait/waitpid，那么就会造成僵尸进程

子进程调用exit以后，父进程调用wait之前，子进程处于僵尸状态。如果父进程在子进程之前退出，会由init接管，变成孤儿进程。


有什么危害？
占用了很小的内存和文件描述符。

如何删除？
僵尸进程的问题在于它的父进程，一般来说，父进程还活着，但是没有回收僵尸进程。这样如果把父进程干掉，
僵尸进程就会被init接管，最终回收。


解决办法：
* 子进程退出时给父进程发SIGCHILD消息，这个行为是系统默认的，父进程处理消息，然后在处理消息中wait子进程。
* fork两次，僵尸进程的主要问题时父进程一直在但是不干事，如果父进程不干事让他退出就是了。fork两次就是让子进程fork孙子进程，然后子进程退出，孙子进程最后被init接管，也不会有问题。