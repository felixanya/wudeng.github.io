# aoi

九宫格，每个格子由一个双向链表将格子中的对象组织起来。

ObjId为64位无符号整数。层Id为32位整数。


guid      ObjId
unit_type 类型，kUnitType_Player=1, kUnitType_Monster=2
x
y
layer_idx 层id是不是就是地图id？

## AOI

提供对外统一的操作接口，支持分层

数据结构：
* unit2layer_index_[guid] = layer_idx, 从ObjId到层ID的映射
    - uint64 -> int32
    - layer_idx >= 0, -1代表失败
* index2layers_  
    - int32 -> `*AOILayer`
    - 从layer_idx到AOILayer
* player2group_guid_   player_guid -> group_guid
* groups_   
    - uint64 -> TGuidList
* player2group_guid_   记录玩家到组的映射
    - uint64 -> uint64
    - player_guid -> group_guid

支持操作：
* AddUnit(guid, uint_type, x, y, layer_idx)
    - 层id由外部传入
    - 添加单位到指定的层
* UpdatePos(uint64, x, y)
* AddGroup(group_guid, group)
* RemoveGroups(group_guid)
* Update(timestamp, player_update_info)

## AOILayer
格子逻辑的真正实现

数据结构
* other_unit_map_ 记录怪物信息
    - uint64 -> UnitInfo
* player_map_  记录玩家信息
    - uint64 -> PlayerInfo
* unit_buckets_  保存当前格子的单位列表，以格子id为key，指向UnitInfo的链表头。
* remove_list_ 记录需要在下一次update的时候需要删除的元素
* group2player_list_  组到玩家列表
    - uint64 -> TPlayerList

支持操作：
* RemoveUnit(guid)
* AddUnit(guid, unit_type, x, y)
* UpdatePos(guid, x, y)
    - 更新单位的pos_
    - 计算老的格子和新的格子，如果不一致，从老的格子移除，插入新的格子
* Update(timestamp, player_update_info)
    - 这个函数由时间驱动，有相对固定的时间间隔，随着人数变化，人数少，频率高，人数多，频率低
    - 需要记录上一次更新的时间戳

## 数据结构
UnitInfo 双向链表，插入的时候在头部插入
* guid_
* flag_ UINT_REMOVED | UINT_INIT
* prev_
* next_
* pos_ Vector2(x, y)
    - x
    - y
* pos_last_ 上次Update的时候所在的位置
* players_looked_me

PlayerInfo，继承UnitInfo
* guid
* group_guid_ 记录玩家当前所在的组，队伍？可能性很大。队伍如果在周围，总是要看到。
* group_guid_last_
* sight_[2] 0,1
    - 类似界面缓存buff那种机制？


TplayerUpdateInfoMap
* uint64 -> PlayerUpdateInfo
    - guid
    - enter_guids_
    - leave_guids_
