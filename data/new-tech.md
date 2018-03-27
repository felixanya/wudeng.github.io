




## 分布式

zookeeper => etcd (centos 标配, writen by go)

hadoop (java) => YARN
Mesos (c++, twitter) 
Borg(c++,google, 201407开源了go版本kubernetes)
kubernetes(go)


服务注册中心 
    分布式，支持持久化存储
    变更通知
        加入：如何注册？暴露zk接口，其他服务向zk接口注册自己?
        如何检测变化？维持到其他服务的心跳？
            答案： 通过Registrator自动注册: Go编写，针对docker使用的，通过检测容器在线或者停止状态自动注册和去注册服务的工具
            只需要启动Registrator,就能发现本地docker的在线状态，然后通知对应的注册中心
    zookeeper
        统一命名服务
        状态同步服务
        集群管理
        分布式应用配置项管理
    etcd
    consul

服务提供者



服务发现
    客户端发现
    服务端发现


https://github.com/jasonGeng88/blog
