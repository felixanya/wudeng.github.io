# Skynet

基于Actor模式的开源框架。

众核时代的并行编程。The Free Lunch Is Over - Herb Sutter, 2005
更多的核心，更多的线程，更大的缓存，更大的带宽。得不到更高的主频。

Erlang，98年开源，2006年传入国内C++程序员圈，Erlang China User Group始于2007，
现改名Effective Cloud User Group。

并行和分布式。
- Erlang采用的actor模式使其擅长并行处理
- 错误处理机制和储存管理为分布式服务
- Erlang并不擅长储存密集型数值计算

Actor模式
- 一切都是Actor
- 并行的面向对象模式
- 创建Actor，处理消息，发送消息
- 发送的消息和发送者解耦，异步通讯

没有Actor的语言提供的框架：
- Akka by Scala
- CAF: C++ Actor Framework by C++
- Remact.Net by .net

历史
- 2010想法
- 2011.12基于Erlang的第一版实现
- 2012.7 基于C重写
- 2012.8 开源发布
- 2014.4 v0.1.0
- 2014.11 v0.9.1

Code Base
* 5000行c核心代码
    - 消息分发
    - Actor调度
    - Timer管理
    - 基于epoll/kqueue的socket库（支持tcp/udp）
* 1000行c核心服务代码
* 1000行Lua核心库
* 5000行Lua外围库
    - redis/Mysql/MongoDB driver
    - crypt, sproto, sharedata, etc
* 单进程 + 可选Lua沙盒
* 可选分布式结构
* MIT License

消息调度模块

异步编程

coroutine vs callback
lua 5.2 coroutine的内存开销仅208字节
coroutine对异常有良好的支持
复用coroutine避免过频的GC
远程过程调用(RPC)
快速失败模式

Actor沙盒


分布式解决方案
不做热更新、只做热修复
A/B滚服，定期维护，减少复杂度



典型手游集群
* 不按硬件分服
* 玩家在登录处获取令牌
* 玩家登录任意agent池
* 逻辑服处理排行榜
* 根据运营调整


调试和优化
* 內建性能分析
* Lua模块內建监控协议



网络游戏服务器轻量架构

多份类似的业务
上下文状态

把一个符合规范的c模块，从动态库(so文件)中启动起来。绑定一个永不重复的数字id作为其handle。
模块被称为服务器service，服务间可以自由发送消息。每个模块可以向Skynet框架注册一个callback函数，
用来接收发给它的消息。每个服务都是被一个个消息包驱动，当没有包到来的时候，他们就会处于挂起状态，
对CPU资源零消耗。如果需要自主逻辑，则可以利用Skynet系统提供的timeout消息，定期触发。

service 服务：某项具体的业务
* 处理业务的逻辑 + 关联的数据状态，常驻内存
* 非独立进程，通讯效率高
* 同时存在于同一个skynet进程下，同生共死
* lua编写


skynet - erlang虚拟机
lua虚拟机 - erlang进程

虚拟机之间相互发送消息。

发送方申请内存，接收方释放。




调试控制台。

网络数据、定时器 - 消息

每个用户一个lua虚拟机。lua服务。agent

lua 5.3

服务查询路径、第一个服务。每个服务拥有要给唯一的32位id。
三个阶段：
* 加载
* 初始化，skynet.start 注册初始化函数，注册消息的序列化、反序列化函数、消息回调函数
* 工作阶段


可以有多个用户线程，skynet.fork
同一服务内的不同用户线程总是轮流获得执行权。

## 网络

工作线程。物理核心数量相关。类似调度器。
服务类似 erlang 进程。


## 消息
* 消息类型 type, 255个不同的port
    - 回应消息
    - 网络消息
    - 调试消息：反馈自身虚拟机所占用的内存情况、当前被挂起的任务数量、动态注入一段lua代码
    - 文本消息：底层的、直接使用c编写的服务处理，简单字符串
    - lua消息：可以序列化lua的复杂数据结构，大多数情况下只是用lua类消息
        - skynet.ret(skynet.pack(...)) pack返回一个lightuserdata和一个长度，符合ret的参数需求。ret只能被调用一次。
        - unpack(message, size)可以把一个c指针加长度的消息解码成一组lua对象。
    - 错误
* session
* 发起服务地址
* 接收服务地址
* 消息C指针
* 消息长度

消息：c指针/lightuserdata加数字长度。消息由发送消息方生成。默认情况下，框架会在之后调用skynet_free释放这个指针。

