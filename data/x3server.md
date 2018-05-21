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

## 编码

file -b --mime-encoding server/core/clib/lworld.c 

ack -f | xargs file -b --mime-encoding