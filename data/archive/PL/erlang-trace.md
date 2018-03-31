# Erlang Trace


trace哪些进程：all/existing/new
trace哪些模块、函数

tracer有两种，一种是process，一种是port。默认的tracer是个process。
`tracer(process, {Hanlder, HandlerData})`
Hanlder是一个函数，接收两个参数，一个Msg，一个HandlerData，返回更新后的HandlerData。
Handler = fun((Msg, AccIn) -> AccOut end)
类似lists:foldl(Fun, Acc0, List)里面的Fun函数，只不过Fun处理的是List里面的元素，
而Handler处理的是process收到的trace消息。trace消息有五种格式：

- end_of_trace
- trace Tuple
- trace_ts Tuple
- {drop, Msg}
- seq_trace Tuple

## dbg

基础API：

global call
tp({Module, Function, Arity}, MatchSpec)

local call + global call：
tpl({Module, Function, Arity}, MatchSpec)

```
Module = atom() | '_'
Function = atom() | '_'
Arity = integer() | '_'
```


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

第二步：指定需要trace的进程：
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

第三步：指定需要trace的模块和函数：
dbg:tpl(lists, seq, x)

第四步：停止tracer。
dbg:stop()
dbg:stop_clear()


```erlang
%% Item:
%%      all | processes | ports |
%%      new | new_processes | new_ports |
%%      existing | existing_processes | existing_ports |
%%      pid() | atom | {X, Y, Z} | "<X.Y.Z>"
%% Fun:
%%      atom() | '_'
dbgon(Item, Module, Fun, N) when is_atom(Module),is_integer(N) ->
    case tracer(N) of
        {ok, _} ->
            dbg:p(Item, call),
            dbg:tpl(Module, Fun, [{'_', [], [{return_trace},{exception_trace}]}]),
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


dbgon(Module) when is_atom(Module) ->
    dbgon(Module, 100);

dbgon(MF) when is_list(MF) ->
    [M, F] = string:tokens(MF, ":"),
    dbgon(list_to_existing_atom(M), list_to_existing_atom(F)).

dbgon(Module, N) when is_integer(N) ->
    case tracer(N) of
      {ok, _} ->
    	  dbg:p(all, call),
    	  dbg:tpl(Module, [{'_', [], [{return_trace}]}]),
    	  ok;
      Else -> Else
    end;
dbgon(Module, Fun) when is_atom(Fun) ->
    dbgon(Module, Fun, 100);
dbgon(Module, File) when is_list(File) ->
    {ok, _} = dbg:tracer(port, dbg:trace_port(file, File)),
    dbg:p(all, call),
    dbg:tpl(Module,
	    [{'_', [], [{return_trace}, {exception_trace}]}]),
    ok.

dbgon(Module, Fun, File) when is_list(File) ->
    {ok, _} = dbg:tracer(port, dbg:trace_port(file, File)),
    dbg:p(all, call),
    dbg:tpl(Module, Fun,
	    [{'_', [], [{return_trace}, {exception_trace}]}]),
    ok;
dbgon(Module, Fun, N) when is_atom(Fun) ->
    {ok, _} = tracer(N),
    dbg:p(all, call),
    dbg:tpl(Module, Fun,
	    [{'_', [], [{return_trace}, {exception_trace}]}]),
    ok.

dbgon_port(Module, TcpPort) when is_integer(TcpPort) ->
    io:format("Use this command on the node you're "
	      "tracing (-remsh ...)\n"),
    io:format("Use dbg:stop() on target node when done.\n"),
    {ok, _} = dbg:tracer(port, dbg:trace_port(ip, TcpPort)),
    dbg:p(all, call),
    dbg:tpl(Module,
	    [{'_', [], [{return_trace}, {exception_trace}]}]),
    ok.

dbg_ip_trace(TcpPort) ->
    io:format("Run on same machine as target but NOT "
	      "-remsh target-node\n"),
    io:format("Use dbg:stop() when done.\n"),
    dbg:trace_client(ip, TcpPort).

dbgadd(Module) ->
    dbg:tpl(Module,
	    [{'_', [], [{return_trace}, {exception_trace}]}]),
    ok.

dbgadd(Module, Fun) ->
    dbg:tpl(Module, Fun,
	    [{'_', [], [{return_trace}, {exception_trace}]}]),
    ok.

dbgdel(Module) -> dbg:ctpl(Module), ok.

dbgdel(Module, Fun) -> dbg:ctpl(Module, Fun), ok.

dbgclr() -> dbg:stop_clear().

dbgoff() -> dbg:stop().
```
