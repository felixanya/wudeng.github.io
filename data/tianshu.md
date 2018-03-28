# 天书


## 心跳
cs_heartbeat
    tcp_client:heartbeat()
sc_heartbeat


连接进程每10秒钟检测一次，进程字典里面dic_heartbeat_time存的是上一次客户端发心跳的时间。
如果上一次客户端心跳时间超过了1分钟。说明客户端已经不活跃，主动断开连接。


TODO：
- remove cs_login_role_heartbeat
- remove cs_login_role_list

cs_role_heartbeat：玩家进入选角界面以后发送一次，防止玩家在选角界面停留太长时间。不超过5分钟。
    tcp_client:heartbeat(self()): 修改role_list_ui_active_time，服务器不回应。

经验丹 61-64

gm命令：max_hp，max_def，max_att
加防：12,16
加血：13,15,18
加攻：14,17

从r16b03-1升级到20.2，使用kerl管理多版本。


## 充值相关

pay_count : 充值获得的元宝数量
pay_lv : 付费等级

diamond : 拥有的元宝数量

base_pay 充值档位
base_monthcard 月卡

## svn

svn://192.168.4.168:6379/game6/program/code/server/trunk

/tools
/design
/program/proto
/program/code/client/Game
/program/code/server/trunk

## 登录
登录时间：login_time
登出时间：logout_time


判断顶号：
    写入时机：
    删除时机：
    ets_player_pid
        player_id
        player_pid
        account

建立连接，客户端发account过来，获取玩家列表，选择角色登录。

如果没有玩家进程。
    怎么判断？查找ets_player_pid表。
创建玩家进程。
    init        加入ets_player_pid
    terminate   删除ets_player_pid



egrep "{cs_login,1000001,\"22\"|tcp terminate, PlayerId:1000020|mod_player:156 1000020" console.log > xx


建立连接1
15155 2017-12-28 11:35:56.344 [debug] <0.29907.18>@tcp_client:220 packet from undefined: {cs_login,1000001,"22",[<<"mypassword">>],"0","0",0,[],[],1,true}

建立连接2
15169 2017-12-28 11:36:10.584 [debug] <0.30030.18>@tcp_client:220 packet from undefined: {cs_login,1000001,"22",[<<"mypassword">>],"0","0",0,[],[],1,true}

断开连接1
15295 2017-12-28 11:36:17.302 [info] <0.29907.18>@tcp_client:258 tcp terminate, PlayerId:1000020, account:"22", PlayerPid:<0.29905.18> Reason:normal
15303 2017-12-28 11:36:17.560 [info] <0.29905.18>@mod_player:224 close_gate_proc___1 4
玩家进程1退出
15393 2017-12-28 11:36:27.562 [info] <0.29905.18>@mod_player:156 1000020 terminated! normal

断开连接2
15406 2017-12-28 11:36:37.473 [info] <0.30030.18>@tcp_client:258 tcp terminate, PlayerId:1000020, account:"22", PlayerPid:<0.30028.18> Reason:normal

玩家进程2出错，无法退出

连接进程

所有连接进程都挂载了tcp_client_sup进程下面。有连接过来以后，启动tcp_client_sup的孩子进程。也就是tcp_client:start_link().

gateway_app -> gateway_sup ->
  tcp_client_sup
  tcp_listener_sup
    tcp_acceptor_sup
    tcp_listener

gen_statem



## 背包

lib_reward:award(RewardList, Type)



## 优化点
* 通过PlayerId获取Pid的操作：目前通过global来实现，需要改成本地ets。
    * ets_online 掉线了貌似就移除了
    * ets_player_pid
    * 如果用本地的ets来做的话，需要考虑跨服直接call玩家进程的情况
* 通过名字搜索在线玩家：家族中，ets:match_object ?
* 打宝塔组队匹配推荐玩家
* 创角立马登录，通过账号去数据库查询，有可能查不到。解决办法: 选择角色带id


## 战斗属性管理

速度，防御的增加，减少如何实现？


