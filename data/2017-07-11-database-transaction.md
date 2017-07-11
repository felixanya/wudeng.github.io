数据库事务
====

事务需要满足ACID特征。Mysql的innodb引擎支持事务。

* 原子性，Atomicity, 一个事务是不可分割的整体，要么全部成功(committed)，要么全部失败(rolled back)，不存在部分成功。
* 一致性，Consistency，数据总是从一致性的状态转移到另一个一致性的状态，总是处于有意义的状态。比如转账，A的钱减少，B的钱增加，总和是不变的。
* 隔离性，Isolation，主要解决多个事务**并发读写**的问题，一个事务未提交，那么它对数据的修改对外是不可见的。
* 持久性，Durability，一个事务一旦提交，对数据的影响的永久性的，就算断电，系统崩溃也是如此。

四个性质最根本的是一致性，其他三个性质都是服务于一致性的。

隔离级别
----

当多个进程的事务同时读写数据时，就会出现一些问题。
* 脏读，read uncommitted，一个事务可以读到其他事务尚未提交的修改。尚未提交意味着可能回滚，那么该事务读到的就是无效的数据。
* 不可重复读，unrepeatable read，同一个事务范围内多次查询却返回了不同的数据，这是因为在查询间隙，数据被另外的事务修改了。
* 虚读，幻读，phantom read，同一个事务范围内，相同的操作两次读取的记录数不一样，比如多出来一行。跟不可重复读的对象不一样，幻读针对的是一批数据，而后者指的是同一个数据。

隔离性是为解决上面的问题，innodb通过不同的锁策略支持四个级别的隔离性。
* Read uncommitted，最低级别，任何情况都无法保证。
* Read committed，可避免脏读。
* Repeatable read，可避免脏读，不可重复读，innodb的默认级别。
* Serializable，最高级别，可避免脏读，不可重复读，幻读的发生，效率最低，一般通过锁表来实现。

| 隔离级别 | 脏读 | 不可重复读 | 幻读 |
|----------|------|------------|------|
| 未提交读 | Yes  | Yes        | Yes  |
| 已提交读 | No   | Yes        | Yes  |
| 可重复读 | No   | No         | Yes  |
| 可串行化 | No   | No         | No   |

mysql中查看隔离级别：
```
mysql> select @@global.tx_isolation, @@tx_isolation;
+-----------------------+----------------+
| @@global.tx_isolation | @@tx_isolation |
+-----------------------+----------------+
| READ-COMMITTED        | READ-COMMITTED |
+-----------------------+----------------+
1 row in set (0.00 sec)
```

innodb可以通过下面的命令设置隔离级别，注意带global, session或者都不带效果是不一样的。
```
set [global | session] transaction isolation level [read uncommitted |read committed |repeatable read | serializable];
```
* With the GLOBAL keyword, the statement applies globally for all subsequent sessions. Existing sessions are unaffected.
* With the SESSION keyword, the statement applies to all subsequent transactions performed within the current session.
* Without any SESSION or GLOBAL keyword, the statement applies to the next (not started) transaction performed within the current session. Subsequent transactions revert to using the SESSION isolation level.

在事务内部无法修改下一个事务的隔离级别：
```
mysql> begin;
Query OK, 0 rows affected (0.00 sec)
mysql> set transaction isolation level serializable;
ERROR 1568 (25001): Transaction isolation level can't be changed while a transaction is in progress
mysql> commit;
Query OK, 0 rows affected (0.00 sec)
mysql> set transaction isolation level serializable;
Query OK, 0 rows affected (0.00 sec)
```
或者
```
set tx_isolation = 'read-uncommitted';
set tx_isolation = 'read-committed';
set tx_isolation = 'repeatable-read';
set tx_isolation = 'serializable';
```

参考文档
----
* [数据库事务的四大特性以及事务的隔离级别](http://www.cnblogs.com/fjdingsd/p/5273008.html)
* [SET TRANSACTION Syntax](https://dev.mysql.com/doc/refman/5.7/en/set-transaction.html)
* [Transaction Isolation Levels](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html)

