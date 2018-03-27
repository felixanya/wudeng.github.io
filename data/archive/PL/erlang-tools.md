# Erlang系统工具


推荐recon，非常全面的线上调试工具。下面是我个人比较偏爱的一些线上分析问题的工具。一般可以放在user_default模块，在shell里面直接执行。

## 反编译

用于确认线上代码是否正确版本。需要在编译的时候加上debug_info选项，btw，加上export_all选项对于线上调试很有帮助。
```
decompile(Mod) -> decompile(Mod, undefined).
decompile(Mod, Dir) ->
    {ok, {_, [{abstract_code, {_, AC}}]}} = beam_lib:chunks(code:which(Mod), [abstract_code]),
    Code = erl_prettypr:format(erl_syntax:form_list(AC)),
    case is_list(Dir) of
        true ->
            FileName = filename:join([Dir, lists:concat([Mod, ".erl"])]),
            filelib:ensure_dir(Dir),
            {ok, IoDev} = file:open(FileName, [binary, write]),
            file:write(IoDev, unicode:characters_to_binary(Code)),
            file:close(IoDev);
        _ ->
            io:format("~ts~n", [Code])
    end.
```

## 进程栈

类似jstack，分析异常进程行为。
```
pstack(ProcName) when is_atom(ProcName) ->
    case whereis(ProcName) of
        Pid when is_pid(Pid) -> show_stack(Pid);
        _ -> undefined
    end;
pstack(Pid) ->
    case erlang:process_info(Pid, backtrace) of
        {_, Stack} -> show_info("~ts~n", [Stack]);
        Ret -> Ret
    end.
```

## etop

类似系统top，查询占用资源最多的进程。
```
top_mem() -> show_top(20, 5, memory).
top_msg() -> show_top(20, 5, msg_q).
top_runtime() -> show_top(20, 5, runtime).
top_reductions() -> show_top(20, 5, reductions).
show_top() -> show_top(20, 5, memory).
show_top(SortBy) -> show_top(20, 5, SortBy).
show_top(Count, Interval, SortBy) ->
    erlang:spawn(fun() ->
        Opts = [{node, node()}, {output, text}, {lines, Count}, {interval, Interval}, {sort, SortBy}],
        etop:start(Opts)
    end).
stop_top() -> etop:stop().
```

## 手动gc

内存占用过高时可以快速见效，属于治标不治本。
```
gc() -> gc(erlang:processes()).
gc(Pid) when is_pid(Pid); is_atom(Pid) -> gc([Pid]);
gc(PidList) when is_list(PidList) ->
    Fun = fun (P) when is_pid(P) ->
                  erlang:garbage_collect(P), show_info("gc ~p ", [P]);
              (P) when is_atom(P) ->
                  case whereis(P) of
                      Pid when is_pid(Pid) ->
                          erlang:garbage_collect(Pid), show_info("gc ~p", [P]);
                      _ -> ignore
                  end;
              (_) -> ignore
          end,
    lists:foreach(Fun, PidList).
```

## Trace

非常强大的功能，可以参见另一篇[]()

## system_monitor

可以设置一个进程监听系统的异常情况。当发生异常情况时这个进程会收到通知。

* longgc
* large_heap
* busy_port
* busy_dist_port
* long_schedule

## process_info

* last_calls
    - process_flag(save_calls, N), N > 0
    - process_flag(Pid, save_calls, N)，这个函数意味着这个标记可以从外部设置
    - global function calls, BIF calls, receives

## 系统级别

* perf top
* dstat -tam
* vtune
