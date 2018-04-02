# Erlang Trace

Trace可以在生产系统中追踪local和global函数的调用与返回，消息发送与接收，GC等等各种事件。
这对复杂系统的DEBUG是非常有用的特性。比如你的ETS表里面出现了不该出现的数据，你需要找到是哪个函数插入的。
Trace能够做到这些。

## trace BIF
trace构建于两个最基本的BIF之上。dbg模块是对trace功能的封装，类似的还有redbug，recon_trace等等。
* `erlang:trace(PidPortSpec, How, FlagList) -> integer()`
    - PidPortSpec = pid() | port() | all | processes | ports | existing | existing_processes | existing_ports | new | new_processes | new_ports
    - How = boolean()
    - FlagList = [trace_flag()]
    - trace_flag() = all | send | 'receive' | procs | ports | call | arity | return_to | silent | running | exiting | running_procs | running_ports | garbage_collection | timestamp | cpu_timestamp | monotonic_timestamp | strict_monotonic_timestamp | set_on_spawn | set_on_first_spawn | set_on_link | set_on_first_link | {tracer, pid() | port()} | {tracer, module(), term()}
* `erlang:trace_pattern(MFA :: send, MatchSpec, FlagList :: []) -> integer() >= 0`
    - FlagList = [global|local|meta|{meta, Pid}|call_count|call_time]
    - `Module = atom() | '_'``
    - `Function = atom() | '_'`
    - `Arity = integer() | '_'`

## 各种消息格式

消息发送接收 send/'receive'：
- {trace, Pid, send, Message, To}
- {trace, Pid, send_to_non_existing_process, Message, To}
- {trace, Pid, 'receive', Message}

进程调度 running：
- {trace, Pid, in, {M, F, Arity}}
- {trace, Pid, out, {M, F, Arity}}

进程相关 procs：
- {trace, Pid, spawn | spawned, Pid2, {M, F, A}}
- {trace, Pid, exit, Reason}
- {trace, Pid, link | unlink , Pid2}
- {trace, Pid, getting_lined, getting_unlinked, Pid2}
- {trace, Pid, register | unregister, Pid2}

内存回收 garbage_collection：
- {trace, Pid, gc_start | gc_major_start, Info}
- {trace, Pid, gc_end | gc_major_end, Info}

函数执行 call/return_to，return_to只能跟call一起出现，函数执行取决于两个部分的 **交集**：
- 使用trace/3，定义trace哪些进程：all/existing/new
- 使用trace_pattern/3，定义trace哪些模块、函数。**模块必须在调用trace_pattern之前就加载好，
重新编译模块或者重新加载模块会清掉trace pattern，这时需要重新执行trace_pattern启用trace。**

消息格式：
- {trace, Pid, call, {M, F, Args}} 配合arity，后面的部分为{M, F, Arity}
- {trace, Pid, return_to, {M, F, Args}}



## dbg：创建tracer

tracer有两种，一种是process，一种是port。默认的tracer是个process。
`dbg:tracer(process, {Hanlder, HandlerData})`
Hanlder是一个函数，接收两个参数，一个Msg，一个HandlerData，返回更新后的HandlerData。
Handler = fun((Msg, AccIn) -> AccOut end)
类似lists:foldl(Fun, Acc0, List)里面的Fun函数，只不过Fun处理的是List里面的元素，
而Handler处理的是process收到的trace消息。trace消息有五种格式：
- end_of_trace
- trace Tuple
- trace_ts Tuple
- {drop, Msg}
- seq_trace Tuple

tracer是用来接收trace消息的进程或者端口。一般情况下，要使用trace功能，
第一步就是创建一个用于接收trace消息的进程，有两种方式创建tracer：
* 默认的tracer：`dbg:tracer()`
* 自定义的tracer：`dbg:tracer(process, {Handler, HandlerData})`
注意，这两个函数返回的是创建的dbg进程，并不直接是tracer进程。
创建出来的tracer进程具有最高优先级。默认的tracer没有自主退出机制，需要手动退出。很容易搞垮系统。
所以最好是自己定制tracer，保证tracer只会处理特定数量的消息，到达消息上限的时候主动退出。

tracer进程是通过dbg进程以link的方式来创建的，必须依赖父进程dbg的存在而存在。如果dbg退出了，tracer也就退出了。
tracer进程有两个状态，一个是loop，一个是done。

创建tracer的过程：创建一个进程，注册为dbg，等dbg创建好了，通知dbg创建tracer进程。dbg将tracer放到进程字典中，
以当前node为key。

## dbg：指定trace的进程

指定需要trace的进程：
dbg:p(Item, c)

Item可以是单个进程：
- pid()
- atom  whereis(atom)
- Integer <0,Integer,0>
- "<X.Y.Z>"
- {X, Y, Z}

也可以是进程的集合：
- all

Flags可以是单个atom，也可以是list：
* s (send)
* r (receive)
* m (messages)
* c (call)
* p (procs)
* ports

## dbg：指定trace的pattern

匹配模块和函数调用：
* `dbg:tp({Module, Function, Arity}, MatchSpec)` global call
* `dbg:tpl({Module, Function, Arity}, MatchSpec)` local call + global call

## MatchSpec

MatchSpec强化了MFA的约束，增加了对参数的限制，能够更加有效的定位到目标函数，在实际工作中用途很广泛。
比如前面提到的定位ets错误数据是被哪个函数插入的：

```erlang
%% 定位ets:insert(Table, {error, unknown_msg}})
MatchSpec = dbg:fun2ms(fun([_,{_, {error, unknown_msg}}]) -> message(caller()) end),
dbg:tpl(ets, insert, 2, MatchSpec).
```
MatchBody可以是下面这些函数：
- `return_trace()` 函数返回时返回产生一个额外的事件包含函数的返回值。被追踪的函数失去尾递归的特性。
- `exception_trace()`
- `display(Data)` 在shell中打印Data，可以是函数头部的数据，也可以是其他literal fun
- `message(Data)` 产生一个额外的事件，send一个带Data的消息到tracer进程。
- `message(false | true)`
- `enable_trace(TraceFlag)`
- `enable_trace(Pid, TraceFlag)`
- `disable_trace(TraceFlag)`
- `disable_trace(Pid, TraceFlag)`
- `silent(true)`
- `set_tcw(Int)`

## 停止tracer

* dbg:stop()
* dbg:stop_clear()

## trace工具函数

```erlang
%% Item:
%%      all | processes | ports |
%%      new | new_processes | new_ports |
%%      existing | existing_processes | existing_ports |
%%      pid() | atom | {X, Y, Z} | "<X.Y.Z>"
%% Fun:
%%      atom() | '_'
%% Item:
%%      all | processes | ports |
%%      new | new_processes | new_ports |
%%      existing | existing_processes | existing_ports |
%%      pid() | atom | {X, Y, Z} | "<X.Y.Z>"
%% Fun:
%%      atom() | '_'
dbgon(Item, Module, Fun, N) when is_atom(Module),is_integer(N) ->
    MatchSpec = [{'_', [], [{return_trace},{exception_trace}]}],
    dbgon(Item, Module, Fun, MatchSpec, N).

