# pond

## center

青蛙的中心服主要起个什么作用？
不负责登陆。但是会有个网关。核心是个hall服务。
等待game服务器的连接？处理node协议。来自game服务器。
两个操作：
register: game服务器启动的时候注册。
unregister：game服务器退出的时候注册。

hall处理的lua消息来自agent。这里的agent是game服务器在center服务器上的代理。

那么agent是谁起的？hall收到连接以后，会创建一个node。这个node就会启动一个agent来接收来自server的请求。

服务器连过来有一个名字，一个连接句柄。中心服为名字分配一个自增的node_id。

sessions以连接句柄为key，保存连接的信息。


如果是一个新的服务器。创建一个agent。并把socket所有权指向agent。并创建一个node代表这个服务器的数据。
并且将node保存到session中去。所有每个服务器在中心服有一个agnet与之对应。启动服务是可以传参数的。

在服务代码块中用...来接收参数。

agent除了处理node消息。还处理user消息。
* online(uid)
* batch_online(uids)
* offline(uid)
* bcst_cmd(_all)
* notify(_all)
* is_online(uid)
* online_count()


game启动后会注册自己。
玩家登陆后会通知center online。

usc 是记录在线玩家。通知玩家下线。

game服会启动center_proxy，唯一服务.


gate
nodes
sessions


处理lua消息：
* node_unregister(name)
* online_info()
* get_nodes_info()
* get_user_node(uid)
* get_node()
* get_node_list()


### usc
    - users uid -> node_id
    - nodes node_id -> count

usc.lua 封装对服务usc的接口。



### hall
状态：
- gate
- sessions fd -> session
    - fd
    - ipv4 {ip, port}
    - node
        - id
        - name
        - agent
        - session
        - boot
- nodes
    - next_id
    - nodes     id -> node
    - names     name -> id
- timer

处理node协议：
* register(name, _addr)
* unregister(_addr)


## game

### account_center

处理lua消息：
* gen_gm_token(uid)
* request_token(msg)
* player_info(msg)
* parse_token(token)

### center_proxy

Env:
* client
    - cli jclient
        - call(cmd, data, timeout) 生成一个唯一session，{co, cmd, time}
            - cmd
            - session
            - timestamp
            - data
        - send(cmd, data)
            - cmd
            - timestamp
            - data
        - conn
        - host
        - port
        - sessions call出去还没回来的
            - co
            - cmd
        - cbs
            - client.kick_user
            - client.bcst_cmd
            - client.notify_user
            - client.sync_online_user_count
* node_name: name .. 随机数字

协议：处理来自中心服的请求。
* kick_user(uid) 踢下线，已经在其他节点登陆。
* bcst_cmd(_all) 向所有在线玩家发gm指令
    - cmd
    - pparams
* notify_user(_all) 通知单个玩家
    - cmd
    - ct "t"
    - c
    - uid
* sync_online_user_count(_all) 记录在线情况
    - timestamp
    - totle

### reliable_message

事件信息存储在数据库，接受者和发送者正确请求和响应之后再删除事件。保证事件的接收者即使不在线也能可靠处理。

game会启动多个reliable_message服务。

### hall


## tools

client.hello 获取服务器时间
hall.gen_token  发送账号，获取测试token

rpcservice 传入主机和端口，跟对端建立连接。封装协议发送
* timediff
* host
* port

对端有一个叫做debug_gate的全局服务。当接收到连接以后，会起一个debug服务来处理连接。
debug是一个service，所以会在配置的service路径下去查找。
debug位于service/debug目录下面。入口为main文件。会注册debug.run、debug.stop消息的处理函数。

每个服务都会注册code类型的消息run。用来处理debug_gate过来的消息。

如果指定了target，debug服务会call target一个code消息。run一个source。然后target通过lua消息返回执行的输出，
debug服务收到这个输出以后，通过debug.lua_out协议发送到客户端。



### debug

运行时修改内部状态或者直接修改代码打补丁。

debugger.lua： 
初始化好rpcservice，
注册debug协议。
传入目标服务和脚本，读取脚本，将脚本内容和目标服务作为参数，调用debug.run协议。
拿到返回结果以后，调用debug.stop，结束调用。

znet 注册了两种类型的消息：code和sys，code用来处理debug消息运行code。而sys用来优雅的退出服务。
通过sys.atexit(func) 注册一个退出回调函数，收到sys的EXIT消息以后退出。

## 协议
number
string
boolean

struct `.`开头
map
array

design/protocol/yaml/*.yaml  (schemadump)-> game/build/protocol/proto.lua


## luacheck




objectory.lua 管理对象的创建和销毁

require "obj.init"

new(role, config)
* role
* cfg
* seqs
* objs
* uninitialized
* locks

role.lua 
* uid
* conn
* cator
* ip
* obj

command


agent/command/*.lua  处理lua消息的回调函数，agent/command.lua
mt是干啥用的？ agent/mt/*_mt.lua agent/role.lua中通过inject_mt来注册的。目的是给role结构注入各种行为。



exchange是干啥用的？ game启动了一个exchange服务。配置文件为：game/etc/exchange.example

入口：service/exchange/main.lua
处理lua消息，主要支持四种操作：
* call
* send
* debug
* shutdown


* mq_urls
* mq_exchange

server_name
server_realm
connect_timeout


bind
routing_key



environ.lua 环境变量配置管理



lfs: LuaFileSystem, file system manipulation library
* lfs.attribute(filepath [, attributename | attributetable])
* lfs.chdir(path)
* lfs.currentdir()


## cjosn -> sproto

Jservice -> sproto_service




配置文件：
pond/res/
* extra.lua 程序生成。
* 其他配置文件，由json转换过来。json由excel生成。

res_loader: service/res_loader/main.lua



convertor
* mode 
    - server, lua
    - client, json




lua-crab.c
* open
* close
* filter