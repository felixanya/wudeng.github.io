# s3server

监听端口：
8290 
8295
8296

## service

* znet
    - dispatch(type, function(session, address, cmd, ...))  注册特定类消息的处理函数
        - 大多数程序会注册lua类消息的处理函数
        ```lua
        local CMD = {}
        skynet.dispatch("lua", function(session, source, cmd, ...)
            local f = assert(CMD[cmd])
            f(...)
        end)
        ```
        - 通常约定lua类消息的第一个元素是一个字符串，表示具体消息对应的操作。我们会在脚本中创建一个CMD表，把对应的操作函数定义在表中。
        每条lua消息抵达后，从CMD表中查到处理函数，并把余下的参数传入。这个消息的session和source可以不必传递给处理函数。
        - 收到该类别的消息时回调，先经过unpack，返回值传入dispatch，每条消息的处理都工作在一个独立的coroutine中。
    - ret(message, size)
    - register_protocol() 注册新的消息类别，必须提供pack, unpack函数，用于消息的编码和解码
        - name
        - id
            - PTYPE_TEXT
            - PTYPE_RESPONSE
        - pack 返回一个string或者是一个userdata和size，推荐返回string类型。后一种主要出于性能考虑，可以减少一些数据拷贝。
        - unpack 接收一个lightuserdata和一个整数，即message和size。
        - dispatch
        - profileby
    - tostring()
    - fork(function())
    - sleep(ms)
    - register(name) 给自身注册一个名字
    - start(function())
    - wakeup(co)
    - wait()
    - abort() 退出skynet进程
    - kill() 强行杀掉一个服务
    - filter() 过滤消息在处理


    - self() 用于获取服务自己的地址
    - harbor() 服务所属的节点
    - address(address) 把地址数字转换成可用于阅读的字符串

    - register(name) 为自己注册一个别名（16个字符以内），等价于skynet.name(name, skynet.self())
    - name(name, address) 为一个地址命名
        - 以.开头的名字在同一skynet节点下有效
        - 以字母开头的名字在整个skynet网络中都有效，可以通过全局名字把消息发送到其他节点。
    - localname(name) 用来查询一个.开头的名字对应的地址，非阻塞，不能查询跨节点的全局名字
    

    - monitor("monitor") 给当前skynet进程设置一个全局的服务监控
    - uniqueservice("")
        - res_loader
        - gamelog
        - telnetd
        - debug_gate

        - role_producer
        - names
        - csender
        - mail
        - hall
        - report
        - scene
        - gm

        - exchange 
        - ejoy_monitor
    - getenv("")
        - EXCHANGE
    - timeout(ti, function())

* log
    - config {name = "scene"}
    - Info("", name, Skynet.self())
    - Infof("")

* mongo_ex
    - get_name_collection("uniqueid")

* monitor
    - register("scene", function shutdown())


* id_allocator
    - new(id_db, "land_id")

* global
    - scene_obj
        - fini()
        - save()
    - allocator

* global_func
    - loadstring(str)
    - is_same_pos(pos1, pos2)
        - row
        - col
    - gen_global_id(id)     id*SERVER_RESERVED+serverid
        - 低4位用来存服id 
        - 高位递增*10000
    - is_role(id)   [1, 1000000]
    - is_land(id)   [1000001, 11000000]
    - is_army(id)   [11000001, 12000000]
    - cancelable_timeout(ti, func)

* hardreload
    - reload(module_name)

* csender_api
    - notify(roleid, "", {})

* environ
    - config("SERVER")
        - serverid

* context
    - get_role()
    - set_role(role)

* sharenv 全局共享表
    - init()
    - fini(GLOBAL)
    - key不能是数字，初始化完成后不能增加key，首字母大写的key只能访问不能修改

* common_service.code
    - REG(skynet)
    - code
        - run

* common_service.sys
    - REG(skynet)
    - sys
        - EXIT

## service
* agent
    - register_sproto('', filter) 前端协议处理
        - hall
            - cs_gang_list()
            - cs_gang_create()
            - ...
        - client
        - scene
        - hero
        - army
        - report
        - building
        - alliance
        - mail


## mongdb

config:
* auto_create_index
* indexes
    - keys
    - options
        - name 
        - unique
* schema

collections:
* uniqueid 
* role
* report
* scene_role
* army
* land_partition
* alliances
* role_mail
* alliance_notice_mail

## sproto
Yet another protocol library like google protocol buffer, but simple and fast.

* request/response
    * integer 64位有符号
    * integer(2) 带两位十进制小数位的定点数，编码的时候*100取整，解码的时候/100还原
    * boolean
    * string
    * binary
    * *Record *代表数组
    * Record 引用结构体
    * 自定义结构体 .Record

* https://capnproto.org/

## 目录结构

* ./common/proto   sproto协议定义
    - client    定义客户端发往服务器的协议
    - common    定义一些结构体，不包含具体协议   
    - server    服务器发到客户端的协议


## 带着问题读源码

gamelog配置mongodb是为了在数据库中保存日志么？