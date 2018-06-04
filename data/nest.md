# nest

## Center

* share
    - monitor
    - res_loader
    - telnetd
    - debug_gate
* local
    - user_db 更新用户，thread个服务
        - users lru cache
        - 新开一个协程，每分钟将脏数据同步
    - account_center
    - exchange
    - ejoy_monitor
    - servers
    - station 集群管理服务。提供中心服的连接点，用于其他服连接过来，全局唯一
    - auth
    - gm

## Game
* share
    - monitor
    - res_loader
    - gamelog
    - telnetd
    - debug_gate
* local
    - role_producer
    - names
    - hall
    - gm
    - exchange
    - ejoy_monitor


## account_center
Lua API:
* gen_gm_token(uid)
* ban(uid, ban_ts)
* unban(uid)
* request_token(msg)
* player_info(msg)
* verify_token(token)

Env:
* users[account] = {
    uid,
    token,
    ban_ts,
    player_info
}


认证：

base64(json{
    account,
    uid,
    expire,
    checksum : hash(account, uid, expire, token)
})


## user_producer
创建用户，跟数据库打交道。

LuaAPI:
* create(account) 返回uid
{
    account,
    uid,
    token
}

Env:
* uid_db
* user_db
* allocator

## auth

.auth 这个服务，自己既充当watchdog，又充当agent。那么agent消息的回调函数在哪里？
command为空，说明不处理lua消息。为什么要多此一举呢？
除了lua消息，就只有text消息，那么入口只有watcher这里了。但是watcher这里只处理连接，不处理数据。
那么数据是哪里处理的？

因为require了sproto_service这个模块。我们看看这个模块做啥了。
这个模块注册了PTYPE_SPROTO这种消息。这就是data的的处理函数了。
实际上Service.enable('auth', filter) 这个语句，注册了auth消息模块的处理函数。

数据结构：
* ss {sessions = {}} fd -> session obj
    - fd
    - ipv4 = {ip, port}
    - device
    - account
    - tag

* gate c网关

协议处理：
* auth 认证第三方授权。假设已经拿到了第三方的授权token，服务端校验，返回uid。
* authorize 申请服务器登陆授权。向中心服申请一个登陆token用来登陆服务器。返回一个token


前端认证流程：
通过第三方平台获取认证token，然后用认证token向登陆服认证获取登陆token。

servers是一个全局唯一的服务。管理服务器列表。

## servers

lualib/servers.lua 封装了服务器列表管理的一些接口。负责向全局唯一的管理服务发消息。
lualib/service_obj.lua 设置好目标服务和协议类型，简化向目标服务发协议的流程，分为call和send
servers/server_obj.lua 定义server和servers数据结构


Lua消息处理：
* online(serverid, token) game服注册自己，生成sessionid，用于登陆token的加密
* offline(serverid)
* update(serverid, info)
* reload_servers()
* cb_reload_servers()
* query_servers(device, platform, tag)
* list_servers()
* get_server(serverid)
* online_count()
* authorize(serverid, cator)

数据结构：
servers，serverid -> server

server
* obj 静态属性，通过外部配置工具修改，从数据库中获取。
    - serverid
    - name
    - status
    - hidden
    - forbidden
    - capacity
    - default
    - opentime
    - addr
* extra 动态属性，运行过程中通过服务器上报获得，为nil表示服务器离线
    - count
    - sessid


## station 中心服

服务器管理，在线服务器列表，每个服有个queue。

game服务器的有一个循环，会call中心服的station服务的server_loop方法，获取中心服下发的事件。
这些事件的回调函数定义在 center_event_handler中。目前事件主要是踢玩家下线。
所有事件定义在 lualib/center_events.lua文件中。
* kick_user

玩家管理

API:
* server_register(serverid, token) 服务器上线
* server_unregister(sessid) 服务器下线
* server_update(sessid, info) 更新服务器信息
* server_loop(sessid)

games
* games  serverid -> server 为什么这里还有要一个？
* sessions  sessid -> serverid
* online_uids uid -> sessid


## gm

服务端启动一个telnetd的服务，这个服务充当一个watchdog，
处理PTYPE_GM_GATE_NAME类型的消息。（消息从而而来）
* open(id, addr) （这条消息来自gm_gate，新连接到来的时候）
    - 新连接过来，获取tips，并且推送到客户端，这里就是show_welcome，这条tips来自gm服务，通过call gm服务获取到的。
