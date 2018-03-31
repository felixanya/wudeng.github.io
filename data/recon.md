# recon

追踪：lib_ai:get_targets1(14)

recon_trace:calls({lib_ai, get_targets1, fun([14]) -> ok end}, 100).

默认只trace global的函数，通过指定scope来选择。
指定特定的进程：

```erlang
recon_trace:calls({lib_ai, tick, '_'}, 10, [{pid, MapPid},{scope,global|local}]).

recon_trace:clear().

%% 怀疑这种方式是因为只trace global的函数
recon_trace:calls({lib_ai, tick_cb, fun(['_',#tick{monster_id=14}]) -> ok end}, 10, [{pid, MapPid},{scope,local}]).


recon_trace:calls({lib_ai, tick, fun([#tick{monster_id = 14}]) -> return_trace() end}, 10, [{pid, MapPid}]).


put(dic_battle_list_monster,[14])


```

结束：
recon_trace:clear().
