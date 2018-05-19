# x3server

## 编译

sudo apt install autoconf build-essential
cd skynet
git submodule init
git submodule update
make linux

* roomkeeper 一个unique snax服务，随服务启动
    - rooms{}
    - response.apply()
    - accept.close()

* room 一个snax服务
    - 非阻塞
        - accept.timeout(session)   超时
    - 阻塞
        - response.join(agent, secret)

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



## 编码

file -b --mime-encoding server/core/clib/lworld.c 

ack -f | xargs file -b --mime-encoding