* close(id)

telnetd启动一个服务：gm_gate，并把自己的地址、最大连接数、端口传过去。gm_gate负责监听端口8286。
gm_gate最多处理1024个连接。
新连接到来的时候会调用add_conn(id, addr) (怎么做到的？)，并且向telnetd发送一个open的消息。
telnetd收到这个消息，向gm_gate回两个消息，一个是forward，一个是start （发来发去什么鬼？）

gm_gate处理的PTYPE_GM_GATE_NAME消息：
* forward(id, agent) 来自telnetd的消息。所谓的agent，其实就是telnetd的地址。
* start(id) 来自telnetd。开始接收消息。
* kick(id)
* close()

gm_gate处理文本协议：

以及lua消息：

gm_gate收到客户端过来的消息，丢给telnetd，然后telnetd去call gm服务的接口：exec_cmd

可以看出，gm服务才是最终执行gm指令的地方。看看gm服务主要做啥了。

找到serice/gm/main.lua文件，很简单，就是处理lua消息。
这里特意说下servcie的组织结构：
main.lua放入口文件。command.lua放lua消息处理函数。global放服务的变量。
主要处理的消息如下：
* exec_cmd(playerid, cmd) 这个应该是最主要的，被telnetd这个服务调用。
* exec_cmd_restricted(playerid, cmd, permission)
* exec_tcmd(playerid, cmd, args, permission)
* show_welcome()




服务启动流程：
skynet启动服务的接口：
newservice(name,...) 向.launcher发送LAUNCH消息

uniqueservice(global,...) 向.service发送LAUNCH/GLAUNCH消息。
也就是说唯一服务，不管是本地唯一还是全局唯一，都是通过service_mgr来实现的。
可以有多个协程来启动服务，但是只有一个会成功，其他都需要等待这个成功了才能得到服务地址。

这个注册为.service的服务这个服务是由service_mgr来实现的。我们看看它都做啥了。
也是起了一个服务：处理收到的lua消息。并且注册.service这个名字。如果是单例模式，顺便注册一下SERVICE这个名字。
否则就要想全局的SERVIE通报一下自己的存在。

register_local/register_global是根据自己的定位，注册一下相应的lua处理函数。看起来这两个函数没做事，其实是因为cmd是外部传入的upvalue,
在cmd下面定义函数就相当于在cmd里面添加了入口。修改cmd才是这两个函数真正的目的。

全局名字会在前面加@符号。

service_manager的cmd：
* LAUNCH(service_name, subname, ...) （怎么多了个subname，是个什么鬼） 对于snax服务来说，subname才是他真正的名字。
* QUERY(service_name, subname)

如果是local：
* GLAUNCH(...)
* GQUERY(...)
* LIST()

如果是global：
* GLAUNCH(name, ...)
* GQUERY(name, ...)
* REPORT(m)
* LIST()

service_mgr有一个数据结构：service[name]，值可能是number，也可能是table。
* table表示等待它的协程？对啦！！所以这个服务起来了以后要将这些协程唤醒。
    - launch 标记，防止重入
* number表示啥？服务自身？对啦。
* string呢，其实就是服务启动失败的消息啦！


有两个函数：waitfor是入口，最终通过request来执行new_service。只允许一个协程来执行request。其他协程要等待。
等到request执行完毕，会将这些等待的协程唤醒。并且将service[name]设为服务地址。后续的请求直接返回这个地址就行了。


如果不是snax服务。最终都是通过skynet.newservice来启动的。这个函数通过call 一个注册为.launcher的服务来启动。这个服务是个c服务。

```lua
function skynet.newservice(name, ...)
	return skynet.call(".launcher", "lua" , "LAUNCH", "snlua", name, ...)
end
```

由于是call服务，这个协程需要让出执行权。等到唤醒的时候能够继续下去。调用send会得到一个唯一的session，
我们把这个session记录在watching_session中。


还是没有找到怎么加载代码的，特别是如何加载文件夹里面的代码的。突然想到了文件夹下面init.lua文件不需要明确加载，可以直接加载文件夹这个问题。
当时觉得奇怪还特意问了ly。检查了 package.path, 和 package.cpath

