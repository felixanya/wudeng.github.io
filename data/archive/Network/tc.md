
tc - show / manipulate traffic control settings

man tc

shaping: egress
scheduling: egress
policing: ingress
dropping: ingress and egress



* qdiscs: queueing discipling
* classes
* filters

classless qdiscs:
* [p|b]fifo, limited in packets or in bytes
* pfifo_fast
* red: random early detection
* sfq: stochastic fairness queueing
* tbf: token bucket filter

classful qdiscs
* cbq: class based queueing
* htb: hierarchy token bucket
* prio

控发不控收


iproute2


tc qdisc add dev eth0 root netem delay 50ms 30ms loss 5%
这句的意思是给网卡eth0加上 50ms的延时，± 30ms 的抖动，在加 5%的丢包率


```
tc qdisc add dev eth0 root netem delay 100ms
tc qdisc change dev eth0 root netem delay 100ms 10ms
```

```
tc qdisc del dev eth0 root netem
```



http://lartc.org
https://www.ibm.com/developerworks/cn/linux/1412_xiehy_tc/index.html
https://wenku.baidu.com/view/f02078db50e2524de5187e45.html
http://www.tldp.org/HOWTO/html_single/Traffic-Control-HOWTO/


https://wiki.linuxfoundation.org/networking/netem

