
剑指Offer

STAR原则
    Situation
    Task
    Action
    Result

* 基本功
    - 编程语言
    - 数据结构
    - 算法
* 能写高质量代码
* 分析问题思路清晰
* 能优化时间效率和空间效率
* 学习沟通
    最近看什么书、从中学到什么技术

精通，熟悉，掌握，了解

## Erlang

* 网关进程创建过程。
* supervisor如何知道子进程异常退出？
    - link trap_exit

## Algorithm

* 判断四个点是否构成一个正方形
    - 求出6条边长，排序，len[0] == len[3], len[4] == len[5], len[4] > len[3]

* 带O(1) min功能的栈
* 两个栈模拟队列，参考queue.erl
* 斐波那契数
    青蛙跳台阶，1或者2，跳n有多少种跳法。
* 2-Sum, 3-Sum, N-Sum
* map N function calls to M nodes
```
map_nodes([],_,_) -> [];
map_nodes(ArgL,[],Original) ->
    map_nodes(ArgL,Original,Original);
map_nodes([{M,F,A}|Tail],[Node|MoreNodes], Original) ->
    [?MODULE:async_call(Node,M,F,A) |
     map_nodes(Tail,MoreNodes,Original)].
```

## 网络

* 浏览器的一个请求从发送到返回都经历了什么？


## Design

* 设计手机扫码登陆

* 设计Rate Limiter
    - Token Bucket Algorithm， leaky bucket
    - 只需要记录{LastUpdate, Value} https://www.smyte.com/blog/stopping-account-takeover?hs_preview=SqfhHtvj-5674313679
    - refill time
    bucket.value = min(maxAmount, value+refillCount*refillAmount)

* [设计短Url系统](https://www.zhihu.com/question/29270034)
    - 自增
    - 多个实例，如单双
    - 301永久重定向，压力小，但是无法统计
    - 302临时重定向，大数据分析
    - 短时间的缓存长连接到短连接的映射，解决每次请求给出不同的url





ACID

原子性能够保证一致性？不能，缺乏隔离性
事务原子性怎么实现？

rr 不可重复读：mvcc, 版本号


隔离性：为什么不用序列化？

varchar, int 后面数字的意义

redis 10000000个key，找出10000个key以固定字符串开头。

mongo：为什么用mongo，跟mysql有什么区别

mysql，集群？

tcp vs rpc vs rest api

mysql隔离级别：分别解决了什么问题

select  from where id = ... for update
id不存在是否加锁？gap锁

死锁的案例

处理死锁。超时，wait for graph


git: commit, 切换分支？权限控制？
svn:

vim: wx权限，没有写权限的文件。能读不能写。如何强制写。w! 能不能写 目录有写和执行权限。目录有写权限。
替换。vim: s

10个磁盘，1个进程，1m,10m,100m, O(1)
