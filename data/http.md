# HTTP发展简史


## HTTP/0.9
1991年发布0.9版本。只有一个命令GET。只能传HTML格式的字符串。不能回应其他格式。

## HTTP/1.0
1996年发布1.1版本。可以传任何格式。引入了POST、HEAD命令。每次通信都包含头信息。
其他新增功能包括：
* 状态码
* 多字符集支持
* 多部分发送（multi-part）
* 权限（authorization）
* 缓存
* 内容编码（content encoding）

每个TCP连接只能发送一个请求。`Connection: keep-alive`是一个非标准字段。

## HTTP/1.1
1997年发布。

* 持久连接。`Connection: keep-alive`成为标准。
* 管道机制（pipelining）。同一个TCP连接可以发送多个请求。
* 因为有多个请求，所以需要区分数据包属于哪个请求。这就是`Content-Length`的作用。
* 分块传输编码。服务器无需等待所有操作完成才发送数据。
* 新增PUT、PATCH、HEAD、OPTIONS、DELETE方法
* 新增Host字段，指定服务器域名，为虚拟主机的兴起打下基础。

缺点：
* 队头堵塞（Head-of-line blocking）处理完前一个才会进行下一个。优化技巧：
    - 减少请求数，如合并样式、脚本、图片嵌入CSS代码，
    - 多开持久连接，如域名分片
* 头部冗余，cookie，user-agent


## SPDY 协议
2009年谷歌开发，主要解决HTTP/1.1效率不高的问题。成为HTTP/2的基础。
* multiplexed streamings
* request prioritization
* http header compression
* server push / server hint

## HTTP/2
2015年发布。没有子版本。下一版本将是HTTP/3。

* 二进制协议。头信息和数据体都是二进制。统称为帧（frame）：头信息帧和数据帧。
* 多工（Multiplexing）。复用TCP连接。不用按顺序一一对应。
* 数据流。客户端发出的数据流，ID一律为奇数，服务器发出的，ID为偶数。
* 可以取消数据流。可以指定数据流的优先级。
* 头信息压缩。HPACK
* 服务器推送。PUSH


## 参考文献
* https://http2.github.io/
* Hypertext Transfer Protocol version 2 RFC7540
* HPACK - Header Compression for HTTP/2 RFC7541
