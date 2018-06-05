# 工具

* [HTTPie](https://httpie.org/) http命令行客户端，比curl更好用
* [mitmproxy](https://mitmproxy.org/) HTTPS proxy，可以拦截http请求
* SecureCRT
    - emulation选择linux。选ansi的时候发现tig显示无法刷新。

## tar
* tar cvzf result.tar.gz -C /path/to/dir1/ . -C /path/to/dir2/ .

## linux
* tcpdump
    * tcpdump -i eth0 port 80
* lsof
* nc
    * nc ip port
* strace
    * -c，统计每个系统调用执行时间，执行次数和出错次数。
    * -f，跟踪由fork调用生成的子进程。
    * -t，在输出的每一行信息前加上时间信息。
    * -e，指定一个表达式，用来控制如何跟踪系统调用
        * [qualifier=] [!] value1[,value2]...
        * trace/abbrev/verbose/raw/signal/read/write
        * -e trace=open,close,read,write，只跟踪这四种系统调用
        * -e trace=file，只跟踪与文件操作相关的系统调用
        * -e trace=process，进程控制相关
        * -e trace=network
        * -e trace=signal
        * -e trace=ipc
        * -e signal=!SIGIO
        * -e read=3,5
    * -o，将strace的输出写入指定文件。
* netstat
    * netstat -nltp
    * 可以打印本地网卡接口上的全部连接、路由表信息(-r)、网卡接口信息(-i)，主要利用第一个。
    * -n，使用ip地址表示主机而不是主机名；使用数字表示端口而不是服务名称。
    * -a，显示结果中也包含监听socket。
    * -t，仅显示tcp连接。
    * -r，显示路由信息。
    * -i，显示网卡接口的数据流量。
    * -c，每隔1秒输出一次。
    * -p，显示socket所属进程的PID和名字。
* vmstat
    * 可以实时输出系统的各种资源的使用情况，比如进程信息、内存使用、CPU使用率以及IO使用情况。
    * -f，显示系统自启动以来执行的fork次数。
    * -s，显示内存相关的统计信息以及多种系统活动的数量（比如CPU上下文切换次数）
    * -d，显示磁盘相关的统计信息。
    * -p，显示磁盘分区的统计信息。
* ifstat
    * interface statistics
* mpstat
    * multi-processor statistics
* man.linuxde.net

# network

* curl ifconfig.me
    -i 显示头部信息
    -d BODY
    -F "name=joe" 表单
    -F "avatar=@/path/to/avatar.png" 传文件
* ping.pe