```
ubuntu@deng:~$ lua
Lua 5.3.4  Copyright (C) 1994-2017 Lua.org, PUC-Rio
> = package.path
/usr/local/share/lua/5.3/?.lua;/usr/local/share/lua/5.3/?/init.lua;/usr/local/lib/lua/5.3/?.lua;/usr/local/lib/lua/5.3/?/init.lua;./?.lua;./?/init.lua
> = package.cpath
/usr/local/lib/lua/5.3/?.so;/usr/local/lib/lua/5.3/loadall.so;./?.so
```
所以说init文件可以直接加载文件夹即可。这里会不会也是一样？从效果上来说，这里加载文件夹等价于加载文件夹下的main，那么肯定是哪里配置了加载路径。
找到配置文件：config.example，果然：
```lua
-- subsystem config
common_lua_path = root.."lualib/?.lua;" .. root.."lualib/?/init.lua;" .. root.."skynet/lualib/?.lua;"
lua_path = branch.."lualib/?.lua;" .. branch.."lualib/?/init.lua;" .. common_lua_path

common_lua_service = root.."service/?.lua;"..root.."service/?/main.lua;"..root.."skynet/service/?.lua;"
luaservice = branch.."service/?.lua;"..branch.."service/?/main.lua;"..common_lua_service
```

所以统一丢在service文件夹下面，建立文件夹，然后里面建立main文件就可以了。好处？可能组织结构更加清晰吧。




使用nc连上去：
```
nc localhost 8286(center) 8296(game)
```
指令：
* help
* help groupname

groups:
* admin
    - add_exp
    - set_level
* base
    - help
* debug
    - echo
* servers
    - reload_servers
    - query_servers
    - list_servers 服务器列表
* state
    - kick
    - ban
    - unban
* system
    - sys_list
    - sys_stat
    - sys_info
    - sys_mem
    - sys_cmem
    - sys_task
    - sys_signal
    - sys_gc
    - sys_agent_online_count
    - sys_shutdown
    - sys_maintain
    - sys_clear_code_cache
    - sys_reload_resouce
    - sys_reset_agent_pool
    - sys_write_info
    - shrtbl
* test
    - force_sys_shutdown
* view
    - online_count
    - user
    - role_by_roleid

game/service/gm/
* admin
* base
* debug
* state
* system
* test
* view


## Game



## hall

注册hall、client的协议处理。处理text消息。主要是c网关发过来的连接信息。由watcher处理了。
watcher中的self是socket句柄fd。watcher调用connection处理连接。通知c网关将消息发送到本服务。
gate_api封装了对c网关的操作。比如开启监听，设置协议转发目标服务。刚开始建立连接的时候，因为还没有建立起agent，只能由hall来处理。
所以connection在打开连接的时候，通知网关，先把消息发到本服务。

sproto_service负责sproto协议的处理。hall服务通过调用sproto_service来注册消息处理函数。

下一步，玩家发送hall.login(token, _addr)登陆。

tokens定义了一组api：
* gen_token(content, password)    content -> token
* parse_token(token, password)    token -> content

看看服务器怎么注册自己的：
每个服务器定义了一个password，这个password在初始化的时候会写入数据库。所以中心服的服务器列表管理是持有这个password的。
这个password就相当于一个加密解密的秘钥。用来加密和解密token。


* {serverid = serverid, timestamp = now} -> token, password 是中心服和服务器共享的一个key。

中心服的servers服务收到服务器注册的请求：生成一个sessionid，这个sessionid也作为加密的key。用来加密game_token。
game_token是玩家向中心服登陆获取的一个token，用来登陆game的一个凭证。其实就是用sessionid加密的一个json数据结构。
{
    sessid = sessid,
    obj -- 静态属性
}


中心服的station服务会监听来自其他服务的lua消息。主要是一些服务器的管理消息。比如服务器上线，下线、修改等。



数据结构：
* serverid
* sessions
    - sessions fd -> session obj
        - fd,
        - ipv4: ip, port
        - user 通过attach绑定到session上面，建立连接以后，通过hall.login登陆的时候。
        - uid
    - bans uid -> ban_ts
* timers
* users
    - users uid -> user
        - uid
        - session
        - cator
        - client_info
        - login_session
        - role
    - account2uid account -> uid
* center 负责跟center通信
    - serverid
    - password
    - sessid
    - parse_token(token) 不需要请求中心服。本地校验即可。
* agent_pool
    - get(uid) 

agent_pool
* node_creator
* node_deleter
* count
* threshold
* online_key uid -> node
* nodes i -> node
    - count
    - work_times
    - addr
* retired_nodes 什么鬼



hall.login
hall.enter_game


common/sproto/

