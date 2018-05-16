# Redis
* string (字符串)
* hash (哈希)
* list (列表)
* set (集合)
* zset (sorted set)


所有数据放在内存。

速度快：10w每秒

* sds: simple dynamic string
  * int len
  * int free
  * char buff[]
O(1) 获取长度
杜绝缓冲区溢出
减少修改字符串带来的内存分配次数：空间预分配，惰性空间释放

* 链表
  * 双端，无环，头，尾，长度
* 字典
  - 哈希表
- 跳跃表
  - 有序集合键
  - 集群节点


redis设计与实现


key:string

value:
string,hash,list,set,zset,Bitmaps,HyperLogLog,GEO

附加功能：
键过期、发布订阅、事务、流水线、Lua脚本。


开启aof rdb持久化策略

缓存重建


监控输出
./redis-cli monitor


## 安装

### 从源码安装
```
$ wget http://download.redis.io/releases/redis-3.0.7.tar.gz
$ tar xzf redis-3.0.7.tar.gz
$ ln -s redis-3.0.7 redis
$ cd redis
$ make
$ make install
```
第三步建立了一个redis的软连接，这样做事为了不把redis目录固定在指定版本上，有利于未来版本升级，算是安装软件的一种好习惯。


### 从ATP安装
安装：
sudo apt install redis-server
启动：
sudo service redis-server start
验证：
redis-cli
设置开机自动启动：
sudo update-rc.d redis-server defaults
查看状态：
sudo service --status-all

关闭：
redis-cli 
shutdown

配置文件：
/etc/redis/redis.conf

## 查看帮助
redis-cli
* help
* config get *
* config get loglevel
* config set loglevel "notice"


## Key
## String
## LIST
## Set S
## SortedSet Z
* zrange key start stop [withscores]
* zrevrange key start stop [withscores]


## BOOK
Redis开发与运维


## 参考文档
* https://redis.io/
* http://redisdoc.com/
* http://www.runoob.com/redis/redis-tutorial.html