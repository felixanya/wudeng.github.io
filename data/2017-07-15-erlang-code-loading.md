Erlang代码加载模式
====

ERTS中有两种代码加载模式：
* interactive：代码第一次被引用的时候会去代码路径中搜索并加载。
* embedded：一开始就根据boot script来加载。

code模块对外提供接口，code_server模块处理实际的工作，注册为code_server。维护一个私有ets表code。
预加载的10个模块，包括erlang_prim_loader，erlang，init，prim_inet，prim_file等模块，需要被code_server用到的模块都属于预加载。
```
(dev@127.0.0.1)1> erlang:pre_loaded().
[erts_internal,erlang,erl_prim_loader,prim_zip,zlib,
 prim_file,prim_inet,prim_eval,init,otp_ring0]
```
ERTS系统中，一个模块可以存在两个版本。新版本(current)和老版本(old)，两个版本都是有效的。
代码第一次加载进来的时候是新版本，当有新的代码加载的时候，新的代码变成新版本，原来的新版本变成老版本。当有第三个版本加载进来的时候，之前的老版本就需要被移除掉(purge)，
这样如果有进程还在执行老版本的代码，这些进程就会被无条件杀死。所以一般第一次热更新的时候是安全的，因为系统只有一个新版本的代码。第二次热更新如果使用强制更新就可能杀死
一些进程，要避免这种情况，一方面是在调用代码的时候使用全名函数，一方面在热更新的时候使用软更新(soft purge)。这样如果还有进程在执行老代码，就不会执行热更新。
OTP框架下的模板都是支持热更新的。

purge
----
purge：是指将老版本的代码从系统中移除掉，如果仍有进程在执行老版本代码，则将这些进程杀掉。
soft_purge：尝试将老代码移除掉，如果仍有进程在执行老版本代码，则返回失败。


erlang:check_old_code(Mod) -> true | false. 检查模块是否有老代码。

要找出仍在执行老代码的进程仍然需要遍历进程列表processes()，对进程执行erlang:check_process_code(Pid, Mod) -> boolean()
返回进程时候仍在执行该模块的老代码。有三种情况下返回true：
* 进程正在执行老代码。这种情况下是不管进程调用的全名函数短名函数。对于全名函数，当前这次执行完了下次就会切换到新代码，所以一开始返回true，后面就false了。对于短名函数则始终为true。
* 进程包含短名函数的引用。
* 进程包含引用短名函数的fun对象。比如其他模块发送了一个fun给进程执行，这个fun对象包含了模块的短名对象，那么执行fun对象期间返回true。

杀进程。一般先monitor，再kill，等待接收moniter消息，确认当前进程杀死。因为被杀死的进程可能需要进行一些清理的行为，如果不等待它确认死亡就将执行后续操作如移除代码，可能会导致清理过程出现找不到代码的问题。

杀死进程的过程：

```
%% do_purge(Module)
%%  Kill all processes running code from *old* Module, and then purge the
%%  module. Return true if any processes killed, else false.

do_purge(Mod0) ->
    Mod = to_atom(Mod0),
    case erlang:check_old_code(Mod) of
	false -> false;
	true -> do_purge(processes(), Mod, false)
    end.

do_purge([P|Ps], Mod, Purged) ->
    case erlang:check_process_code(P, Mod) of
	true ->
	    Ref = erlang:monitor(process, P),
	    exit(P, kill),
	    receive
		{'DOWN',Ref,process,_Pid,_} -> ok
	    end,
	    do_purge(Ps, Mod, true);
	false ->
	    do_purge(Ps, Mod, Purged)
    end;
do_purge([], Mod, Purged) ->
    catch erlang:purge_module(Mod),
    Purged.
```

直接purge有杀死进程的危险性，所以code_server也提供了soft_purge，如果有进程仍在执行老代码，则不移除老代码。
```
%% do_soft_purge(Module)
%% Purge old code only if no procs remain that run old code.
%% Return true in that case, false if procs remain (in this
%% case old code is not purged)

do_soft_purge(Mod) ->
    case erlang:check_old_code(Mod) of
	false -> true;
	true -> do_soft_purge(processes(), Mod)
    end.

do_soft_purge([P|Ps], Mod) ->
    case erlang:check_process_code(P, Mod) of
	true -> false;
	false -> do_soft_purge(Ps, Mod)
    end;
do_soft_purge([], Mod) ->
    catch erlang:purge_module(Mod),
    true.
```

热更机制
----
在开发环境中，可以使用Mochiweb的reloader模块来进行热更。reloader的实现机制主要是每隔一秒钟检测一次系统中加载的代码对应的beam文件时间戳，
如果发现时间戳从上次检测以来发生了变更，就执行热更新。在开发中使用起来非常方便。我们只需要编译代码，系统自动进行加载。
但是reloader的热更新用的是purge，也就是说如果你的进程不符合otp规范，比如进程的主循环用的是短名函数，那么就存在进程被杀死的风险。
reloader的核心代码如下：
```
doit(From, To) ->
    [case file:read_file_info(Filename) of
         {ok, #file_info{mtime = Mtime}} when Mtime >= From, Mtime < To ->
             reload(Module);
         {ok, _} ->
             unmodified;
         {error, enoent} ->
             %% The Erlang compiler deletes existing .beam files if
             %% recompiling fails.  Maybe it's worth spitting out a
             %% warning here, but I'd want to limit it to just once.
             gone;
         {error, Reason} ->
             io:format("Error reading ~s's file info: ~p~n",
                       [Filename, Reason]),
             error
     end || {Module, Filename} <- code:all_loaded(), is_list(Filename)].

reload(Module) ->
    io:format("Reloading ~p ...", [Module]),
    code:purge(Module), %% **watch out**
    case code:load_file(Module) of
        {module, Module} ->
            io:format(" ok.~n"),
            case erlang:function_exported(Module, test, 0) of
                true ->
                    io:format(" - Calling ~p:test() ...", [Module]),
                    case catch Module:test() of
                        ok ->
                            io:format(" ok.~n"),
                            reload;
                        Reason ->
                            io:format(" fail: ~p.~n", [Reason]),
                            reload_but_test_failed
                    end;
                false ->
                    reload
            end;
        {error, Reason} ->
            io:format(" fail: ~p.~n", [Reason]),
            error
    end.
```
实际上Erlang Shell中提供的函数的热更函数l(Module)实现就是直接purge，shell提供的函数实现在c.erl文件中，
```
l(Mod) ->
    code:purge(Mod),
    code:load_file(Mod).
```


直接load代码存在杀死进程的危险，所以一种安全的热更机制应该先尝试soft purge，如果成功，则加载代码，失败则放弃更新。
```
case soft_purge(Mod) of
true -> code:load_file(Mod);
false -> error 
end
```

参考文档
----
* [Compilation and Code Loading](http://erlang.org/doc/reference_manual/code_loading.html)
* [sync](https://github.com/rustyio/sync)
