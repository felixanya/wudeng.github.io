
所有数据放在内存。

速度快：10w每秒

value:
string,hash,list,set,zset,Bitmaps,HyperLogLog,GEO

附加功能：
键过期、发布订阅、事务、流水线、Lua脚本。


开启aof rdb持久化策略

缓存重建


监控输出
./redis-cli monitor


## 安装
```
$ wget http://download.redis.io/releases/redis-3.0.7.tar.gz
$ tar xzf redis-3.0.7.tar.gz
$ ln -s redis-3.0.7 redis
$ cd redis
$ make
$ make install
```
第三步建立了一个redis的软连接，这样做事为了不把redis目录固定在指定版本上，有利于未来版本升级，算是安装软件的一种好习惯。


## BOOK
Redis开发与运维
