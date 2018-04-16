# 游戏架构


## 移动加速
* 客户端伪造瞬移
* 客户端修改本地内存中的移动速度
* 客户端使用加速器：加快客户端的时间流逝

https://zhuanlan.zhihu.com/p/27509434

## 登录

玩家管理进程：mod_player_manager，目前主要是管理玩家进程的pid，对外部提供获取玩家pid的接口。
但这里可能有个问题，其他进程获取玩家pid的时候是通过call这个管理进程来实现的。
凡是出现call的时候，就要注意了，这个call可能会阻塞当前进程。因为被call的进程如果有进程消息堆积或者
处理需要时间比较长的消息导致无法及时响应call消息。

一个可能的办法是通过ets来管理数据，其他进程直接向ets来请求数据。


连接进程选择角色

玩家登陆以后有两种情况：
  - 玩家进程还在运行。
  - 不存在玩家进程。

第一种情况，需要定位到玩家进程，这时需要根据player_id得到玩家进程。有两种方案：
        ets表
        process dict进程字典。只能call
设想这样一个场景：客户端同时发两个登录请求，正常情况应该一个先登录，后一个顶号登录。而不是创建两个玩家进程。
所以创建玩家进程这个操作只能是同步的。即玩家登陆需要通过mod_player_mananger来创建玩家进程。



判断是否已经有玩家进程
    玩家进程退出时
        根据pid定位到玩家：

    方案：
        玩家进程管理进程，进程字典存双向数据，player_id -> pid , pid -> player_id
        start_link启动玩家进程，trap_exit, 等待`EXIT`消息。


创建玩家进程
  直接通过连接进程创建角色可能的问题：重名的问题。
  所以通过玩家管理进程来创建：控制是否能够创角。检查重名，创角个数。


## 进程字典

玩家进程写入player_id
场景进程写入map_id


## 背包

格子中的物品，有些有特殊属性，比如装备，有强化等级，开光等级，属性等，
而有些物品没有这些属性，只需要记录绑定状态和数量。通过item.id来区分。
没有特殊属性的物品，id=0，有特殊属性的物品，id>0.


是否需要支持玩家移动格子：否

增加物品：指定绑定，非绑
删除物品：指定绑定，非绑，或者都可以，都可以的话优先删除绑定

每个格子的物品都需要有一个实例ID
```
#bag_cell{
    items = [#item{type = Type, bind = Bind, number = Number, id = 0}]
}
```

item表：
id, type, expire,

{ItemType, BindType}

查询接口：
has_item(ItemType, BindType, Number)

接口：
delete_item(ItemType, BindType, Number)

## 任务

观察者模式：
  事件：杀死怪物，杀死玩家，采集，
  加入观察
  发生事件时调用observer的回调函数。


## 战斗属性管理

区分原生属性和最终属性。

拿速度来说：原生属性是玩家职业和等级对应的基本速度，最终属性是玩家的最终速度。

最终速度 = （原生速度 + 速度加值）* （1 + 速度百分比）

其中速度加值和速度百分比是通过装备，buff等加上去的。

<base_attribute, effect> => fight_attribute

属性变化时，直接修改属性变化的增量和百分比，得出变化的量。变化的量通过进程字典保存。put(hp, attribute_change),put(level, attribute_change)


根据属性是否需要广播到周围的玩家，可以将属性分成两个列表来分别处理。
有一些属性的变化不仅需要通知前端，
还需要广播给周围的玩家。
比如

坐骑变化，机甲

属性变化区分
玩家进程：增加
场景进程：buff

buff的属性分为两种：
    一种直接改变属性的。比如增加攻击、防御、速度等, TimeEffect
        眩晕：dizzy + 1， > 0 表示处于眩晕状态
    一种是间隔触发的，比如掉血等。CountEffect


get_keys(attribute_change)


## 网关进程


方法一：`Port ! {PortOwner, {command, Data}}` 异步（从R16起），只有Owner可以发送。
当Port不是端口或者进程bad_arg, 如果Port是已经关闭的端口，不会报错（disappears without a sound）。
当调用进程不是owner，或者data不是有效的iolist，owner会报badsig错误。

方法二：`port_command(Port, Data) -> true` 同步，其他进程也可以发送. 如果Port忙，会等待返回。

通过bench可以看到erlang:port_command(Socket, Bin, [nosuspend]) 比 gen_tcp快了很多。

- force 如果port忙，强行写入。如果底层不支持ERL_DRV_FLAG_SOFT_BUSY会报错。notsup
- nosuspend 如果port忙，放弃写入。

