## Erlang时间相关

在Erlang/OTP 18(ERTS 7.0)之前，时间接口主要是两个：erlang:now()返回虚拟机时间，os:timestamp()返回操作系统时间。
erlang:now()存在性能问题，被迫使用os:timestamp()又存在时间回退的问题。
从OTP 18以后，引入了很多新的时间接口。

18之前支持时间纠正。
18之后支持之间纠正和时间跳跃。



## 基本概念

* UT1：世界时
* UTC：Coordinated Universal Time，协调世界时，对秒的定义跟UT1有差异，包含闰秒。UTC的一天可能为86399, 86400, 86401秒。
* POSIX Time(aka Unix/Epoch time): Time since EPOCH (UTC 1970-01-01 00:00:00)，POSIX Time的一天刚好为86400秒。奇怪的是EPOCH被定义为UTC时间。
* `OS System Time`：操作系统视角的POSIX time。存在时间跳跃。
* `Erlang System Time`: Erlang运行时视角的POSIX time。跟操作系统时间可能有偏差。
* Erlang monotonic time: events, timers, time interval, 单调，但是不严格单调递增>=。
* Time offset: 通过时间偏移来同步操作系统时间，无需修改单调时间的频率。

时间单位：每秒多少个单位:
* second，1
* millisecond，1000
* microsecond，1000000
* nanosecond，1000000000
* native，Erlang runtime system使用的单位，不同的操作系统会不一样。我的电脑里面，Windows下为1024000，CentOS下为1000000000。

时间单位的转化可以通过函数：erlang:convert_time_unit(Time, FromUnit, ToUnit)。

`erlang:convert_time_unit(1, second, native).` = number of whole native time units per second.

## Time Warp
* no_time_warp 默认方式，系统启动的时候就决定了time offset，以后也不会改变。
跟之前的系统兼容。因为offset不会变。所以只能通过调整mono time的频率来接近系统时间。mono的时间频率存在1%的误差。
* multi_time_warp
* single_time_warp Multi-time warp mode in combination with time correction is the preferred configuration.


monitor(time_offset, clock_service)

## Time Correction
- `+c true | false`
- erlang:system_info(time_correction). 如果关闭时间纠正，Erlang monotonic time可能向前跳或者停止。永远打开这个选项。
- 如果设置为true，Erlang通过加速和减速来跟操作系统时间同步。幅度最大是1%，也就是说，VM经历1秒实际上可能是0.99秒或者1.01秒。
当系统时间改变了1分钟，erlang会花100分钟来慢慢校正，并最终与系统时间同步。
- 如果设置为false，当操作系统时间落后时，虚拟机时间会停滞。直到操作系统时间追上来为止。这意味着重复调用erlang:monotonic_time()会返回相同的值。
当操作系统时间领先时，monotonic_time前跳。

## OS System Time

操作系统时间不是单调递增的。系统时间随时可以修改。比如我去了一个操作系统时间t1，然后将系统时间改为1天前，再取一个系统时间t2，
t2-t1的出来是个负值。
```
> erlang:system_info(os_system_time_source).
[{function,'GetSystemTime'},
 {resolution,100},
 {parallel,yes},
 {time,1558773547665408}]

> erlang:system_info(os_monotonic_time_source).
[{function,'GetTickCount64'},
 {resolution,100},
 {extended,no},
 {parallel,yes},
 {time,1191373658112}]
```

* os:system_time() 返回native时间单位的操作系统时间`os system time`
* os:system_time(Unit) 将操作系统时间转化为Unit时间单位。等价于 `erlang:convert_time_unit(os:system_time(), native, Unit)`。
* os:timestamp() -> {MegaSecs, Secs, MicroSecs}
    - calendar:now_to_universal_time/1
    - calendar:now_to_local_time/1

```
format_utc_timestamp() ->
    TS = {_,_,Micro} = os:timestamp(),
    {{Year,Month,Day},{Hour,Minute,Second}} = calendar:now_to_universal_time(TS),
    Mstr = element(Month,{"Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"}),
    io_lib:format("~2w ~s ~4w ~2w:~2..0w:~2..0w.~6..0w",[Day,Mstr,Year,Hour,Minute,Second,Micro]).
```

## Erlang System Time

正是因为操作系统时间如此不可靠，我们需要一个单调递增的时间。这就是虚拟机时间。
不管系统时间怎么变，只要虚拟机不停机，前后两次虚拟机时间相减得出的时间差相对比较准确。
在操作系统时间发生变化的时候，虚拟机时间的表现取决于两个参数：Time Warp和Time Correction.


Properties of Erlang corrected time:
* Never jumps backwards or forwards
* Never differs more than 1% in speed from OS Monotonic time
* Attemps to be as close as possible to OS system time

jumping from 1970 to 2015 will take 4500 years to recover, which means all relative time will happen 1% faster for 40 years.

* erlang:system_time() 返回native时间单位的虚拟机时间`erlang system time`，虚拟机时间由两部分构成：time_offset和monotonic_time。
erlang:system_time() 等价于 erlang:monotonic_time() + erlang:time_offset()
* erlang:system_time(Unit) 将erlang系统时间转化为Unit时间单位。等价于`erlang:convert_time_unit(erlang:system_time(), native, Unit)`
* erlang:monotonic_time() 虚拟机内部的时间引擎。定时器、receive after定时器、BIF定时器、timer模块定时器都是由这个时间触发。
* erlang:time_offset()
* erlang:timestamp() -> {MegaSecs, Secs, MicroSecs} `Erlang system time`，这个函数的存在只是为了兼容现有的代码的时间格式。
`Erlang system time`可以通过上面的函数erlang:system_time/1更加高效的获取。这个函数等价于：

```
timestamp() ->
    ErlangSystemTime = erlang:system_time(microsecond),
    MegaSecs = ErlangSystemTime div 1000000000000,
    Secs = ErlangSystemTime div 1000000 - MegaSecs*1000000,
    MicroSecs = ErlangSystemTime rem 1000000,
    {MegaSecs, Secs, MicroSecs}.
```


## 使用指南

时间跳跃安全的代码：Time Warp Safe Code，能够处理时间Erlang系统时间跳跃。
* 不用erlang:now/0

### 获取系统时间

使用erlang:system_time/1获取系统时间。如果需要erlang:now/0返回的数据格式，可以用erlang:timestamp/0。

### 测量时间差

使用erlang:monotonic_time/0之差来测量时间，结果是native时间单位，可以用erlang:convert_time_unit/3来转化为其他时间单位。
也可以直接使用erlang:monotonic_time/1之差来测量时间，不过这种方式会损失一定的精度。

### 事件的顺序

erlang:unique_integer([monotonic]).

### 随机数种子

* erlang:monotonic_time()
* erlang:time_offset()
* erlang:unique_integer()

## 参考文档
* http://learnyousomeerlang.com/time
