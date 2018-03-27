# iptables


netfilter项目的一部分。

## 语法

`iptables -t 表名 <-A/I/D/R> 规则链名 [规则号] <-i/o 网卡名> -p 协议名 <-s 源IP/源子网> --sport 源端口 <-d 目标IP/目标子网> --dport 目标端口 -j 动作`

-A 添加 -append
-I 插入 -insert
-D 删除 -delete
-R 替换 -replace
-L 显示 -list
-F 清除 -flush
-Z 清空计数器 -zero
-j<目标>：指定要跳转的目标

-s -source [!]address[/mask] 源地址
-d destination [!]address[/mask] 目标地址
-j --jump target

## tables
优先顺序：raw - mangle - nat - filter

PREROUTING: raw -> mangle -> nat
INPUT: mangle -> nat -> filter
OUTPUT: raw -> mangle -> nat -> filter
FORWARD: mangle -> filter
POSTROUTING: mangle -> nat

路径：
- PREROUTING -> INPUT
- PREROUTING -> FORWARD -> POSTROUTING
- OUTPUT -> POSTROUTING



* *filter* 包过滤，用于防火墙规则。
    - INPUT 处理输入数据包
    - FORWARD 处理转发数据包
    - OUTPUT 处理输出数据包
* nat 地址转换用于网关路由器
    - PREROUTING  用于目标地址转换（DNAT）
    - OUTPUT
    - POSTROUTING 用于原地址转换（SNAT）
* mangle 数据包修改（QoS），用于实现服务质量。
    - PREROUTING
    - OUTPUT
    - INPUT
    - FORWARD
    - POSTROUTING
* raw 高级功能，如网址过滤 (数据跟踪处理？)
    - PREROUTING
    - OUTPUT
* security
    - INPUT
    - OUTPUT
    - FORWARD

## 动作
* ACCEPT 接收数据包
* DROP 丢弃数据包
* REDIRECT 重定向、映射、透明代理
* SNAT 原地址转换
* DNAT 目标地址转换
* MASQUERADE IP伪装（NAT）,用于ADSL
* LOG 记录

## 清除已有iptables规则
iptables -F 
iptables -X 
iptables -Z


## 开放指定的端口
iptables -A INPUT -s 127.0.0.1 -d 127.0.0.1 -j ACCEPT #允许本地回环接口(即运行本机访问本机) 
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT #允许已建立的或相关连的通行 
iptables -A OUTPUT -j ACCEPT #允许所有本机向外的访问 
iptables -A INPUT -p tcp --dport 22 -j ACCEPT #允许访问22端口 
iptables -A INPUT -p tcp --dport 80 -j ACCEPT #允许访问80端口 
iptables -A INPUT -p tcp --dport 21 -j ACCEPT #允许ftp服务的21端口 
iptables -A INPUT -p tcp --dport 20 -j ACCEPT #允许FTP服务的20端口 
iptables -A INPUT -j reject #禁止其他未允许的规则访问 
iptables -A FORWARD -j REJECT #禁止其他未允许的规则访问


## 屏蔽ip
iptables -I INPUT -s 123.45.6.7 -j DROP #屏蔽单个IP的命令 
iptables -I INPUT -s 123.0.0.0/8 -j DROP #封整个段即从123.0.0.1到123.255.255.254的命令 
iptables -I INPUT -s 124.45.0.0/16 -j DROP #封IP段即从123.45.0.1到123.45.255.254的命令 
iptables -I INPUT -s 123.45.6.0/24 -j DROP #封IP段即从123.45.6.1到123.45.6.254的命令

## 查看已添加

iptables -L -n -v

* http://man.linuxde.net/iptables
* http://www.zsythink.net/archives/1199
* https://www.cnblogs.com/metoy/p/4320813.html