dbgon(Item, Module, Fun, ShellFun, N) when is_atom(Module),is_integer(N),is_function(ShellFun) ->
    MatchSpec = fun_to_ms(ShellFun),
    dbgon(Item, Module, Fun, MatchSpec, N);
dbgon(Item, Module, Fun, MatchSpec, N) when is_atom(Module),is_integer(N),is_list(MatchSpec) ->
    case tracer(N) of
        {ok, _} ->
            dbg:p(Item, call),
            dbg:tpl(Module, Fun, MatchSpec),
            ok;
        Else ->
            Else
    end.

tracer(Total) ->
    dbg:tracer(process, {
        fun
            (_, N) when N >= Total -> dbg:stop_clear();
            (Msg, N) -> io:format("~p~n", [Msg]), N + 1
        end,
        0
    }).

dbgclr() ->
    dbg:stop_clear().

fun_to_ms(ShellFun) when is_function(ShellFun) ->
    case erl_eval:fun_data(ShellFun) of
        {fun_data,ImportList,Clauses} ->
            case ms_transform:transform_from_shell(
                   dbg,Clauses,ImportList) of
                {error,[{_,[{_,_,Code}|_]}|_],_} ->
                    io:format("Error: ~s~n",
                              [ms_transform:format_error(Code)]),
                    {error,transform_error};
                Else ->
                    Else
            end;
        false ->
            exit(shell_funs_only)
    end.
```

## 参考文档
* http://erlang.org/doc/apps/erts/match_spec.html
* erl9.2//lib/runtime_tools-1.12.3/doc/html/dbg.html
* Erlang Programming
