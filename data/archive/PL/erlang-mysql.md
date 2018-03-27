erlang-mysql



## [erlang-mysql-driver](https://github.com/dizzyd/erlang-mysql-driver)

mysql: 管理进程。
mysql_conn: 连接进程。

内置连接池。

mysql：连接池管理进程，每个连接对应一个连接进程，管理进程会monitor连接进程，如果配置了重连，连接进程挂掉时会自动重新建立新的连接进程。
管理进程启动的时候会创建一个连接。多余的连接可以通过mysql:connect函数来创建。创建完以后将连接交给管理进程管理起来。

缺点：管理进程可能会成为单点，连接池的管理采取rr策略，在处理长时间的请求时可能导致其他请求超时。

执行sql的时候，都需要向管理进程获取连接。通过`call`管理进程来执行。管理进程找到一个可用的连接，然后把请求转发给连接进程，
注意这里管理进程不需要等待连接进程返回，而是直接`send_msg`的方式将请求方`From`交给连接进程，由连接进程处理请求以后，
通过`gen_server:reply`将结果返回请求方。

连接池：使用的是round-robin策略，所有请求均匀的分配到每个连接上面，而没有考虑到请求执行的时间，并且请求完毕并不会执行将used连接放入unused队列中。
而是等到unused队列为空时，将used队列加入unused队列。

具体来说，连接池是PoolId到{Unused, Used}的映射，通过gb_trees管理。当需要执行sql的时候，如果有空闲的连接，则从空闲的list里面取出来一个连接，
并将其加入使用中。如果没有空闲的连接，则从Used队列里面取出队尾的连接。并将剩余的Used队列加入空闲队列。也意味着`unused`队列中的连接其实并不是一定是空闲的，
如果有一个连接在处理一个长时间的请求，当轮到他处理请求的时候，其他请求依然会交给这个连接来处理。


## [emysql](https://github.com/Eonblast/Emysql)

`erlang-mysql-driver`的改进版本。

缺点：
- spawn一个新的进程出来执行请求。
- 不支持事务。
- 最新版本为0.4.1，最近一次更新是2014年。

同样有内置的连接池。

每个连接池有一个PoolId，一个available的连接集合，一个locked的连接（gb_trees）。还有一个等待连接的waiting队列。

locked: id -> emysql_connection

emysql_conn_mgr管理连接池。emysql的连接没有所谓的连接进程。一个连接就是一个包含socket的record。
emysql的连接池也是两部分，available和locked，其中available使用queue来管理，而locked使用gb_trees来管理。

* 请求进程向管理进程要连接的时候有两个选择，
  - 一个是`nonblocking`，使用的接口是`lock_connection`，这种情况如果没有可用连接，不等待。
  - 另一个是blocking的，使用的接口是`wait_for_connection`表示愿意等待，但是如果超时还是没有就会崩溃`exit`。
如果拿到了连接，就直接返回。如果没拿到，就等待Timeout时间，如果Timeout时间内还是没有收到，就告诉管理进程`abort_wait`，我不等了，抛出`connection_lock_timeout`异常。
管理进程收到`abort_wait`就找到这个连接池的等待列表，发现这个进程就remove掉，返回ok“好的。”，没发现就返回not_waiting，“你没有等我呀！”。

* 执行请求的时候，发起请求的进程直接向管理进程要连接，同时告诉管理进程：如果暂时拿不到，我愿不愿意等待。

* 管理进程收到请求进程发起的要连接的请求，先看看是否有available的连接，如果有，取出一个连接，设置这个连接的锁定时间和锁定对象（请求进程的监视ref），
将这个连接加入locked中。

* 连接管理进程会监视拿到连接的进程。当发现监视的请求进程退出，会通过lockers找到这个请求进程对应到的连接池和连接，
新建一个进程出来，先主动关闭跟数据库的连接再重新建立连接。然后替换管理进程中的连接。

* 请求进程拿到连接以后，就会spawn一个进程出来执行请求。等到使用完了以后，又会将连接还回去。请求进程spawn进程出来以后，就阻塞等待这个进程的返回。当然他等到的可能是这个
新进程死亡的消息。异常的情况都会重置连接。

所以如果有大量请求同时竞争连接，就会出现这些进程因为拿不到连接而崩溃。如果没有catch住异常，那么发起请求的进程就崩溃了。


## [mysql-otp](https://github.com/mysql-otp/mysql-otp)


一个连接就是一个进程。需要第三方连接池。poolboy

资源是进程，资源的两种策略：
lifo
fifo

资源worker通过supervisor创建，管理进程用link连接起来。能够检测进程退出。
state
  max_overflow 允许超出初始的资源个数，当负载降下来以后，超出的部分会回收
  workers list,空闲的资源列表
  waiting queue，`{_, _, MRef}`
  minitors ets，被占用的资源信息。`{Pid, _, MRef}`

checkout 请求资源。
checkin 还回来，Pid是资源

https://github.com/mysql-otp/mysql-otp-poolboy
