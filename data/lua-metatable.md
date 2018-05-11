# lua metatable


self

调用函数：dog:reactToDoorbell() 等价于 dog.reactToDoorbell(dog)
method里面用self来指代当前的dog

__index 值是一个table，如果找不到属性，就会去__index table里面找。这个行为一般被用来实现继承。

Animal = {}
Animal.prototype = {__index : {name : ""}}

Dog = {}
setmetatable(Dog, Animal.prototype)

function Animal:new(o)
  setmetatable(o, Animal.prototype)
  return o
end


__call 值是一个函数，可以使得表能够像函数一样被调用。



package.path

loadfile
dofile
require 可重复


table的底层实现包含两个部分：数组部分和哈希表部分。
lobject.h

类型的实现：
带标记的联合体。

```
#define LUA_TNIL        0
#define LUA_TBOOLEAN        1
#define LUA_TLIGHTUSERDATA  2
#define LUA_TNUMBER     3
#define LUA_TSTRING     4
#define LUA_TTABLE      5
#define LUA_TFUNCTION       6
#define LUA_TUSERDATA       7
#define LUA_TTHREAD     8
```


NUMBER实质是double类型，lua 5.3 分开了整数和浮点数的表示。

Value是个联合体。
GCObject也是个联合体。


table的实现分为两个部分：
* array，2^n
* hash, open address table, 2^m


string internalized


GC
标记清除


设置__index 为table自己有什么用？

保持继承链。


weak table, __mode
* weak key  "k"
* weak value "v"

weak key, strong value : ephemeron table

__mode="kv"

