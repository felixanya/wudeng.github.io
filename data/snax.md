# snax

## 配置

用 snax 框架编写的服务的查找路径：
`snax = root.."examples/?.lua;"..root.."test/?.lua"`


范例
test/pingserver.lua

回调函数，参数的类型不受限，而skynet服务只接受字符串参数。
* init()    启动服务
* exit()    服务退出

启动snax服务的三种方式：
* snax.newservice(name, ...)        只能用地址与之通信
    - 可以把一个服务启动多份
    - 传入服务名和参数
    - 返回一个对象用于和这个启动的服务交互  
        - .handle，一个数字，服务地址
        - .type
* snax.uniqservice(name, ...)       可以用名字找到
    - 一个节点只会启动一份同名服务，多次调用返回相同的对象
* snax.globalservice(name, ...)     可以用名字找到
    - 整个skynet网络中只会有一个同名服务

* snax.bind(handle, typename) 转换成服务对象
    - typename 服务的启动名，以用来了解这个服务有哪些远程方法可以供调用

* snax.queryservice(name) 查询当前节点的具名服务，如果服务尚未启动，阻塞等待它启动完毕
* snax.queryglobal(name) 查询一个全局名字的服务，返回一个服务对象，阻塞等待它启动完毕
* snax.kill(obj, ...)
* snax.self()   等价于snax.bind(skynet.self(), SERVER_NAME)
* snax.exit(...) 等价于 snax.kill(snax.self(), ...)


## RPC调用

分为有响应和无响应。

阻塞调用：
* function response.foobar(...) response前缀表示这个方法一定有一个回应。可以通过函数返回值来回应远程调用。

调用方法：
obj.req.foobar(...) 会阻塞
    - obj 表示服务对象
    - req 表示这个调用需要接收返回值
    - foobar 是方法的名字

非阻塞调用
* function accept.foorbar(...) 不需要返回值

调用方法：
obj.post.foobar(...) 不会阻塞
    - 不适用于Cluster模式。只能用于本地消息或master/slave结构下的消息


## 热更新
支持热更新。

snax.hotfix(obj, patchcode)

patchcode中可以包含一个function hotfix(...)函数。在patch提交后立刻执行。这个函数可以用来查看或修改snax服务的线上状态。
hotfix的函数返回值会传递给snax.hotfix的调用者。

不可以在patch中增加新的远程方法。

