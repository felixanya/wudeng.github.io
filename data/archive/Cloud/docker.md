# Docker

最初基于LXC，0.7以后去除LXC，转而使用自行开发的libcontainer，从1.11开始，进一步演化为runC何containerd。
三个最基本的概念：
* 镜像 Image
    - 文件系统
    - 分层存储
* 容器 Container
    - 不可变，轻量，创建速度快
* 仓库Repository

## 核心技术
namespaces
    进程
    网络
        Host
        Container
        None
        Bridge
            docker0
            brctl show
            iptables -t nat -L
    挂载点
control groups
    隔离物理资源：CPU、内存、磁盘IO、网络带宽
Union filesystem


## docker常用指令

* docker help 获取帮助
* docker info 查看docker信息
* docker search tutorial 搜索镜像
* docker pull ubuntu:13.10 获取镜像
* docker logs [-f] <CONTAINER_ID> 查看stdout输出
* docker top <CONTAINER_ID> 查看容器的正在运行的程序
* docker ps -l 显示最近一个容器
* docker commit 698 learn/ping
* docker images 显示本地镜像
* docker ps 显示运行的容器
    * -a 查看所有容器，不管运行还是没有运行
* docker inspect xxx 显示详情
* docker start/stop/restart 容器启动/停止/重启
* docker run --rm -it learn/ping /bin/bash 运行容器
    * --rm 容器退出时自动删除，不能与-d同时使用
    * --it 交互式启动 --interactive --tty
    * -d   后台运行 --detach
    * -p 自定义端口映射, --publish
        - hostPort:containerPort
        - ip:hostPort:containerPort
        - ip::containerPort
    * -P 随机映射端口
    * -v 数据卷，挂载宿主机的文件系统到docker，需要保存容器运行的数据的时候要用到
    * --volumns-from 从其他容器中挂载数据卷
    * -w 工作目录 --workdir
    * --name 指定容器名字
    * --link 链接其他容器
        - name:alias
        - 环境变量，前缀采用大写的别名
        - /etc/hosts
    * --env 设置环境变量
    * --net=bridge
        - bridge
        - host
        - container:Name_or_ID
        - none
* docker rm <容器ID> 删除容器，需要是停止的容器
    * -f    强制删除，如果还在运行则先停止在删除
* docker rmi <镜像id> 删除镜像
* docker build -t xxx . 从Dockerfile创建镜像
    * -t 镜像名字和标签 --tag name:tag
