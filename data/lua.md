# lua


5.3.3

多行字符串 [[
    multiple
    line
    comment
]]

number
nil
math
    floor
    ceil
    random()

math.randomseed(os.time())
math.pi

if then
elseif
end

meta table
```
Animal = {height = 0, weight = 0, name = "No Name"}
function Animal:new (height, weight, name)
    setmetatable({}, Animal)
    self.height = height
    self.weight = weight
    self.name = name
    return self
end
```

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


## Module

new way
old way


## LuaJIT
