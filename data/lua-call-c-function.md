# lua call c function

static int (*Lua_cfunction) (lua_State *L)
c函数返回栈中返回值的个数。

通过栈来传递参数和返回值。


lua.h
lualib.h
lauxlib.h
对于cpp来说可以直接用lua.hpp

ctrl+d 退出lua运行环境

5.1使用luaL_register()，5.2以后没有luaL_register(), Lua不鼓励将模块设置到全局域。应该使用luaL_newlib()

获取参数：
lua_tonumber不安全，应该用luaL_checknumber。


swig

使用这种方法可以将任意c库函数导出到lua。但是存在两个缺点：
* 每个函数都需要解析参数，容易出错
* c里面没有类，如果是c++封装起来会很麻烦

为了解决以上两个问题，可以使用swig


nm -g mylib.so
otool -L mylib.so


```c
#include "lua.h"
#include "lualib.h"
#include "lauxlib.h"

static int
add(lua_State *L) {
    double v1 = lua_tonumber(L,1);
    double v2 = lua_tonumber(L,2);
    lua_pushnumber(L, v1 + v2);
    return 1;
}

static const struct luaL_Reg mylib [] = {
    {"_add", add},
    {NULL, NULL}
};

LUALIB_API int luaopen_mylib(lua_State *L) {
    luaL_newlib(L, mylib);
    return 1;
}
```

编译：
`gcc -shared -o mylib.so -fPIC -llua`


```lua
local mylib = require "mylib"
print(mylib._add(4,5))
```

c支持协程：cps。
lua_pcallk

* https://chsasank.github.io/lua-c-wrapping.html