(基础值 + 加减值) * (1 + 百分比值) = 最终值


buff影响加减值和百分比值，buff变化的时候计算最终值。
战斗计算的时候直接用最终值。

等级变化 => 基础知识变化
buff变化 => 加减值，百分比变化

两种情况都需要重新计算最终值

关于buff：
  功能buff：针对玩家。这部分buff的时间由玩家进程管理。
  战斗buff：所有战斗对象。这部分buff由场景进程管理。切换场景以后移除。

  所有buff都通过场景进程添加，因为需要广播。
  切换场景的时候，buff变化需要通知前端？



## ETS表

* ets_online
    - #ets_online{}
* ets_player_pid
    - #ets_player_pid{}
* ets_player_battle_info
    - #battle_info{}

## 性别
role_type
* 女1
* 男2
role_base_id: 20 + role_type
角色表id

## 技能

技能由兵魂决定，兵魂有多组，初始激活并装备一组。装备的兵魂id决定技能(base_soul_weapon)。
女主角只有一组普攻，男主角有两组普攻。

技能槽有5个位置，1号位普攻，2号到4号技能，5号闪避。

## 家族

* 上限5人
* PK模式
* 聊天频道

## 世界BOSS

多人PVE
BOSS血量同步

## 八卦副本

单人副本

## 银两副本

单人副本

## 千层塔

单人副本


| 副本 | 类型 | 人数 | 是否跨服 |
| 八卦阵 | PVE | 单人 | 本服 |
| 千层塔 | PVE | 单人 | 本服 |
| 银两副本 | PVE | 单人 | 本服 |
| 打宝塔 | PVE | 多人 | 本服 |
| 家族战 | PVP | 多人 | 跨服|
| 玲珑幻境 | PVE & PVP | 多人 | 跨服 |
| 英雄帖 | PVP | 两人 | 跨服 |

## 英雄帖

hero_mgr
跨服单人PVP

## 打宝塔

pagoda_mgr
pagoda_ctl
三人组队本地PVE副本
可以单人进入
每天一次免费进入次数，可以购买一次。

## 玲珑幻境

fairyland_mgr
fairyland_ctl
跨服多人副本

## 家族战

family_war_mgr
family_war_ctl
跨服多人PVP

报名：设置is_enroll=1, is_enroll=1的家族，成员无法退家族，也无法解散家族。
到点以后中心服拉取报名家族信息插入ets_war_family,
然后进行匹配，并应对玩家的查询请求。

为了防止玩家报名后退家族，家族战期间不能退家族。


## server_list

DDNS不稳定，解析时间太长，所以必须得外网部署一个。
那内网再部署一个就没必要了。可以统一用外网的。


## AI

基于行为树，行为树直接用Erlang数据结构手动编写，方便修改。


## 排行榜

一个进程负责排序，存储通过lib_global_value存储

一个最小榜单value，所谓看门人。大于看门人才能进。
    对于可能增加可能减少的排行榜，如果是减少需要检查是否在排行榜中。
    对于由于减少导致掉出排行榜的情况不处理？可以:门槛只能越来越高。
    当list长度小于100是，门槛一直为0

更新通过cast进来，

请求的时候直接读缓存，不走排序进程。


## 挂机

玩家进程离线时，terminate，开启挂机。

通过挂机进程管理挂机玩家。
    timer: ets:delete(PlayerId)

    ets: ets_offline_player: (PlayerId, MapPid, RoleId).
    ets: ets_offline_map, {MapPid, RoleId}, 正在挂机战斗的{} 正在等待战斗的。

玩家上线：
    结算离线奖励。
    干掉离线怪物形象。
    一个离线只能对应一个分线。


    场景进程：
        添加玩家形象。
        移除玩家形象。
        每个地图初始化。
        AI
            周围有人时跑AI循环，AI以刷怪点为单位。
            当成主角处理：
                type = player

            BattleInfo还是存ets表。会修改技能和base_role_id，但是玩家重新上线以后会覆盖，所以没问题。
            机器人死后移除AI


