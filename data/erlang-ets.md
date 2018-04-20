# Erlang ETS

## 访问权限
* public 所有进程可以读写。
* protected 默认选项。所有进程可读，宿主进程可写。
* private 只有宿主进程可以读写。

## 并行优化
* {write_concurrency, boolean()}
默认为false。这种情况下对ETS进行写操作会获得排它锁。其他所有操作都需要等待写操作释放锁才能进行。
如果为true，那么对不同数据的写可以并行。代价是内存和串行访问以及并行读的效率。
对于ordered_set类型的表，这个选项无效。

* {read_concurrency, boolean()}
默认为false。打开这个选项，并行读效率更高，但是读写切换的开销增大。


match如果限定key，效率是很高的。如果没有限定key，需要全表扫描。
