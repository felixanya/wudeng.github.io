# 分布式哈希表 DHT
Kademlia(Kad)
Chord


分布式系统的关键基础设施。

P2P技术三代：
- 单点故障
- 广播风暴
- DHT


使用场景：
- 分布式文件系统IPFS
- 分布式缓存
- 暗网比如I2P，FreeNet
- 无中心的聊天工具，如TOX
- 无中心的微博客，如Twister
- 无中心的社交网络


难点：
- 无中心
- 海量数据
- 节点动态变化
- 快速定位节点的挑战

散列算法的选择：大于等于128位
node分配唯一id，与数据key同构。

拓扑接口，路由算法 key-based routing
覆盖网络：Overlay Network，网络之上的网络，DHT，基于互联网之上的覆盖网络。
设计拓扑结构和路由算法时，只需要考虑node id，不需要考虑下层网络属性。

每个节点只知道少数一些节点的信息。路由算法直接决定了DHT的速度和资源消耗。

路由表越大，可以实现越短的路由，但是路由表的维护成本越高。

距离算法：节点之间的距离，数据之间的距离，节点与数据之间的距离




数据定位：
最关键的功能：保存数据，获取数据；

递归处理。

保存数据：计算节点与数据的距离，找出自己知道的跟数据最短的距离的节点。
获取数据：





## Chord

诞生于2001年。第一批DHT协议。CAN、Tapestry、Pastry。

简单。

Consistent Hashing 一致散列

路由机制：
- 基本路由，低效，复杂度O(N)
- 高级路由，基于Finger Table, O(logN)



## Kademlia(Kad) 协议

http://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf

## Mainline DHT

Standard DHT used by BitTorrent

https://en.wikipedia.org/wiki/Distributed_hash_table
