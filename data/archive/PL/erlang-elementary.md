

## Shell
如果启动的时候带上+Bi，参数，则无法通过ctrl+g,q 或者ctrl+break,a退出。

emulator break handler


Werl
* Ctrl+Break(Windows), Ctrl+C(Linux)
```
BREAK: (a)bort (c)ontinue (p)roc info (i)nfo (l)oaded
       (v)ersion (k)ill (D)b-tables (d)istribution
```

* Ctrl+G
```
User switch command
 --> h
  c [nn]            - connect to job
  i [nn]            - interrupt job
  k [nn]            - kill job
  j                 - list all jobs
  s [shell]         - start local shell
  r [node [shell]]  - start remote shell
  q                 - quit erlang
  ? | h             - this message
```

windows下erl重启shell。ctrl+c和ctrl+break都会退出shell。

使用erl -remsh 连接远程节点时，要通过Ctrl+G,q退出。直接q().会关闭远程节点。
使用rebar的attach连接节点的时候，用ctrl+D退出。

## 网络
inet:i(). 查看网络连接。

## 进程
exit(Reason)
结束调用进程。
exit(Pid, Reason)
Pid可以是进程或者Port，向Pid发送一个Reason的exit信号。当Reason为kill时，信号无法被trap。否则，如果Pid的trap_exit为true，会将exit信号转化为普通消息。

When trap_exit is set to true, exit signals arriving to a process are converted to {'EXIT', From, Reason} messages, which can be received as ordinary messages.
If trap_exit is set to false, the process exits if it receives an exit signal other than normal and the exit signal is propagated to its linked processes.
Application processes should normally not trap exits.

trap_exit为true的进程称为系统进程。系统进程异常(非normal)退出的时候，如果启用了sasl，那么会通过error_logger:format/2打印出错日志。
而普通进程没有这个待遇。



| parent | trap_exit | Reason                       | die | call terminte | explain                                                                                 | complain |
|--------|-----------|------------------------------|-----|---------------|-----------------------------------------------------------------------------------------|----------|
| Y      | Y         | normal/shutdown/{shutdown,X} | Y   | Y             | 进程收到{'EXIT', Parent, normal}，调用terminate, exit(Reason)退出，gen_server.erl:385   | N        |
| Y      | Y         | abnormal                     | Y   | Y             | 进程收到{'EXIT', Parent, abnormal}，调用terminate, exit(Reason)退出，gen_server.erl:385 | Y        |
| Y/N    | Y/N       | kill                         | Y   | N             | kill信号无法被trap，直接exit(killed)退出，不会调用terminate                             | N        |
| Y/N    | N         | normal                       | N   | N             | 对于normal的exit信号无响应                                                              |          |
| Y/N    | N         | abnormal/shutdown            | Y   | N             | 对于非normal的exit信号，直接退出，不会调用termiante                                     | N        |


结论：
总原则：
* 如果导致进程退出的是非normal exit **信号**，那么进程会直接退出，且不会调用terminate函数，并且还会将exit信号向link的进程扩散。而进程对normal信号无反应。
* 如果导致进程退出的是普通 **消息** 如{'EXIT',Parent,Reason}，那么会调用terminate函数然后退出。
* trap_exit能将非kill信号转化为消息。

分情况讨论：
* 如果trap_exit为true，那么进程会将收到的除kill外的所有exit信号转化为{'EXIT', From, Reason}，
然后按照正常的消息处理流程来处理。如果From是父进程，那么将会导致进程调用terminate并exit(Reason)，最终退出进程。
而kill信号无法被trap，所以进程无条件退出，并且不会调用termiante。
* 如果trap_exit为false，那么进程收到除normal外的所有exit信号都会直接退出进程，并且不会调用terminate。

> Shutdown defines how a child process should be terminated.
> brutal_kill means the child process will be unconditionally terminated using exit(Child,kill).
> An integer timeout value means that the supervisor will tell the child process to terminate by calling exit(Child,shutdown) and then wait for an exit signal with reason shutdown back from the child process.
> If no exit signal is received within the specified number of milliseconds, the child process is unconditionally terminated using exit(Child,kill).

推论：
* trap_exit=false, 收到的非normal的exit信号不管来自哪里，都会退出。
* 监控树下面的进程，如果需要调用terminate收尾的（不是所有进程都需要的）：
    - 必须将trap_exit设为true，否则，监控树关闭的时候，向其发送shutdown信号，会直接退出而不会调用terminate。
    - 监控树不能是brutal_kill, 而应该是一个整数的超时时间
