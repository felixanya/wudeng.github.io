
UTC：协调世界时，包含闰秒。

POSIX Time: Time since EPOCH (UTC 1970-01-01 00:00:00)，POSIX Time的一天刚好为86400秒。
奇怪的是EPOCH被定义为UTC时间，而UTC的一天有不同的定义。可能为86399, 86400, 86401秒。

时间相关函数

Time Warp: 时间跳跃。

Erlang System Time VM时间，Erlang runtime systems view of POSIX time.
erlang:system_time()



OS System Time 操作系统时间


获取系统时间：

erlang:system_time(seconds)

测量时间差：

erlang:monotonic_time/0,1

事件的顺序：

erlang:unique_integer([monotonic]).



To find system time: erlang:system_time/0-1
To measure time differences: call erlang:monotonic_time/0-1 twice and subtract them
To define an absolute order between events on a node: erlang:unique_integer([monotonic])
Measure time and make sure an absolute order is defined: {erlang:monotonic_time(), erlang:unique_integer([monotonic])}
Create a unique name: erlang:unique_integer([positive]). Couple it with a node name if you want the value to be unique in a cluster, or try using UUIDv1s.

* http://learnyousomeerlang.com/time
