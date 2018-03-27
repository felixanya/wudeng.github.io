# Etcd

配置共享
服务发现

Zookeeper
doozer

基于Raft算法

    
etcd: A highly-available key value store for shared configuration and service discovery. 

    高可用
    强一致性
    服务发现存储仓库
    
    控制数据
    应用数据（数据量小，更新访问频繁）
    
    Service Discovery
    
    2379 http api
    2380 peer 3,5,7
    
    heartbeat 100ms
    election 1000ms
    
    http://cizixs.com/2016/08/02/intro-to-etcd
    

        raft实现
        WAL write ahead log预写式log，日志存储: wal file / snapshot
        数据存储和索引
    
一致性
存储
watch
key过期

Raft 强一致性算法
    Distributed Consensus
    Leader Election
    Log Replication
http://thesecretlivesofdata.com/raft/


```
docker network create etcd --subnet 172.19.0.0/16

docker run -d --name etcd0 --network etcd --ip 172.19.1.10 quay.io/coreos/etcd etcd \
-name etcd0 \
-advertise-client-urls http://172.19.1.10:2379,http://172.19.1.10:4001 \
-listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
-initial-advertise-peer-urls http://172.19.1.10:2380 \
-listen-peer-urls http://0.0.0.0:2380 \
-initial-cluster-token etcd-cluster-1 \
-initial-cluster etcd0=http://172.19.1.10:2380,etcd1=http://172.19.1.11:2380,etcd2=http://172.19.1.12:2380 \
-initial-cluster-state new

docker run -d --name etcd1 --network etcd --ip 172.19.1.11 quay.io/coreos/etcd etcd \
-name etcd1 \
-advertise-client-urls http://172.19.1.11:2379,http://172.19.1.11:4001 \
-listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
-initial-advertise-peer-urls http://172.19.1.11:2380 \
-listen-peer-urls http://0.0.0.0:2380 \
-initial-cluster-token etcd-cluster-1 \
-initial-cluster etcd0=http://172.19.1.10:2380,etcd1=http://172.19.1.11:2380,etcd2=http://172.19.1.12:2380 \
-initial-cluster-state new

docker run -d --name etcd2 --network etcd --ip 172.19.1.12 quay.io/coreos/etcd etcd \
-name etcd2 \
-advertise-client-urls http://172.19.1.12:2379,http://172.19.1.12:4001 \
-listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
-initial-advertise-peer-urls http://172.19.1.12:2380 \
-listen-peer-urls http://0.0.0.0:2380 \
-initial-cluster-token etcd-cluster-1 \
-initial-cluster etcd0=http://172.19.1.10:2380,etcd1=http://172.19.1.11:2380,etcd2=http://172.19.1.12:2380 \
-initial-cluster-state new

docker run -it --name client --network etcd quay.io/coreos/etcd sh

# test cluster with etcdctl

#etcdctl --endpoints http://172.19.1.10:2379,http://172.19.1.11:2379,http://172.19.1.12:2379 set /foo bar
#etcdctl --endpoints http://172.19.1.10:2379,http://172.19.1.11:2379,http://172.19.1.12:2379 get /foo

#ETCDCTL_API=3 etcdctl --endpoints http://172.19.1.10:2379,http://172.19.1.11:2379,http://172.19.1.12:2379 put foo bar
#ETCDCTL_API=3 etcdctl --endpoints http://172.19.1.10:2379,http://172.19.1.11:2379,http://172.19.1.12:2379 get foo
```


## 参考文档

* https://gist.github.com/jolestar/6644dee696dcdce432caa46705ddc7ba
* http://www.infoq.com/cn/articles/etcd-interpretation-application-scenario-implement-principle
