# LVS - Linux Virtual Server
IP负载均衡
基于内容请求分发
组成部分
    负载调度器load balancer / Director
    服务器池server pool / Realserver
    共享存储shared storage
    
LVS = ipvs + ipvsadm

ipvs是核心，工作于内核空间。
ipvsadm定义规则，用户空间。ipvsadm与ipvs的关系就好比iptables和netfilter的关系。


ipvsadm
    -A 添加一条虚拟服务器记录，即创建一个LVS集群。
    -t VIP:port  tcp
    -s Scheduling
        rr - 轮询 round robin
        wrr - 加权轮询 weight rr
        lc - 最少链接
        wlc - 加权最少链接
        lblc - 基于局部性的最少链接调度算法
        lblcr4
        dh - 目标地址散列调度
        sh - 源地址散列
        sed
        nq
    -a 添加一个RealServer到集群中
    -r 要添加的RealServer的IP地址
    -m 工作模式为LVS-NAT



## LVS负载均衡方式

### Virtual Server via Network Address Translation (VS/NAT)

Realserver的网关指向Director

Director上：
```
yum install -y ipvsadm
echo <<EOF
#!/bin/bash
# director服务器上开启路由转发功能:
echo 1 > /proc/sys/net/ipv4/ip_forward
# 关闭 icmp 的重定向
echo 0 > /proc/sys/net/ipv4/conf/all/send_redirects
echo 0 > /proc/sys/net/ipv4/conf/default/send_redirects
echo 0 > /proc/sys/net/ipv4/conf/eth0/send_redirects
echo 0 > /proc/sys/net/ipv4/conf/eth1/send_redirects
# director设置 nat 防火墙
iptables -t nat -F
iptables -t nat -X
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j MASQUERADE
# director设置 ipvsadm
IPVSADM='/sbin/ipvsadm'
$IPVSADM -C
$IPVSADM -A -t 172.16.254.200:80 -s wrr
$IPVSADM -a -t 172.16.254.200:80 -r 192.168.0.18:80 -m -w 1
$IPVSADM -a -t 172.16.254.200:80 -r 192.168.0.28:80 -m -w 1
EOF > /user/local/sbin/lvs_nat.sh

/bin/bash /user/local/sbin/lvs_nat.sh
## 查看配置的规则
ipvsadm -ln
ipvsadm -Ln
```

service ipvsadm save
/etc/sysconfig/ipvsadm
service ipvsadm reload


ipvsadm -S > /testdir/lvsrules
ipvsadm -R < /testdir/lvsrules

### Virtual Server via Ip Tunneling (VS/TUN)


### Virtual Server via Direct Routing (VS/DR)

Director:

```
# vim /usr/local/sbin/lvs_dr.sh
#! /bin/bash
echo 1 > /proc/sys/net/ipv4/ip_forward
ipv=/sbin/ipvsadm
vip=192.168.0.38
rs1=192.168.0.18
rs2=192.168.0.28
ifconfig eth0:0 down
ifconfig eth0:0 $vip broadcast $vip netmask 255.255.255.255 up
route add -host $vip dev eth0:0
$ipv -C
$ipv -A -t $vip:80 -s wrr 
$ipv -a -t $vip:80 -r $rs1:80 -g -w 3
$ipv -a -t $vip:80 -r $rs2:80 -g -w 1
```

RS:
```
# vim /usr/local/sbin/lvs_dr_rs.sh
#! /bin/bash
vip=192.168.0.38
ifconfig lo:0 $vip broadcast $vip netmask 255.255.255.255 up
route add -host $vip lo:0
echo "1" >/proc/sys/net/ipv4/conf/lo/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/lo/arp_announce
echo "1" >/proc/sys/net/ipv4/conf/all/arp_ignore
echo "2" >/proc/sys/net/ipv4/conf/all/arp_announce
```

## 健康检查
keepalived

* https://www.cnblogs.com/liwei0526vip/p/6370103.html
* http://www.linuxvirtualserver.org/zh/lvs1.html
