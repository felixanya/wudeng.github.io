# lua

## Coroutine

协程和线程的区别：任意时刻只有一个协程。而线程可以有多个。

```lua
co = coroutine.create(function()
    print("hi")
end)
```

* suspended
* running
* dead

coroutine.status(co)
coroutine.resume(co)
coroutine.yield()

co = coroutine.create(function()
    for i=1,10 do
        print("co", i)
        coroutine.yield()
end)
