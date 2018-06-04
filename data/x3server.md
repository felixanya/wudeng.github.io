# x3server

## 编译

sudo apt install autoconf build-essential
cd skynet
git submodule init
git submodule update
make linux

* roomkeeper 一个unique snax服务，随服务启动，负责创建房间
    - rooms{}
    - response.apply()
    - accept.close()
    - accept.update()
* room 一个snax服务
在frame里面驱动。10毫秒一次tick。
    - 非阻塞
        - accept.timeout(session)   超时
    - 阻塞
        - join(agent, secret)
        - is_wairing()
        - update()
    - world
        - island_data
        - man_count
        - finish_count
        - messages
        - frame
        - session_camp_map

* udpserver 一个snax服务

* agent
    - snax服务，处理来自客户端的请求
    - disppatch_client(_, _, name, msg)
        - join(msg)
        - cancel_join(msg)
        - query_info(msg)
        - query_ranklist(msg)
        - heartbeat(msg)



import.lua

## gated

维护了一个内部id，每次玩家登陆自增1.


## 登陆流程

认证通过以后，gated启动agent，

玩家登陆进来，启动一个agent服务。

room是一个snax服务，
每个房间room对应了一个world，world保存游戏双方的数据。提供一些接口。

当所有对战双方都进入房间room以后，开启战斗。
启动一个frame服务，frame以定时器的方式驱动room的update。并创建一个island。


房间每帧update，向客户端发送消息。
调用 world.island:tick(world.frame)

simmain，修改config默认启动simmain，通过simmain启动一个房间，并且接受外部传入的指令（debug_console）。

`call 8 "exit"`
`call 8 "REQ" "is_fighting"`


服务内部如何获取.type, .handle

warrior
    - type
    - player


meld:
    - man_type
    - type
    - tiles



## 问题

青蛙的中心服起了啥作用？

关于多重登陆：青蛙并没有限制多重登陆。