刷出机器人的时机：每张地图每个刷怪点最多5只。时间到后移除。

死亡的时候移除，或者自动复活。
登录的时候结算奖励
MechAI

压测机器人，进场景。


BUFF属性变化，去老的，加新的。
排行榜分页显示。


BattleInfo=lib_player:get_battle_info(2000019).
lib_offline_player:create_offline_player(BattleInfo).

* https://gamedevelopment.tutsplus.com/series/understanding-steering-behaviors--gamedev-12732



`lists:filter(fun(Level) -> case lib_offline_player:get_base_monster(Level) of [_] -> false; _ -> true end end, lists:seq(1, 300)).`


搭建一个敏感词过滤系统



##

battle_info存地图进程的理由：
    玩家已经离开场景，但是buff或者技能杀死了怪物，掉落物会导致前端出错





    dbgon(lib_skill,skill_action),dbgadd(lib_skill,do_shift).



机器人移动：
    机器人id

    地图：从起点 走到 终点
    移动：前端寻路



IDE
    XML/Scene/1.Xml
        root
            Scene: ID,Name,Music,Path
            Born: Pos,Forward
            Triggers
                Trigger:ID,Pos,Forward,Center,LossyScale,MapId,MapPos
            Monsters
                Monster:ID,MonsterID,Pos,Forward,Radius,Number,Rebirth
            Obstacles
    SceneVO

寻路作为第三方服务


## 内测问题

* 服务器重启更新服务器列表状态问题：url没有更新。
* 怪物刷新位置问题：由修改怪物的复活导致的刷新点偏移问题。
* 家族战没有正常启动的bug：{start_match} 少写了大括号。
* 主角死后复活攻击没有伤害：跟怪物不攻击的原因一样，都是加上了buff没有清掉。不过玩家的buff是因为死亡没有清除。
    技能结算的时候由于先算buff，后算伤害，被攻击死亡那一次加上了buff，但是后面的buff_timer看到玩家死亡了直接从
    监视列表中移除了玩家。导致buff一直在身上。
* 野外boss是后端控制刷新。地图杀满一定数量的小怪就会刷boss出来。策划配置了复活，所以可能有多个boss。去掉复活即可。
* 死亡后有保护buff，复活以后切换全体模式没有移除保护buff：已经修复。

## 疑难杂症
* PVP，A攻击B，B死亡，原地复活，B加了保护Buff，但是A还是可以砍B。
- buff肯定加上去了。
- 时机问题？不也像。
加了点log，发现确实加上了保护，但是确实死了。那么只有可能是过滤的函数有问题的。一项项看下来还正式，
把FromType写成了FromPos。

* 怪物不攻击（2018-03-20）

长松同学反映，开发服上有些怪物不攻击玩家。我估计可能是中了某种不攻击的buff。
于是让他把怪物杀死，看看重生后的怪物会不会攻击。结果是仍然不会。这就奇怪了。难道是AI的问题。
在我本地跑了一下，没有问题，那就是开发服上的问题了。找了只怪物看了下，确实是有过期的石化buff。
那么为什么buff到期了没有清理掉呢？难道是清理buff的机制除了问题？检查了一遍代码没发现问题，
手动加buff，时间到了正常移除。注意到怪物身上的buff是几小时前加上去的。于是找到了那段时间的log。
发现并没有找到给这只怪物上buff的log。而buff的施法者确实在那个时刻给一些怪物上了buff，然而并不包括当前这只怪物。
于是结论就很明显了：当前怪物的这个buff不是这个玩家加上去的。那么buff是哪里来的呢？
只有一种可能，就是继承来的。查看了一下怪物复活的机制，果然没有把buff清理掉。

* 家族任务只有5次，但是有人完成了6次

从log上看到确实只接了5次任务。定位到了修改次数的地方，发现是先检查内存，然后cast到另一个进程去修改内存。
那么如果玩家最后一次攻击造成多个怪物死亡，那么这个地方会被调用两次。第一次的修改由于是异步的，第二次调用的时候很可能还没有修改完毕。
所以就会出现调用两次。