{active, true} 性能最好，只有一次epoll_ctl，但是没有流控，很容易被客户端搞死。
{active, once} 性能一般，因为每次调用{active, once}都需要调用epoll_ctl，erts只有一个线程收割epoll_wait，性能损失。
{active,N} 平衡性能和安全。收到N个包以后会收到{tcp_passive, Socket}消息。

现象：
一个进程使用port_command(Port, Data, [force])的时候报badarg错误。但是第一次出现到最后一次出现相隔了10分钟。
说明Port是打开的有效的端口。那么是数据出了问题？确实如此：protobuff的uint32字段出现负值。


* 防止一个进程拥有太多Port。因为时间片有限，广播进程可能被卡。
* gen_tcp:send的时候因为需要阻塞，进程被调出


## 消息的序列化
如解码，收到了{tcp, Data, Socket}

为什么要有Proto ID：
如何知道Data改交给谁来解码？除非只有一个模块，否则必然面临路由的选择。那就只弄一个模块吧。

protocol buffer 只有在知道binary是原本是什么类型数据的情况下才能解压出来。为了识别binary的类型，还需要一个proto id


协议包含包体结构定义，协议编号，常量定义。分别生成前后端。如果protocol buffer解决了包体结构定义的问题。
name_to_cmd
cmd_to_name

### 将网关进程挂在监控树下是否是一个好主意？

回答是NO，理由如下：

- 网关进程永远不会重启。监控树的意义在于，如果异常退出，可以自动重启？？那么网关进程就算重启也已经失去了Socket，没有意义。
    - 那么temporary 这个重启机制的监控树是有啥用呢？temporary机制永远不会重启进程。这就要考虑监控树的作用了，文档上定义了三种作用：
    starting/stopping/monitoring，启动和监控都没意义，那只有stopping的意义了。也就是Application退出时，由监控进程关掉网关进程。
    看起来意义也不大。
- 过多的网关进程会拖慢监控树的效率。监控树采用sets，dict等组织孩子节点，几千个孩子长度的list，效率够低的。

Ranch是怎么做的？
    Ranch使用一个特殊进程来ranch_conns_sup管理网关进程，accept进程创建socket以后将socket所有权交给管理进程，并且通知管理进程创建网关进程。
    在管理进程内部实现限制数量的功能。管理进程创建网关进程以后将socket权限转移到网关进程。
RabbitMQ是怎么做的？
    RabbitMQ用的就是Ranch。


创角界面的心跳。
创建玩家进程以后的心跳。

//发送登录请求
message CSLoginReq
{
    required CSLoginInfo LoginInfo = 1;
}
//登录请求回包数据
message CSLoginRes
{
    required uint32 result_code = 1;
}

enum EnmCmdID
{
    CS_LOGIN_REQ = 10001;//登录请求协议号
    CS_LOGIN_RES = 10002;//登录请求回包协议号
}


### 消息广播

前端发到后端的消息带上消息序列。后端发送到前端的包，如果是回应请求的，带上相同的seq，否则为0.
现在的实现，所有消息的封包都在网关进程，如果包体比较大，所有进程进行封包操作没必要，可以先封包，然后网关进程直接广播。

## 日志
有这么个需求。希望可以动态控制日志显示与否。

## 玩家进程

玩家进程应该由谁来启动？
    如果通过网关进程来启动的话，如何防止重复启动玩家进程。只能加锁
    所以通过管理进程来启动应该是更好的选择。需要注意的是，创建玩家进程的时候应该采用延迟初始化，防止管理进程阻塞太久。
    管理进程需要管理玩家进程的生命周期，所以应该用start_link来启动。以便监听玩家进程死亡通知。


注册拾取掉落物事件。
副本向玩家注册回调函数。



玩家的战斗信息保存在battle_info中。主要是速度，血量，buff等信息。
这个信息应该放到场景中来管理。

## 玩法活动状态

活动两个状态：
    开放
    关闭
游戏服维护一个ets表，保存每个活动的状态。玩法的开启由cron来通知控制进程，控制进程更新活动状态的时候顺便更新ets表里面记录的状态。
玩家上线以及活动状态变化时，系统推送活动状态到前端。



## 开服压力测试
通过模拟客户端登录，瞬间创建大量角色，服务器最大的压力在数据库。
创建角色的时候创建大量数据表。
在本地测试时，瞬间100个角色就出现大量超时，看了下emysql数据库的连接数是2。最大的连接数是151.
可以调大一点。

