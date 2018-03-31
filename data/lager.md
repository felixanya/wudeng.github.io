# lager

老版本的lager所有log都是通过call同一个进程lager_event来完成的。
这意味着如果太多进程打印log，可能会因为这个进程处理不过来而阻塞。这种情况以前线上遇到过。
修仙物语第一次测试的时候因为打开了debug级别的log，结果log处理不过来造成进程阻塞。

新版本的lager支持多个sink，并且支持异步和同步切换。


debug -> 7;
info -> 6;
notice -> 5;
warning -> 4;
error -> 3;
critical -> 2;
alert -> 1;
emergency -> 0;

none是一个特殊的级别。默认不打印log。只有满足filter的时候才会打印log。

{formatter, lager_default_formatter}
{formatter_config, [time, " [", severity, "] ", message, "\n"]}


lager_default_formatter:semi-iolist
* 传统的iolist直接打印
* atom: placeholders for lager metadata, and extracted from log message
    * date/time/message/sev/severity 这些都是一致存在的
    * pid/file/line/module/function/node 这些需要parse_transform
* {atom(), semi-iolist()} fallback for placeholder
* {atom(), semi-iolist(), semi-iolist()} conditional operator


[{pid, Pid}, {module, Module}, {line, Line}, {function, Function}]


```erlang
gen_event:sync_notify(Pid, {log, lager_msg:new(Msg, Timestamp,Severity, Metadata, Destinations)});
```


## 启动过程

lager_sup启动三个进程：
* 一个gen_event管理进程并注册为lager_event
* lager_handler_watcher_sup，这个进程也是一个监控进程，负责监控lager_handler_watcher进程。
* 根据配置决定是否启动lager_crash_log

loglevel 保存全局的基本配置：包括所有handler的最低的log级别，以及各个handler的过滤器。
过滤器基于写log时传过来的描述数据，主要是进程Pid，模块名Module，所在行数Line等。

lager主要提供两种handler，一个是基于console，一个是基于file的。
通过读取配置，启动watcher进程，将handler加入lager_event事件管理器，如果失败会隔5秒重试。
* lager_console_backend 只有一个目标，所以只启动一个watcher
* lager_file_backend 有多个目标，会启动多个watcher

watcher进程负责将handler装入事件管理器。为事件管理器添加Handler的时候有两种方式：
* add_handler(EventMgrRef, Handler, Args)
* add_sup_handler(EventMgrRef, Handler, Args)

Handler其实是一个回调模块。事件管理器收到添加请求以后会调用Handler:init(Args)的方式来初始化Handler.

add_sup_handler这种方式调用进程会监控handler的连接。调用进程退出时，事件管理器会收到消息，
反之，事件管理器删掉handler的时候，也会给调用进程发送消息。

在这里，watcher是通过add_sup_handler来添加handler的。当handler被事件管理器删掉以后，
watcher进程会收到来自事件管理器发出的{gen_event_EXIT, Handler, Reason} 消息，然后watcher会自己退出。


最后根据配置，将error_logger_lager_h 这个handler加入error_handler这个时间管理器。

## TraceFilter

TraceFilter用于可以用于过滤特定模块和进程的log。
* trace_console/1
* trace_console/2
* trace_file/2
* trace_file/3
* stop_trace/1
* clear_all_traces/0

用来向lager_console_backend添加log，前提是lager_console_backend是一个handler。那么问题来了。如果已经是一个handler，为什么还要过滤？
猜测：用来显示一部分而不是全部log。

loglevel: {LevelThreshold, TraceFilters}

TraceFilters: 可以将特定进程和特定模块的log重定向到特定目标。
[{Filter, FilterLevel, Dest}]

Filter: [{Key, Match}]

Match: '\*'


## lager_console_backend

查看handler:
gen_event:which_handlers(lager_event).
gen_event:delete_handler(lager_event, lager_console_backend, []).

添加handler：
gen_event:add_handler(lager_event, lager_console_backend, debug).

## lager_file_backend



* https://github.com/erlang-lager/lager
