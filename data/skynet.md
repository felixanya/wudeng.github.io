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

## 源码分析

服务模块：
* create
* init
* signal
* release

四种线程
* monitor 检测节点内的消息是否堵住
* timer 定时器
* socket 网络数据收发
* worker 对消息队列进行调度，数量可配置
    - 全局消息队列
    - 次级消息队列，消息，自旋锁


lua服务收到消息产生一条协程。每个服务都有一个协程池。

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

msgserver   -   gen_server
gated       -   Module
skynet提供的一个登陆框架，这个框架需要使用者提供一些回调函数，而gated提供了一些回调函数，注入到msgserver中。

u = user_online[username]
    - fd
    - ip
    - version
    - secret
    - index
    - username
    - response = {session} session 在message中
connection[u.fd] = u


msgserver提供的接口：
* userid(username)
    - return uid, subid, server
* username(uid, subid, server)  `base64(uid)@base64(server)#base64(subid)`
    - return username
* login(username, secret) 在login_handler中调用它，注册一个登录名对应的secret
    - update user secret
* logout(username) 让一个登陆名失效（登出），通常在logout_handler中调用
    - user logout
* ip(username)
    - return ip when connection establish, or nil
* start(conf)
    - start server

snax.msgserver(M) 网关服务器模板。定义了一组API，用来启动gateserver。
* **connect(fd, msg)**
* **message(fd, msg, sz)**
* open(source, conf)
* disconnect(fd)
* error(fd, msg)

与LoginServer(L)一起使用。

* server.login_handler(uid, secret) 用户登录后，登录服务器转交uid和secret
* server.logout_handler(uid, subid) 用户登出
* server.kick_handler(uid, subid) 当外界（通常是登录服务器）希望一个玩家登出时
* server.disconnect_handler(username) 用户的通讯连接断开以后
* server.request_handler(username, msg, sz) 用户发起一个请求

* server.register_handler(servername) 向登陆网关注册登陆点


## gateserver

gateserver提供一个框架，由外部提供一组回调函数，这里是由msgserver提供回调函数。
监听端口，处理客户端过来的连接请求。socket类型的消息主要分为几类：
* data
* more()
* open(fd, msg)
* close(fd)
* error(fd, msg)
* warning(fd, size)

connection[fd] = true | false

## 登录服务器
snax.loginserver 登陆框架，需要一组回调函数，由logind提供。

启动1个master，注册名字，监听端口，收到连接转发给slave进行认证。默认8个slave（可配置instance），实现认证逻辑。

x3server中，登陆服务器配置通过logind模块提供。

master会记录登陆玩家的信息在user_online中。主要包括：user_online[uid] = {address,subid,server}
* address 网关服务的地址
* subid
* 网关服务的编号

master需要的配置：
* instance slave的个数，存放在slave表里面。
* host 主机
* port 端口
* command_handler 进行lua消息处理，主要是两类消息：
    - register_gate(server, address) 由网关服务调用，网关服务向登陆服务注册自己的地址。
    - logout(uid, subid) 由网关服务调用。表示玩家离线了。

master接收lua消息。

slave 需要的配置：
* auth_handler

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
* id = socket.open(address, port) 建立tcp连接，返回一个数字id
* socket.close(id) 关闭一个连接，可能阻塞执行流
* socket.close_fd(id) 直接关闭，避免阻塞
* socket.shutdown(id) 强行关闭一个连接。不等待。适合在__gc元方法中关闭连接，gc过程无法切换coroutine
* socket.read(id,sz) 读指定的字节数
* socket.readall(id) 读所有数据
* socket.readline(id, sep) 读到的数据不包含这个分隔符
* socket.block(id) 等待一个socket可读
* socket.write(id, str) 把字符串置入正常的写队列，框架会在可写时发送它
* socket.lwrite(id, str) 把字符串写入低优先级队列
* socket.listen(address, port) 监听一个端口，返回一个id，供start使用
* socket.start(id, accept) 监听的id上有连接接入时调用，accept函数会得到连接的id和ip地址
* socket.start(id) start以后才能读到socket上的数据，向一个socket id写数据也要先调用start
* socket.abandon(id) 清除id在本服务内的数据结构，但并不关闭这个socket。用于转交socket的控制权
* socket.warning(id, callback) 待发送数据超过1M，系统调用callback以示警告，function callback(id, size) size单位是k


## socketChannel



## service
service.init(mod)