因为写数据库是spawn进程出来写的，大量的写操作去抢数据库连接导致超时。这是结构问题。需要优化成消息队列的形式，慢慢写。
通过一两个进程来写。

新创角色的时候现在很多数据表的模式都是先读再写，最好改成一次性写入。可以节省一次读操作。
    两步走：
        - 创建角色节省一次读数据库，直接初始化表。
        - 将写操作串行起来。先用1个进程写。

## Cache设计

缓存穿透：数据库中也不存在key, 蓄意攻击。解决办法：cache中插空值，布隆过滤。
缓存并发（缓存击穿）：同一个key，并发miss，并发请求数据库。解决办法：加锁，setnx(set if not exist)。只允许一个线程请求数据库。其他线程等待。double check

缓存失效：大量key同一时间失效。解决办法：随机事件失效。失效事件小于实际事件，(永远不过期)预加载。
缓存雪崩：大量缓存失效导致所有请求打到存储层。解决办法：一致性hash，缓存可用性，多级缓存。单进程写。


初始化缓存过程中，多个请求到达存储层。

cache-aside: 缓存不负责跟数据库打交道。应用程序保证缓存跟数据库同步。
read-throuth/write-throuth(RT/WT) 应用程序把缓存当成数据存储。cache负责读写数据库。应用程序逻辑很简单。
  simplify application code
  better read scalability with read-through: 缓存加载数据库数据时候先把缓存置上特殊值，只有一个线程去load数据。
  better write performance with write-behind
  better database scalability with write-behind: 大量写操作排队起来慢慢写。
  auto-refresh cache on expiration

read-through + write-behind

write-through: 同步写入数据库
write-behind: 插入和更新异步写入。删除要同步删除。write-behind queue, read once and write at a configured interval。更新时，如果不在队列，加入队列，如果已经在队列，替换之。
  coalesced
  write-combining


## 数据库优化

cache_unit的问题：修改了一个字段，需要写入所有字段。


slg_model的问题：没有解决并发读写的问题。没有解决双key。虽然支持数组，但是实际上还是有唯一key的。比如玩家的装备id，玩家id，虽然可以通过玩家id找到他的装备，实际上装备id是唯一的。
值得借鉴的地方：
  用每个表一个进程的方式维护一个行级锁。这个相对于进程排队的方式要高效，因为进程排队等价于表级锁。


采用cache aside的方式。方便将cache独立出来。可以采用第三方实现的cache，如redis等。也可以是一个ets表。
对数据的修改写入cache，同时给持久化进程发消息。可以每个表一个持久化进程，持久化进程竞争连接池。

cache中的数据有三种形式。
%% undefined: cache miss, 需要去数据库中加载
%% not_found: cache hit , 数据库中也没有
%% Record   : cache hit , 返回缓存中的数据

关于锁的设计：
  两个思路：一个是申请者发现获取失败则一直轮询。每一次查询将sleep时间*2.
  还有一个是申请者排队。需要处理进程退出的情况。
  第一种比较简单。


设计思路：
  并发控制：行锁。如果排队来进行修改，相当于表锁。粒度太大，效率太低。
  每个表对应data_lock进程，用于实现行级锁。修改前先获取key的锁。修改完以后释放key的锁。

  表的属性实现配置化。need_lock: 是否需要是需要加锁。如果是单进程写的数据，则无需加锁。

  如果cache没有命中，进程要先获取行级锁，拿到锁以后要再检查一次缓存。如果还是没有则从数据库中读取。



修改ets数据的同时向持久化进程发持久化消息。这样ets里面的数据删掉也不会丢失数据。将缓存和持久化分离。方便使用第三方实现的缓存库。内存压力比较小。
同时因为持久化不需要读取ets表，不需要整条记录写入。可以只修改必要的字段。

slg_model 有个依赖库改名了。改好就能跑起来。用rebar编译。直接make就行。windows环境。

挑战：
- 数据一致性问题 https://coolshell.cn/articles/6790.html
  - 如果针对所有读写加锁，或者串行化，锁的粒度太大。实际上相同数据写冲突时很少见的。按key锁定，如果加多个锁，要防止出现死锁。
  - 多版本并发控制MVCC， CAS实现。
- 事务，缓存如何回滚？

cache_unit的问题：写数据是即时写。开服瞬间大量写操作对数据库压力太大。

