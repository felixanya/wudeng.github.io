# EPMD
Erlang Port Mapper Deamon
监听4369

ps -ef | grep beam


erl_epmd:names().
epmd -names

查看beam打开的文件和端口：
```

[ts@ts ~]$ lsof -p17395
COMMAND    PID USER   FD   TYPE  DEVICE SIZE/OFF     NODE NAME
beam.smp 17395   ts  cwd    DIR   253,0     4096  3671627 /data/server/cross_server/tools
beam.smp 17395   ts  rtd    DIR   253,0     4096        2 /
beam.smp 17395   ts  txt    REG   253,0 13618570 20971632 /usr/local/lib/erlang/lib/erlang/erts-5.10.4/bin/beam.smp
beam.smp 17395   ts  mem    REG   253,0   157072 40632740 /lib64/ld-2.12.so
beam.smp 17395   ts  mem    REG   253,0    22536 40632746 /lib64/libdl-2.12.so
beam.smp 17395   ts  mem    REG   253,0  1926480 40632741 /lib64/libc-2.12.so
beam.smp 17395   ts  mem    REG   253,0   145896 40632745 /lib64/libpthread-2.12.so
beam.smp 17395   ts  mem    REG   253,0    47112 40632755 /lib64/librt-2.12.so
beam.smp 17395   ts  mem    REG   253,0   599392 40632747 /lib64/libm-2.12.so
beam.smp 17395   ts  mem    REG   253,0   142224 40632424 /lib64/libncurses.so.5.7
beam.smp 17395   ts  mem    REG   253,0    17520 40632458 /lib64/libutil-2.12.so
beam.smp 17395   ts  mem    REG   253,0   134792 40632538 /lib64/libtinfo.so.5.7
beam.smp 17395   ts  mem    REG   253,0    16545 20974726 /usr/local/lib/erlang/lib/erlang/lib/crypto-3.2/priv/lib/crypto_callback.so
beam.smp 17395   ts  mem    REG   253,0  1614556 20974724 /usr/local/lib/erlang/lib/erlang/lib/crypto-3.2/priv/lib/crypto.so
beam.smp 17395   ts    0r   CHR     1,3      0t0     3857 /dev/null
beam.smp 17395   ts    1w   CHR     1,3      0t0     3857 /dev/null
beam.smp 17395   ts    2w   CHR     1,3      0t0     3857 /dev/null
beam.smp 17395   ts    3u   REG     0,9        0     3853 [eventpoll]
beam.smp 17395   ts    4r  FIFO     0,8      0t0  1013779 pipe
beam.smp 17395   ts    5w  FIFO     0,8      0t0  1013779 pipe
beam.smp 17395   ts    6r  FIFO     0,8      0t0  1013780 pipe
beam.smp 17395   ts    7w  FIFO     0,8      0t0  1013780 pipe
beam.smp 17395   ts    8u  IPv4 1013788      0t0      TCP *:48832 (LISTEN)
beam.smp 17395   ts    9u  IPv4 1013790      0t0      TCP localhost:37799->localhost:epmd (ESTABLISHED)
beam.smp 17395   ts   10w   REG   253,0      121  3672117 /data/server/cross_server/log/crash.log.0
beam.smp 17395   ts   11w   REG   253,0      113  3672016 /data/server/cross_server/log/error.log
beam.smp 17395   ts   12w   REG   253,0    60794  3671033 /data/server/cross_server/log/info.log
beam.smp 17395   ts   13w   REG   253,0    60794  3671007 /data/server/cross_server/log/console.log
beam.smp 17395   ts   14r  FIFO     0,8      0t0  1023715 pipe
beam.smp 17395   ts   15u  IPv4 1013804      0t0      TCP 192.168.4.168:48832->192.168.4.168:37409 (ESTABLISHED)
beam.smp 17395   ts   16u  IPv4 1051313      0t0      TCP 192.168.4.168:48832->192.168.4.135:51710 (ESTABLISHED)
beam.smp 17395   ts   17u  IPv4 1050335      0t0      TCP 192.168.4.168:35163->192.168.4.116:63645 (ESTABLISHED)
beam.smp 17395   ts   18u  IPv4 1054566      0t0      TCP 192.168.4.168:48832->192.168.4.122:50880 (ESTABLISHED)
beam.smp 17395   ts   19u  IPv4 1054838      0t0      TCP 192.168.4.168:48832->192.168.4.246:65311 (ESTABLISHED)
beam.smp 17395   ts   20u  IPv4 1056297      0t0      TCP 192.168.4.168:48832->192.168.4.136:50646 (ESTABLISHED)
beam.smp 17395   ts   21w  FIFO     0,8      0t0  1023716 pipe
beam.smp 17395   ts   22u  IPv4 1056599      0t0      TCP 192.168.4.168:48832->192.168.4.168:59150 (ESTABLISHED)
```

/etc/syslog.conf
```
!epmd
*.*<TABs>/var/log/epmd.log
```


源码

https://github.com/erlang/epmd
