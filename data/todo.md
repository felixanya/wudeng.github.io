

open.itcast.cn

程序员的自我修养
thewaytogo
UNP：unix Network Programming




mongodb



coreos

etcd / zookeeper / consul

etcd: A highly-available key value store for shared configuration and service discovery.

kubernetes

mesos


mqtt比xmpp轻, 更适合推送


    mqtt：message queuing telemetry transport
        IBM
        轻量级pub/sub
        开放协议，支持广泛
        二进制
        扩展方便
    xmpp:
        网络流量大，70%消耗在XML标签传输
        xml编解码重，纯文本


xmpp是xml格式
gcm推送: google cloud messaging
fcm: firebase cloud messaging


海量用户接入：负载均衡
    负载均衡 LVS (Linux Virtual Server)
        单点失效
        单点性能瓶颈
    负载均衡从客户端开始做
    域名负载均衡的问题
        域名系统不可靠,劫持
        更新延迟大
    静态分片 (Uid % N)
        单点失效
        后期维护成本高
    动态分配接入点
        - Ticket Service
        - 前端服务器
前端服务器设计
    维护客户端长连接
    同步缓存请求，异步处理请求
    Frontend,Erlang/OTP -> RabbitMQ(排队)
Why Erlang
    优点
        Process模型适合处理高并发
        管理方便：escript
        开发周期比用C短
    缺点
        CPU load
        Memory footage
    下一步：c四核单机维持350万长连接
主逻辑
    读取缓存的请求
    与具体功能模块交互处理请求
        Frontend
    事件驱动
        上一个应答驱动下一个请求
    平行扩展
        添加一个实例，处理能力增强
    Node.js
        优点
            异步模型
            广泛模块支持
        缺点
            动态语言，代码规模不宜过大
            运算密集场所效率低
        下一步：Scala，GO？
基础服务
    独立Service、集群，上下文无关
    低延迟，高并发
高性能Key/Value
    Couchbase
        Auto failover
        动态加节点
        读写负载均衡
        Auto sharding
云服务、自动化运维
    运维就是要自动化
    持续集成开发实践
    Ansible
        目标机器不需要安装agent
        只依赖ssh
        安装脚本模块化
            方便积累，分享







docker

go

service GameService {
    rpc Stream(steam Game.Frame) returns (stream Game.Frame)
}



https://zhuanlan.zhihu.com/distributed