* 如果监控树采用了brutal_kill的退出策略，那么当子进程没有按时退出，就会发kill消息到子进程，导致子进程无条件退出，并且不会调用terminate，对于需要terminate来实现存档的进程是相当危险的。


观察到的一些现象：
子进程trap_exit为false：
    父进程向子进程发normal的exit信号，没有任何响应。
    父进程向子进程发shutdown的exit信号，子进程直接退出，没有调用terminate。

子进程的trap_exit为true：
    父进程向子进程发normal的exit信号，子进程退出，并不会处理EXIT消息，但是会调用terminate。父进程也会受到EXIT信号。

注意exit，以及进程link产生的是信号，如果接收进程trap_exit，则会转化为消息。

> The default behaviour when a process receives an exit signal with an exit reason other than normal,
> is to terminate and in turn emit exit signals with the same exit reason to its linked processes.
> An exit signal with reason normal is ignored.

为什么收到exit normal不应该退出呢？
因为link的进程死亡会发exit信号，正常退出的进程本来就不应该把link在一起的进程拖下水。


一些结论：
* 信号和消息两种机制
* 如果进程设置了trap_exit，除了kill信号的其他信号都会被转化为消息
* kill信号不会被转为消息，trap_exit也不行。
* 进程收到父进程过来的转化的 **消息**，会调用terminate函数退出，所以父进程发出的normal信号如果被转成了消息，会导致子进程退出。
* normal信号不会导致进程退出，不管来自哪里。除非它来自父进程并且转化成了消息。
* 被信号杀死的进程不会调用terminate
* 进程死后都会向link的进程发信号。被kill信号杀死的进程会被发killed信号出去。


```erlang
Erlang R16B03 (erts-5.10.4) [smp:4:4] [async-threads:10]

Eshell V5.10.4  (abort with ^G)
1> true or ok.
** exception error: bad argument
     in operator  or/2
        called as true or ok
2> true orelse ok.
true
3> true andalso ok.
ok
4> true andalso ok andalso false.
** exception error: bad argument: ok
5> true andalso true andalso ok.
ok
6> 1 / 1.
1.0
7> 1 div 1.
1
8> 11 xor 10.
** exception error: bad argument
     in operator  xor/2
        called as 11 xor 10
9> bnot 2#10.
-3
10> (0 == 0) or (1/0 > 2).
** exception error: an error occurred when evaluating an arithmetic expression
     in operator  '/'/2
        called as 1 / 0
11> (0 == 0) orelse (1/0 > 2).
true
```

rebar3 shell

windows 系统下建立文件 rebar.cmd，内容如下，那么可以就直接在命令中使用rebar了。
```cmd
@echo off
setlocal
set rebarscript=%~f0
escript.exe "%rebarscript:.cmd=%" %*
```

## process_flag(message_queue_data, off_heap, on_heap)
- off_heap All messages in the message queue will be stored outside of the process heap. This implies that no messages in the message queue will be part of a garbage collection of the process.
- on_heap All messages in the message queue will eventually be placed on heap. They can however temporarily be stored off heap. This is how messages always have been stored up until ERTS 8.0.

## release

rel文件描述，生成script文件和boot文件，boot文件是二进制模式，用来启动节点。
目标系统：能够被其他机器启动的Erlang系统。

systools => reltool(rebar) => relx(rebar3)

application的集合。

### systools

依赖`erlcount-1.0.rel`文件，which applications are in the release, used to generate boot script and release packages.

```
{release, {"erlcount", "1.0.0"},
    {erts, "5.8.4"},
    [
        {kernel, "2.14.4"},
        {stdlib, "1.17.4"},
        {ppool, "1.0.0", permanent},
        {erlcount, "1.0.0", transient}
    ]
}.
```
在erl中生成release相关的文件，如script文件，boot文件，tar包。

systool:make_script("ch_rel-1", [local]).
systool:make_tar
systool:make_relup

ch_rel-1.script
ch_rel-1.boot


erl -boot ch_rel-1

.app
.rel

.appup  => .relup

SASL

rel
systool
script


## reltool