## 外部服务

mysql
redis
mongo



## 源码


配置相关：https://github.com/cloudwu/skynet/wiki/Config
config.path
* root
* luaservice
* lualoader
* lua_path
* lua_cpath
* snax

config配置
* thread        启动几个工作线程，不要超过实际的CPU核心数量
* logger        输出的log文件名，nil为标准输出
* logpath
* harbor        节点编号，1-255，为0时工作在单节点模式
* address       当前skynet节点的地址和端口
* master        作为slave启动时，连接到master的ip和端口
* bootstrap     启动的第一个服务及其启动参数，snlua bootstrap, service/bootstrap.lua
* start         默认为main，即启动main.lua文件
* standalone    
* cpath         用c编写的服务模块的位置

配置表通过skynet.getenv获取。

入口：skynet_main.c


## 网关服务器
lualib/snax/gateserver.lua

gateserver
* start(handler)
* openclient(fd)    fd只有在openclient调用以后才能发送消息过来
* closeclient(fd)   主动踢掉一个连接

handler，为网关服务提供一些回调函数
- connect(fd, ipaddr)   新连接建立后的回调
- disconnect(fd)        连接断开是的回调
- error(fd, msg)        连接异常时的回调
- command(cmd, source, ...) 处理skynet内部消息，收到lua协议的skynet消息会回调这个方法
    - cmd 通常约定为一个字符串，指明是什么指令
    - source 是消息的来源地址
    - 这个方法的返回值，会通过skynet.ret/skynet.pack返回来源服务
- open(source, conf) 打开监听时的回调，可以坐一些初始化工作
- message(fd, msg, sz)  完整的包被切分以后，message方法被回调
    - msg是一个c指针，处理完毕后要迪奥要skynet_free释放
    - sz是一个数字，表示包的长度
- warning(fd, size) 当fd上待发送的数据累计超过1M字节以后，将回调这个方法

单个数据包不超过65535个字节。

service/gate.lua实现了一个完整的网关服务器。
examples/watchdog.lua 启动了一个service/gate.lua服务，并将处理外部连接的消息转发处理。

## MsgServer

snax.msgserver(M) 网关服务器模板。
与LoginServer(L)一起使用。

* server.login_handler(uid, secret)
* server.logout_handler(uid, subid)
* server.kick_handler(uid, subid)
* server.disconnect_handler(username)
* server.request_handler(username, msg, sz)


## 登录服务器
snax.loginserver

配套的客户端：examples/login/client.lua

回调函数：
* server.auth_handler(token) 对客户端发过来的token进行验证
    - 验证不通过，通过error抛出异常
    - 验证通过，返回登陆点和用户名
* server.login_handler(server, uid, secret) 验证通过后，通知具体的登陆点，得到确认才可以返回
    - server 具体的登陆点
    - 如果关闭了multilogin，那么对于同一个uid，框架不会同时调用多次login_handler
* server.command_handler(command, ...) 接受一些内部控制指令，比如通知玩家下线、动态注册新的登陆点


## Coroutine

skynet.fork
skynet.wait
skynet.wakeup

## skynet
skynet.call()

## CriticalSection

```lua
local queue = require "skynet.queue"
local cs = queue()
```

## DataCenter

跨节点的数据共享

datacenter.set(key1, key2, ..., value)
datacenter.get(key1, key2, ...)
datacenter.wait(key1, key2, ...)

watchdog
    - start(conf)
    - close(fd)

cmd:
    * socket
        - open(fd, addr)
        - close(fd)
        - error(fd, msg)
        - warning(fd, size)
        - data(fd, msg)
## ShareData

sharedata.new(name, value)
* key
    - string
    - 整数
* value 
    - lua table 无环
    - lua 文本代码
    - @文件名

sharedata.update(name, value)
sharedata.delete(name)
sharedata.query(name)

## 组播
multicastd

## socket
```lua
local socket = require "skynet.socket"


```
* id = open(address, port)
* close(id)

## service
service.init(mod)

* command 此服务的lua消息处理函数
* mod.data
* mod.info 需要debug的数据
* mod.require 前置服务
* mod.init 此服务的初始化函数


## 问题
skynet服务和snax服务，如何选择？


## 参考文献
* http://gad.qq.com/content/coursedetail?id=467
* http://github.com/cloudwu/skynet
* QQ: 340504014
* skynet-users@googlegroups.com
* https://blog.csdn.net/qq769651718/article/category/7480207
