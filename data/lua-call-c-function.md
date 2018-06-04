# lua call c function


C API:
* read and write Lua global variables
* call lua function
* run lua pieces of lua code
* register c function can be called by lua



luaL_loadstring：编译lua并将函数压栈
luaL_pcall：弹栈并执行

如果出错，两个函数都会将错误消息入栈。通过lua_tostring可以获取这条消息。
lua_pop弹出消息。




static int (*Lua_cfunction) (lua_State *L)
c函数返回栈中返回值的个数。

通过栈来传递参数和返回值。
正索引：1是栈底，最先入栈的元素。n是栈顶，最后入栈的元素。
负索引：-1是栈顶，不知道元素个数，通过-1可以找到栈顶。-n是栈底。
无论正数还是负数，栈顶都大于栈底。

lua只能修改栈顶部元素。而c可以查看栈中任何位置的元素。甚至插入和删除任意位置的元素。

pushing elements:
* lua_pushnil(lua_State *L)
* lua_pushboolean(lua_State *L, int bool)
* lua_pushnumber(lua_State *L, lua_Number n)
* lua_pushinteger(lua_State *L, lua_Integer n)
* lua_pushlstring(lua_State *L, const char *s, size_t len)
* lua_pushstring(lua_State *L, const char *s)

Querying elements:
* int lua_is*(lua_State *L, int index)
get value from stack:
* int lua_toboolean(lua_State *L, int index)
* const char *lua_tolstring(lua_State *L, int index, size_t *len)
* lua_State *lua_tothread(lua_State *L, int index)
* lua_Number lua_tonumber(lua_State *L, int index)
* lua_Integer lua_tointeger(lua_State *L, int index)



lua.h
lualib.h
lauxlib.h
对于cpp来说可以直接用lua.hpp

ctrl+d 退出lua运行环境

5.1使用luaL_register()，5.2以后没有luaL_register(), Lua不鼓励将模块设置到全局域。应该使用luaL_newlib()

```c
#define luaL_register(L, n, f) (lua_pushcfunction(L, f), lua_setglobal(L, n))
```


获取参数：
lua_tonumber不安全，应该用luaL_checknumber。

* luaL_checkstring
* luaL_checknumber

swig

使用这种方法可以将任意c库函数导出到lua。但是存在两个缺点：
* 每个函数都需要解析参数，容易出错
* c里面没有类，如果是c++封装起来会很麻烦

为了解决以上两个问题，可以使用swig


nm -g mylib.so : list symbols from object files
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
