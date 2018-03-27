Erlang Trace
====

tracer有两种，一种是process，一种是port。默认的tracer是个process。
`tracer(process, {Hanlder, HandlerData})`
Hanlder接收两个参数，一个Msg，一个HandlerData，返回更新后的HandlerData。
Handler = fun((Msg, AccIn) -> AccOut end)
类似lists:foldl(Fun, Acc0, List)里面的Fun函数，只不过Fun处理的是List里面的元素，
而Handler处理的是process收到的trace消息。trace消息有五种格式：
- end_of_trace
- trace Tuple
- trace_ts Tuple
- {drop, Msg}
- seq_trace Tuple
```
tracer(Total) ->
    dbg:tracer(process,
        {
            fun
                (_, N) when N >= Total -> dbg:stop_clear();
                (Msg, N) -> io:format("~p~n", [Msg]), N + 1
            end,
            0
        }
    ).

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