cache_unit的问题：读，修改，写的模式，如果多个进程同时进行，存在数据的竞争。解决办法是引入一个管理进程来同步执行这些操作。
但是这个同步进程可能成为瓶颈。参考空间。所有对空间的操作都通过space_mgr来执行，结果space_mgr成为瓶颈。

实际上数据的性质是不一样的。比如玩家的技能，基本上只有玩家自己会修改。这种数据不存在竞争的问题。

如果直接从数据库得到解析代码的话，需要解决的问题：怎么区分是term还是string:
这个问题已经解决：采用模式匹配的方式：
```
db2mem(Row) ->
    [case F of
         <<"{", _/binary>> -> string_to_term(F);
         <<"[", _/binary>> -> string_to_term(F);
         _ -> F
     end || F <- Row].

mem2db(Row) ->
    [if
         is_list(F) -> term_to_string(F);
         is_tuple(F) -> term_to_string(F);
         true -> F
     end || F <- Row].
```

#element{row=xx, version_number=1, read_lock=2, write_lock=0}


数据表分为三类：
- 玩家的数据，只有玩家进程自己写，可能会有其他进程读
  这种数据直接玩家进程维护
- 玩家的数据，其他进程也会写，有竞争力，比如：好友表，空间点赞、评论等
  这种数据玩家进程维护。其他进程修改时通过玩家进程执行。如果玩家进程不存在，要由全局的代理进程来执行。
- 公共数据：帮派、家族、玩法的数据，所有进程都需要读写。
  这种数据通过公共的进程来维护。


insert: 立即插入ets表，向cache_writer发送inert请求。

为什么需要一个进程。A、B同时发起读操作，然后都发现ets表里面没有东西。都向数据库发起加载请求。

如果可以保证同一时间只有一个进程读写同一条数据，那么cache_unit进程似乎是多余的。


Record => 缓存(ets, 进程字典) -> Table

lookup
    首先读缓存，
        cache_unit没有命中则从数据库中加载

insert

    同步写ets，异步写数据库
    标记为new
    写数据通过cache_writer

update
    标记为modified
    写数据库也要通过cache_writer, 否则可能出现update的时候数据还没有写入数据库。



Record有三种状态：
    new -> insert
    modified -> update
    latest -> skip


双key的应用场景？玩家装备：player_id,pos 为key



cache_unit改成evict回收以后丢失数据的情况：

interval_flush的时候，会先把dirty标记清掉，然后spawn出来一个进程去写入。这时候如果出现一个delete操作，
刚好把准备写入的数据删掉了，数据写入失败，导致写数据的进程崩掉，导致后面的数据没有写入数据库。


## 排行榜
独立服务
[boltdb](https://github.com/boltdb/bolt)

Dynamic Order Statistic 顺序统计
- based on red-black tree
- O(logn) lookup time
- select the i-th largest element


erlang 中实时排行榜的设计。（N, k）
主要支持的操作：{Key, Value}，Value变化，要实时得出Value排名前几的Key，Value. 优先级队列，最小堆。

- 更新value：update(Key, Value)
- 删除key：delete(Key)
- 获取排行榜：get_rank()



主要思想是维持一个排行榜的最小值。每次变化的时候检查key是否在排行榜，是，更新value重新排序。对于前N名，当N不大时，问题不大。然后更新最小值。
如果小于最小值。说明不在排行榜，直接更新value. 如果value只能增加，这种方式没啥问题。如果value还能减少，那么当排行榜上的value减少的时候跌出排行榜的时候，可能的问题：
  - 比如排行榜中的value跌出排行榜，这时新的value比最小值还小，这时需要等待新的value进排行榜。解决的办法是增加几个容错的。比如排行榜需要100名，我们维持前110项。


适合间隔一段时间进行排行：
另外一种方法，利用gb_sets构造一个容量为k的堆。遍历一遍找出前N名的项。这种方法不适合Value变化的时候实时更新。因为每次更新都要遍历所有的项，复杂度为O(N*logK)。
这种方式更适合间隔一段时间来更新排行榜。这种方式能够从原始的分数池中获取前K名排行榜。不需要依赖之前的值。


## 寻路
    可作为第三方服务

# 充值服
游戏服：
php实现部署比较复杂。需要nginx反向代理。合服比较麻烦。所以倾向于直接用游戏服内置的服务器。


回调服：
回调服需要分发充值请求。需要有服务器信息。所以这部分倾向于通过server_list的服务器来控制。

也可以server_list服务器提供一个获取列表的接口给回调服。


http://en.wikipedia.org/wiki/Order_statistic
