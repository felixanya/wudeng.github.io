# SSDB

alternative to redis，不支持centos 7。

KV，主要用于存储离散的，之间没有关系（或者关系被忽略）的大数据：如图片文件、大段文本等。一般KV类型都可以被Hashmap替代，但是KV会比Hashmap性能高一些。
hashmap，类型和KV相似，可用于存储大体积的数据，但不同的数据项在业务上处于某个集合。并且Hashmap维护了一个集合大小的计数。如果数据需要经常被遍历，则应该使用Hashmap来替代KV。
对于只添加不更新和删除的有排序需求的数据集合可以用Hashmap来存储而不是Zset。因为Hashmap会比Zset性能高一些。Key是按照字节顺序排序的。
zset(sorted set)，根据数据项的权重进行排序的集合。数据项是唯一不可重复的。Key是按照score的大小排序的。


```
# start master
./ssdb-server ssdb.conf

# or start as daemon
./ssdb-server -d ssdb.conf

# stop ssdb-server
./ssdb-server ssdb.conf -s stop
# for older version
kill `cat ./var/ssdb.pid`

# restart
./ssdb-server ssdb.conf -s restart
```

配置centos 自动启动
```
cp tools/ssdb.sh /etc/init.d/ssdb
chkconfig --add ssdb
chkconfig ssdb on

service ssdb start
```

客户端：
ssdb-cli -h 127.0.0.1 -p 8888

每秒钟同步输出到磁盘。

## LevelDB

* MemTable
    Key 有序
    SkipList
    Lazy delete
* Immutable MemTable
* Current
    记载当前的Manifest文件名
* Manifest
* log
    系统崩溃不丢失数据
    32K per block
        FULL
        FIRST
        MIDDLE
        LAST
* SSTable
    ssr文件
    Key有序
    Level 0可能重叠
    Level L不会重叠
        数据存储区，key-value
        数据管理区

        Block
        Snappy
        CRC

Compaction
    minor
    major





commands
    info
        cmd
        leveldb
    dbsize
    flushdb
Key Value:
    set key value
    setx key value ttl
    setnx key value
    expire key ttl
    ttl key
    get key
    getset key value
    del key
    incr key [num]
    exists key
    getbit key offset
Hashmap:
    hset name key value
    hget name key
    hdel name key
    hclear name
    hincr name key [num]
    hlist name_start name_end limit     -- list hashmap names
    hexists name key
    hsize name
    hkeys name key_start key_end limit
    hgetall name
Sorted Set:
    zset name key score
    zset name key
    zdel name key
    zincr name key num
    zexists name key
    zsize name
    zlist name_start name_end limit
    zrlist name_start name_end limit
    zkeys name key_start score_start score_end limit
    zscan name key_start score_start score_end limit
    zrank name key
    zrrank name key
List:
    qpush_front name item1 item2 ...
    qpush_back name item1 item2 ...
    qpop_front name size
    qpop_back name size
    qpush name item1 item2 ...
    qpop name size
    qfront name
    qback name
    qsize name
    qclear name
    qget name index
    qset name index val
    qrange name offset limit
    qslice name begin end
    qtrim_front name size
    qtrim_back name size
    qlist name_start name_end limit
    qrlist name_start name_end limit

https://github.com/ideawu/ssdb
http://ssdb.io/docs/
https://zhuanlan.zhihu.com/p/25349591