* docker attach  标准输入输出，多个窗口同步显示，所以推荐用nsenter
    * [.bashrc_docker](https://raw.githubusercontent.com/yeasy/docker_practice/master/_local/.bashrc_docker)
    * docker-pid
    * docker-enter
* docker export 导出容器
* docker import 导入容器
* docker exec -it website /bin/bash
* docker cp 容器和宿主机之间拷贝文件


## Dockerfile

保持Dockerfile所在文件夹没有无关的东西。因为这个文件夹会打包发送到Docker Daemon中。也可以通过.dockerignore文件定义排除在外的文件。
语法跟.gitignore类似。

* 基础镜像信息
    - FROM
    - scratch 特殊镜像，表示空白的镜像，一般用于运行静态编译的go程序
* 维护者信息
    - MAINTAINER
* 镜像操作命令
* 容器启动命令
    - ENTRYPOINT
    - CMD

* ENV
* ADD: 将本地文件加入镜像，支持url，tar解压
* COPY
* RUN
* EXPOSE 暴露端口
* VOLUME
* ONBUILD
    * ADD
    * RUN


supervisorctl



## docker-compose
".yml"

* up
* ps
* stop
* rm


docker create --name maven csphere/maven:3.3.3


docker cp maven:/hello/target/hello.war .


docker run -d -p 3306:3306 --name mysql csphere/mysql:5.5
docker ps -a

关闭所有容器：
docker stop $(docker ps -q)

## 日志系统

elk: 日志系统
elasticsearch
logstash
kibana
logstash-fowwarder

docker run -d --name elk -p 9200:9200 -p 5601:5601 -p 5000:5000 -e ES_MIN_MEM=64m -e ES_MAX_MEM=512m csphere/elk:1.6.0

localhost:5602

docker exec -it elk /bin/bash
    /opt/logstash/bin/logstash -e 'input { stdin { } } output { elasticsearch { host = localhost } }'


`localhost:9200/_search?pretty`


docker run -d --name fwd --link serene_meitner:log.csphere.cn -v /data/logs:/data/logs csphere/logstash-fowwarder:0.4.0

## 网络系统

网络模式：
* Nat, iptables
    * 网络资源隔离
    * 无需手动配置
    * 可访问外网，DNAT
    * 外界无法直接访问容器ip, SNAT
    * 低性能
    * 端口管理麻烦
* Host
    * 共享宿主网络
    * 网络性能无衰减
    * 排查网络故障简单
    * 网络环境无隔离
    * 网络资源无法统计
    * 端口不易管理
* other container
* none
* overlay

Host1, ubuntu:
start docker
docker --version
docker images

docker run -it --name csphere-nat busybox sh
    ifconfig
    route -n
    wget baidu.com
    ctrl+p+q
iptables -t nat -L -n
docker run -it -p 2222:22 --name csphere-nat2 busybox sh
iptables -t nat -L -n


ctrl+l

docker run -it --name csphere-host --net=host busybox sh
    ifconfig
    ctrl+p+q


docker service publish test-bridge.bridge
docker service attach test1 test-bridge.bridge
doccker exec -it test1 sh
    ifconfig
    route -n

docker exec -it test2 sh
    ifconfig
    route -n

swarm scheduler

Filter
    constraint（约束）
    affinity（亲和性）
    port（端口）
    dependency（依赖）
    health（健康）
weigh
    spread（最少）
    binpack（最多）
    random（随机）

## 存储管理

graphdriver
    aufs(ubuntu)
    btrfs(CoreOS)
    devicemappper(redhat, centos)
    overlay(CoreOS)

graph: 镜像的保管者
    json -> 镜像信息
    layersize -> 镜像的大小信息

copy on write

* AUFS: Another Union File System
    * 挂载点 /var/lib/docker/aufs/mnt/$CONTAINER_ID
    * 将不同目录挂载到同一虚拟机挂载点
    * mount -t aufs -o br=/root/dir1=ro:/root/dir2=rw none /root/aufs
* Devicemapper
    * /dev/loop
    * 空间限制，100G，metadata 2GB
* Overlayfs
    * /var/lib/overlay/$CONTAINER_ID/
    * 只有两层
    * 允许页面缓存共享
    * 内核版本3.18+

cat json | python -mjson.tool


modprobe overlayfs
modinfo overlayfs
mount -t overlay overlay -olowerdir=/root/lower,upperdir=/root/upper /root/merged

df -hT
docker pull busybox
ls -alhs
    dd if=/dev/zero of=a.dat bs=2M count=100

Docker volumn
将容器以及容器产生的数据分离开
Dockerfile: VOLUME $CON_PATH
docker run -v $HOST_PATH:$CON_PATH

数据保存：-v
容器间共享数据：--volumns-from

docker run -d --name my-con -v /data/mysql:/var/lib/mysql csphere/mysql:5.5
ls /data/mysql
docker exec -it my-con sh
    mysql
    show database;
    create database mydb;
    exit
    exit
docker rm -f my-con
docker run -d --name my-con2 -v /data/mysql:/var/lib/mysql csphere/mysql:5.5
docker exec -it my-con2 sh
    mysql
    show databases;

docker run -it --name data -v /root/dir1:/root busybox sh
    ls /root/
    ctrl+p,ctrl+q
docker run -it --name data2 --volumns-from data busybox sh
    ls /root


the union file system


## 尚未解决的问题

在vagrant中使用docker，创建容器volumn的时候如果使用vagrant的共享文件夹会报错。显示docker无法读取文件。可能是权限问题。
使用虚拟机内部文件系统没有这个问题。


OCI : Open Container Initative
    [runtime spec](https://github.com/opencontainers/runtime-spec)
        runc
    [image spec](https://github.com/opencontainers/runtime-spec)

* https://hub.docker.com
* http://www.wangminli.com/?p=1179
* https://github.com/moby/moby/issues/15740
* http://dockone.io/
* https://c.163.com/
* https://github.com/docker-library
* https://zhuanlan.zhihu.com/cxwangyi/19902938
* https://csphere.cn/training
* https://draveness.me/docker