erlcount-1.0.config
```
{sys, [
    {lib_dirs, ["/home/ferd/code/learn-you-some-erlang/release/"]},
    %%{erts, [{vsn, "5.8.3"}]},
    {rel, "erlcount", "1.0.0", [
        kernel,
        stdlib,
        {ppool, permanent},
        {erlcount, transient}
    ]},
    {boot_rel, "erlcount"},
    {relocatable, true},
    {profile, standalone},
    {app, ppool, [{vsn, "1.0.0"},
        {app_file, all},
        {debug_info, keep}]
    },
    {app, erlcount, [{vsn, "1.0.0"},
        {incl_cond, include},
        {app_file, strip},
        {debug_info, strip}]
    }
]}.
```

{ok, Conf} = file:consult("erlcount-1.0.config").
{ok, Spec} = reltool:get_target_spec(Conf).
reltool:eval_target_spec(Spec, code:root_dir(), "rel").


### relx

relx.config

* lib_dirs
* paths: additional code path that points to erlang beam files
* release
* {dev_mode, true}
* {vm_args, "config/vm.args"}
* {sys_config, "config/sys.config"}
* RELX_REPLACE_OS_VARS=true

```
#!/bin/bash

export RELX_REPLACE_OS_VARS=true

for i in `seq 1 10`;
do
    NODE_NAME=node_$i PORT=808$i _build/default/rel/<release>/bin/<release> foreground &
    sleep 1
done
```



## relup

appup
relup

erl -init_debug -boot start_sasl

boot script: .script -> .boot，默认是start.boot
* instructions on which code to load
* which processes and application to start

## 文件编码
erlang源文件的编码从OTP 17.0开始由Latin-1变为UTF-8.

`%% coding: utf-8`

`%% -*- coding: latin-1 -*-`

```
for i in `find . -name "*.erl"`; do echo $i; sed -i '1i \
%% -*- coding: latin-1 -*-
' $i; done
```

random -> rand
crypto:random_bytes() -> crypto:strong_random_bytes(16)

查看当前系统编译好的版本：
kerl list builds
更新最新的版本信息：
kerl update releases
获取版本信息：
kerl list release

kerl build 20.2
查看当前状态：kerl status
安装：kerl install 20.2 /home/ts/otp/20.2

. /home/ts/otp/20.2/activate
kerl_deactivate

## process flag

进程调度有两种，抢占式调度(preemptive)，协作式调度(cooperative)。在抢占式调度中，操作系统负责
将cpu时间分配给等待执行的进程，进程被动的失去cpu。而在协作式调度中，进程通过yield主动放弃cpu。

优先级反转

优先级反转是在高优级(假设为A)的任务要访问一个被低优先级任务(假设为C)占有的资源时,被阻塞.
而此时又有优先级高于占有资源的任务(C)而低于被阻塞的任务(A)的优先级的任务(假设为B)时,
于是,占有资源的任务就被挂起(占有的资源仍为它占有),因为占有资源的任务优先级很低,
所以,它可能一直被另外的任务挂起.而它占有的资源也就一直不能释放,这样,
引起任务A一直没办法执行.而比它优先低的任务却可以执行.

解决方案 ( 优先级继承 / 优先级天花板 )

目前解决优先级反转有许多种方法。其中普遍使用的有2种方法：
一种被称作优先级继承(priority inheritance)；
另一种被称作优先级极限(priority ceilings)。

- 优先级继承(priority inheritance)
优先级继承是指将低优先级任务的优先级提升到等待它所占有的资源的最高优先级任务的优先级.
当高优先级任务由于等待资源而被阻塞时,此时资源的拥有者的优先级将会自动被提升.

 - 优先级天花板(priority ceilings)
 优先级天花板是指将申请某资源的任务的优先级提升到可能访问该资源的所有任务中最高优先级任务的优先级.
 (这个优先级称为该资源的优先级天花板)

优先级继承,只有当占有资源的低优先级的任务被阻塞时,才会提高占有资源任务的优先级,
而优先级天花板,不论是否发生阻塞,都提升.

priority：round robin
    - max (内部使用)
    - high ()
    - normal (默认)
    - low

normal和low的执行交错进行，low的执行频率比较低。当有high的进程等待执行时，会优先high的执行。

system_monitor https://github.com/basho/riak_sysmon
- busy_port
- busy_dist_port
- {long_gc, integer() > 0}
- {long_schedule, integer() > 0}
- {large_heap, integer() > 0}

## ets
ets:match
ets:select 这两个函数相对于 ets:foldl效率更高。因为不需要把整个ets表拷贝到进程中。

## 参考文献

* http://blog.csdn.net/zhongruixian
* https://github.com/erlware/relx/wiki