* command 此服务的lua消息处理函数
* mod.data
* mod.info 需要debug的数据
* mod.require 前置服务
* mod.init 此服务的初始化函数

## Console

可以用于启动服务：
* 启动snax服务：snax name
* 普通服务：name

## DebugConsole

启动：
```
skynet.newservice("debug_console", 8000)
```

登陆：

在~/.bashrc中增加：
```bash
#rlwrap 增加上下键编辑历史功能
alias nc='rlwrap nc'
```

nc localhost 8000

* help

针对所有服务的指令：
* list 列出所有服务
* gc 强制让所有服务都执行一次垃圾回收，并报告回收后的内存
* mem 让所有lua服务汇报自己占用的内存。只能获取lua服务的内存占用情况
* stat 列出所有lua服务的消息队列长度，以及挂起的请求数量，处理的消息总数。如果在Config中设置profile为true，还会报告服务使用的cpu时间
* service 列出所有的唯一lua服务

针对单个服务的指令：服务地址如`:01000001`，以冒号开头的8位16进制数字，或者省略前面两个数字的harbor id，以及接下来的0，比如:01000001可以简写为1。
* exit address 让一个lua服务退出
* kill address 强制终止一个服务
* info address 让一个服务汇报自己的内部信息，数据通过skynet.info_func生成
* signal address sig 向服务发送一个信号，sig默认为0
* task adress 显示一个服务中所有被挂起的请求的调用栈
* debug address
    - ... 当前消息
    - watch("lua", function(_,_,cmd) return cmd == "get" end)
    - watch("lua")
    - lua表达式以及lua指令（输入的语句会放在当前位置执行）
    - c 继续处理这条消息。离开关注状态
    - n 单步运行一行，如果是函数调用，会跟踪进去
    - s 单步运行一行，如果是函数调用，不会跟踪进去
    - debug.traceback(_CO)
    - cont
* logon/logoff address 记录一个服务所有的输入消息到文件。需要在Config里配置logpath
* inject address script 将script名字对应的脚本插入指定的服务中运行（通常可用于热更新补丁）
* call address 调用一个服务的lua类型接口，格式为call address "foo",arg1,...
    - 接口名和string类型的参数必须加引号，且以逗号隔开
    - `call 18 "start",1`




## 问题
skynet服务和snax服务，如何选择？

是否可以直接通过debug_console向snax服务发送消息？
    - 间接起一个普通服务，然后通过向普通服务发消息的方法可以实现向snax服务发消息。但是是否有直接发消息的方法呢
    - 看起来snax并没有注册lua消息处理？

服务间发送消息的类型：是否包括函数？
    - 比如A服务是否可以向B服务发送一个函数？
    - 如果只能发string，是否可以将string加载一下，变成函数来执行？
        - 可以是可以，不过貌似能传递值非常有限。无法传递table。

call 


如果服务没有启动，但是服务里面调用queryservice()去请求它，会一直阻塞。（被坑过）


有匿名服务的地址，如何拿到object。
    - snax.bind(tonumber(address), namestr)


热更snax代码，需要遍历一遍所有的服务？

## 总结
* 启动skynet的时候带上rlwrap可以方便在console中输入指令
    - 启动普通服务直接输入文件名，不含后缀
    - 启动snax服务需要输入：snax name
* 可以通过服务地址获取服务的object
    - snax.bind(address, name) address必须是数字，如果是字符串需要用tonumber转化为数字。name必须是字符串。
* 有两种与运行中的系统交互的形式
    - concole 新建一个脚本，然后在console中运行。更加强大。console中启动服务也可以提供参数。
    - debug_console 用nc练上去

* 可以通过snax的hotfix来检查服务的内部状态
    - 新建一个服务来热更
```lua
local address = "0000000c"
local name = "simroom"
local patch = [[
  local world
  function hotfix(...)
      return world.island_data.players
  end
]]

skynet.start(
	function()
		handler = assert(tonumber("0x" .. address))
		obj = snax.bind(handler, name)
		local r = snax.hotfix(obj, patch)
		tprint(r)
		skynet.exit()
	end
)
```

## 参考文献
* http://gad.qq.com/content/coursedetail?id=467
* http://github.com/cloudwu/skynet
* QQ: 340504014
* skynet-users@googlegroups.com
* https://blog.csdn.net/qq769651718/article/category/7480207
* https://github.com/ximenpo/hello-skynet
 