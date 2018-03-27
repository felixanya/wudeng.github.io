# system performance

## 观测工具

要么基于计数器，要么基于跟踪。

系统级别的计数器：
* vmstat 虚拟内存和物理内存的统计，系统级别
* mpstat 每个CPU的使用情况
* iostat 每个磁盘IO的使用情况，由块设备接口报告
* netstat 网络接口的统计，TCP/IP栈的统计，以及每个连接的一些统计信息
* sar 各种各样的统计，能归档历史数据

进程级别计数器：
* ps 进程状态，显示进程的各种统计信息，包括内存和CPU使用
* top 按照一个统计顺序排序，显示排名高的进程
* pmap 将进程的内存段和使用统计一起列出

一般来说，上述这些工具是从/proc文件系统里读取统计信息的。

基于跟踪，有开销，默认不启动

* tcpdump 网络包跟踪
* blktrace 块IO跟踪
* DTrace 跟踪内核的内部活动和所有资源的使用情况，支持静态和动态的跟踪
* SystemTap 同上
* perf Linux性能事件，跟踪静态和动态的指针


进程级别：

* strace 系统调用跟踪
* gdb: 源码级别的调试器


## The USE Method

For every resource, check utilization, saturation, and errors.

定义：
* resource: all physical server functional components (CPUs, disks, busses, ...) [1]
* utilization: the average time that the resource was busy servicing work [2]
* saturation: the degree to which the resource has extra work which it can't service, often queued
* errors: the count of error events

Utilization Saturation and Errors (USE)
http://www.brendangregg.com/USEmethod/use-linux.html


### CPU

Utilization:
system-wide
* `vmstat 1` "us" + "sy" + "st"
* `sar -u` sum fields except "%idle" and "%iowait"
* `dstat -c` sum fields except "idl" and "wai"

per cpu
* `mpstat -P ALL 1` sum fields except "%idle" and "%iowait"
* `sar -P ALL` same as mpstat

per process
* `top` "%CPU"
* `htop` "CPU%"
* `ps -o pcpu`
* `pidstat 1`

per-kernel-thread
* `top`/`htop` K

Saturation:
system-wide:
* `vmstat 1` "r" > CPU count
* `sar -q` "runq-sz" > CPU count
* `dstat -p` "run" > CPU count

per-process:
* `/proc/PID/schedstat` 2nd field (sched_info.run_delay)
* perf sched latency
* dynamic tracing


### 内存
Utilization:
系统级别
* `free -m` "Mem:" (main memory), "Swap:" (virtual memory)
* `vmstat 1` "free" (main memory), "swap" (virtual memory)
* `sar -r` "%memused"
* `dstat -m` "free"
* slabtop -s c

进程级别：
* top / htop "RES" (resident main memory), "VIRT" (virtual memory), "Mem" for system-wide summary

Saturation:
* `vmstat 1` "si"/"so" (swapping)
* `sar -B` "pgscank" + "pgscand" (scanning)
* `sar -W`

进程级别：
* `dmesg | grep killed` OOM killer

### 网络
Utilization
* `sar -n DEV 1` "rxKB/s"/max "txKB/s"/max
* `ip -s link` link指网络设备，-s表示统计数据，RX/TX tput / max bandwidth
* `/proc/net/dev` "bytes" RX/TX tput/max
* `nicstat` "%Util"

Saturation
* `ifconfig` "overruns", "dropped"
* `netstat -s` "segments retransmited"
* `sar -n EDEV` drop and fifo metrics
* `nicstat` "Sat"


### 存储
Utilization
* `iostat -xz 1` "%util"
* `sar -d` "%util"

进程级别
* `iotop`
* `pidstat -d`
* `/proc/PID/sched` "se.statistics.iowait_sum"

Saturation
* `iostat -xnz 1` "avgqu-sz" > 1, or high "await"
* `sar -d`

## 参考文档

* 性能之巅
* http://www.brendangregg.com
