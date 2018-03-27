# tcpdump

tcpdump -i eth0 -nt '(src 192.168.4.116 and dst 192.168.4.168) or (src 192.168.4.168 and dst 192.168.4.116)'

[S] SYN
[P] PSH
[.] ACK



## -D
列出所有接口。有些系统不支持`ifconfig -a`，那这个选项就很有用了。接口名或者编号都可以用在-i选项中。

## -i     
Listen on interface.
指定要监听的网卡接口。`-i any`表示抓取所有网卡接口上的数据包。

## -n -nn
Don’t convert host addresses to names.  This can be used to avoid DNS lookups.
使用IP地址表示主机而不是主机名；使用数字表示端口号，而不是服务名称。


## -t
Don’t print a timestamp on each dump line.
不打印时间戳。

## -c
仅抓取指定数量的数据包。

## -w
将tcpdump的输出以特定的格式定向到某个文件。


## 表达式过滤
man pcap-filter

表达式由一个或多个qualifiers加id构成，id一般为名字或者数字。 qualifiers分为三类
* type类型主要是主机，网络，端口，端口范围
    * *host*
    * net
    * port
    * portrange
* dir方向
    * src 
    * dst
    * *src or dst*
    * src and dst
* proto协议
    * ip
    * tcp
    * udp
    * icmp 
    * ...

逻辑操作符
* and &&
* or ||
* not !

直接用数据包中的部分协议字段来过滤，比如抓取TCP同步报文段：

```
tcpdump 'tcp[13] & 2 != 0'
```
TCP头部第14个字节的第二个位正是同步标记。该命令也可以表示为：
```
tcpdump 'tcp[tcpflags] & tcp-syn != 0'
```



* https://danielmiessler.com/study/tcpdump/
* man tcpdump
* man pcap-filter
