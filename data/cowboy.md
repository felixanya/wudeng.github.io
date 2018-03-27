# cowboy源码分析

cowboy是一个强大的http服务器。底层利用ranch提供连接管理。本文基于cowboy1.0.x版本对源码进行分析。

## cowboy

定义各种对外接口。

### 启动http服务：

* start_http/4
* start_https/4
* start_spdy/4

## cowboy_protocol

cowboy使用ranch来管理socket，之前文章中我们分析了ranch的实现，ranch提供连接管理，并且定义了一个数据处理模块的接口，
要利用ranch来实现一个HTTP服务器，第一件事就是实现一个ranch_protocol接口的模块，用来处理数据收发。
在cowboy中，这个模块就是cowboy_protocol。在启动进程并通过ranch:accept_ack确认拿到了socket的控制权以后，
该进程开始接管socket的数据读写，开始进入数据解析。当所有数据解析完毕以后，调用request开始处理请求。
然后构造一个cowboy_req结构体Req，调用onrequest(Req, State)。

如果定义了全局onrequest回调，则先调用全局onrequest回调。这个回调可能发送响应，那么我们可以开始处理下一个请求。
如果没有回调，或者回调没有发送响应，我们继续调用execute函数。

execute主要逻辑就是依次取出中间件，依次调用中间件的回调函数`Middleware:execute(Req, Env)`。
执行完所有中间件接着处理下一个请求。

## cowboy_middleware

定义了中间件接口。
* `execute(Req, Env) -> {ok, Req, Env} | {suspend, Module, Fun, [Args]} | {halt, Req} | {error, Status, Req}`
    * ok 继续处理后面的中间件 
    * suspend 休眠
    * halt 不处理后续的中间件，继续处理后续请求
    * error 终止处理

cowboy定义了两个中间件，分别是cowboy_router处理路由, cowboy_handler处理请求逻辑。
如果用户没有指定中间件，那么这两个中间件就是默认配置的中间件。
中间件必须按照特定的顺序依次执行，比如路由中间件必须在处理中间件前面执行。

## cowboy_router

路由中间件。主要功能是根据host和path获取handler和handler_opts，将其加入Env。
路由与处理模块信息的映射一般在启动服务器的时候提供。

## cowboy_handler

最重要的中间件。需要用到路由中间件提供的handler和handler_opts参数。

* handle_after_callback：如果已经发送响应，设置resp_send标志，然后调用handle_before_loop。

loop循环体：
* handle_before_loop：设置socket选项{active, once}，然后调用handler_loop
* handler_loop：等待消息，来自socket的，或者来自自身定时器，或者定时器超时。
    * 如果超时，调用handle_after_loop 
    * 如果是非socket消息，交给handler的info回调来处理。

## cowboy_http_handler
定义http_handler接口，通常处理的是一次性的请求。
* `init({atom(), http}, Req, opts()) -> {ok, Req, State} | {loop, Req, State} | {loop, Req, State, Timeout} | {shutdown, Req, State} | {upgrade, protocol, Module} | {upgrade, protocol, Module, Req, opts()}`
* `terminate(Reason, Req, State) -> ok`
* `handle(Req, State) -> {ok, Req, State}` 

## cowboy_loop_handler
定义了loop_handler接口，通常处理的是持续性的连接，比如eventsource这种。
init以及terminate接口跟http_handler是一样的，不同的是loop_handler有个info接口。
* `info(Message, Req, State) -> {ok, Req, State} | {loop, Req, State} | {loop, Req, State, hibernate}`

我们cowboy处理请求的时候首先就要实现自己的http_handler接口或者loop_handler接口。

## cowboy_sub_protocol
定义子协议接口，唯一的接口为upgrade，随后逻辑由子协议的实现模块接管。
* `upgrade(Req, State, Handler, HandlerOpts, Module) -> {ok, Req, Env} | {suspend, M, F, A} | {halt, Req} | {error, Status, Req}`

前面定义的两种类型的接口，如果init返回的是upgrade协议，说明我们要用子协议交互，那么我们的handler模块不需要提供handle或者info，而应该提供rest的回调函数。
cowboy中定义了两种子协议，分别是cowboy_rest和cowboy_websocket。



## cowboy_websocket_handler

定义websocket的handler的接口，主要是四个：
* websocket_init/3
* websocket_handle/3
* websocket_info/3
* websocket_terminate/3


## cowboy_rest
实现了cowboy_sub_protocol接口。

有个函数值得一提，expect，我们写erlang代码的时候，经常会遇到各种条件检查，很容易写出缩进很长的代码，
这里的要求也是各种前置检查：
* service_available
* known_methods
* uri_too_long
* allow_methods
* malformed_request
* is_authorized
* valid_content_headers
* known_content_type
* valid_entity_length
* options
* content_types_provided
* languages_provided
* chaset_provieded
* variances
* resource_exists
`expect(Req, State, Callback, Expected, OnTrue, OnFalse)`
- 如果Handler模块导出了Callback函数，那么执行它。
    - Callback函数有三种返回值 
        - {halt, Req, State} 
        - {Expected, Req, State}，执行OnTrue
        - {UnExpeted, Req, State}，执行OnFalse
- 如果没有导出函数，直接执行OnTrue 


如果升级到rest协议，那么我们的handler需要导出的接口包括：
* rest_init(Req, {Name, Path})
* 前面各种前置检查
* 资源操作
    * delete_resource 
    * delete_completed
    * multiple_choices
    * is_conflict
    * content_type_accepted
    * generate_etag
    * ...
* rest_terminate(Req, State)

## cowboy_static

一个实现了cowboy_rest的handler模块，用于处理静态资源的请求。


## cowboy_websocket

实现了cowboy_sub_protocol的接口。


rfc6455
实时通信

节省每次请求的header
服务器可以主动传送数据到客户端


### handshake:

client:
* connection:upgrade
* upgrade:websocket
* sec-websocket-version:7,8,13
* sec-websocket-key
* sec-websocket-extensions
    * x-webkit-deflate-frame 
* origin 
* sec-websocket-protocol

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

server:
* HTTP/1.1 101 Switching Protocols
* connection:upgrade
* upgrade:websocket
* sec-websocket-accept
    * GUID:  258EAFA5-E914-47DA-95CA-C5AB0DC85B11
    * base64(sha1(key+GUID))
* sec-websocket-protocol

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

## cowboy_req

封装请求，提供各种工具功能。

```
#http_req{
    headers = [],
    meta = [{websocket_version, IntVersion},{websocket_compress, true|false}],
}
```



## 参考文档

*　[The WebSocket Protocol](https://tools.ietf.org/html/rfc6455)
