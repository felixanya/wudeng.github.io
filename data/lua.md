# lua

## 安装

Ubuntu 16.04 安装依赖：
```shell
sudo apt install libreadline-dev autoconf
```

```
curl -R -O http://www.lua.org/ftp/lua-5.3.4.tar.gz
tar zxf lua-5.3.4.tar.gz
cd lua-5.3.4
make linux test
make install
```

* lua 解释器
* luac 编译器
* liblua.a 静态库

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

## 循环

* for循环
    - 数值for循环 `for var=exp1,exp2,exp3 do ... end` 默认步长为1，循环开始前一次性求值，结果用于后面的循环。
    - 泛型for循环 `for i,v in ipairs(a) do ... end` i是数组索引值，v是对应索引的数组元素值。ipairs是lua提供的一个迭代器函数，用来迭代数组。
* while循环
    - `while(condition) do ... end`
* repeat...until循环
    - `repeat ... until(condition)` 条件为true是停止循环

## 流程控制
* `if(condition) then ... end` 注意0为真，false和nil为假
* `if(condition) then ... else ... end` 
* `if(condition) then ... elseif(condition) then ... else ... end`

## 函数
* 多返回值
* 可变参数 ... 
    - ipairs{...} 
    - select("#", ...) 获取可变参数的个数
    - select(n, ...) 返回第n个可变实参

## 字符串
表示方法：
* ''
* ""
* [[]]

字符串操作：
* string.upper(arg)
* string.lower(arg)
* string.gsub(mainstr, findstr, replacestr, num) 字符串替换，num为替换次数，没有num则全部替换，返回的第二个参数表示匹配的个数
* string.find(str, substr, [init, [end]]) 搜索指定内容，返回具体位置，不存在返回nil
* string.reverse(arg)
* string.format(...)
* string.char(arg) 将整形数字转成字符并连接
* string.byte(arg[, int]) 转换字符为整数值。可以指定哪个字符，默认为1，如果为负数，表示从后面开始
* string.len(arg)
* string.rep(string, n) n个拷贝
* .. 连接字符串
* string.gmatch(str, pattern) 返回迭代器函数，每一次调用返回下一个符合pattern的子串。如果没找到，返回nil
    - `for word in string.gmatch("") do print(word) end`
* string.match(str, pattern, init) 只寻找第一个配对，init指定搜索的起点，默认为1，成功配对返回所有捕获结果，如果没有设置捕获标记，返回整个配对字符串，没有成功配对返回nil
    - ()用于捕获
* string.pack(fmt, v1, v2, ...) 返回一个打包了v1, v2等值的二进制字符串
    - < 小端编码 
    - > 大端编码
    - = 大小端遵循本地设置
    - l 一个有符号long
    - L 一个无符号long
    - s[n] 长度加内容的字符串，长度为一个n字节长的无符号整数
    - i[n] 一个n字节长的有符号int
    - I[n] 一个n字节长的无符号int


匹配模式
* .     任意字符
* %a    任意字母
* %c    任意控制符
* %d    任意数字
* %s    任意空白
* %l    任意小写字符
* %u    任意大写字符
* %w    任意字母/数字
* [数个字符类]  与任意字符类配对 
* [^数个字符类] 与任意不包含在[]中的字符类配对
* 用大写时，表示与非此字符类的任意字符匹配

## 数组
索引值从1开始。

## 迭代器
* 泛型for迭代器
    - 初始化，计算in后面表达式的值，返回迭代函数、状态常量、控制变量
    - 状态常量和控制变量作为参数调用迭代函数
    - 将迭代函数返回的值赋给变量列表
    - 如果返回的第一个值为nil，结束循环，否则执行循环
    - 回到第二步再次调用迭代函数
* 无状态迭代器，避免创建闭包花费额外的代价
    - 两个参数，状态常量和控制变量
    - ipairs
* 多状态的迭代器
    - 闭包
    - 封装成table作为状态常量

pairs和ipairs异同：
* 都能遍历集合
* ipairs仅仅遍历值，按照索引升序遍历，索引中断停止遍历
* pairs能遍历集合的所有元素。

## Lua table
不固定大小，可以用任意类型的值来做数组的索引，但这个值不能是nil。Lua通过table来解决模块、包和对象的。
table的赋值指向同一个内存。

table操作：
* table.concat(table [,sep [, start [, end]]]) 列出table中数组部分从start到end位置的所有元素，元素间以sep隔开
* table.insert(table, [pos,] value) 数组部分的pos位置插入值为value的元素，默认位置为数组部分末尾
* table.remove(table [,pos]) 删除元素，前移，默认从最后一个元素删起
* table.sort(table [,comp]) 升序排序
* 使用#获取长度的时候会在索引中断的地方终止计数，导致无法正确取得table的长度

## 模块和包
模块：table，创建table，把常量、函数放入其中，最后返回这个table。

require函数：加载模块，返回table，并定义一个包含该table的全局变量。

加载路径：package.path, LUA_PATH，如果没有定义这个环境变量则使用编译时定义的默认路径来初始化。
.profile或者.bashrc中：
```bash
export LUA_PATH="~/lua/?.lua;;"
```
文件路径以;分隔，最后的两个;;表示新加的路径后面加上原来的默认路径。

找到目标文件，则会调用package.loadfile来加载模块，否则就去找c程序库。
搜索的文件路径是从全局变量package.cpath获取，而这个变量则是通过环境变量LUA_CPATH来初始化。
搜索的策略跟上面一样，只不过换成了so/dll类型的文件，如果找得到，则通过package.loadlib来加载它。

c包使用前必须先加载并连接。动态连接。
```lua
local path = "/usr/local/lua/lib/libluasocket.so"
local f = assert(loadlib(path, "luaopen_socket"))
f() -- 真正打开库
```

stub文件，对应二进制库的实际路径。将stub文件所在的目录加入LUA_PATH，这样设定后就可以使用require函数加载c库。

## 元表 metatable
* setmetatable(table, metatable) 如果元表存在__metatable键值则会失败
* getmetatable(table)

元方法：
* __index 索引 table[key]。可以是函数function(mytable, key) ... end，也可以是表
    * 表中查找，找到返回，找不到继续
    * 看有没有元表，没有返回nil，有继续
    * 看元表有没有__index方法，没有返回nil，有
        - 如果__index是表，重复以上3步
        - 如果__index是函数，调用函数返回该函数的返回值
* __newindex 索引赋值 table[key] = value。对表更新，当给表的一个缺少的索引赋值，如果存在元表，元表存在这个方法，则调用这个方法而不进行赋值操作。
    - __newindex如果是表，给这个表赋值
    - 如果是函数，调用这个函数
* __call 将table作为函数调用时
* __tostring

操作符：
* __add
* __sub
* __mul
* __div
* __mod
* __unm
* __concat
* __eq
* __lt
* __le

## Coroutine

独立堆栈，独立的局部变量，独立的指令指针。
协程和线程的区别：任意时刻只有一个协程。而线程可以有多个。



方法：
* coroutine.create(function() ... end) 创建协程
    - `co = coroutine.create(function() print("hi") end)`
* coroutine.resume(co)  重启协程
* coroutine.yield()     挂起协程
* coroutine.status(co)  查看状态
    * suspended
    * running
    * dead

## 文件IO

两种风格：
* 隐式的文件句柄，设置默认输入文件和输出文件，所有操作都针对这些默认文件，所有操作由io提供
* 显式文件句柄，通过文件句柄来操作。文件句柄通过io.open返回。

io.stdin, io.stdout, io.stderr 预定义文件句柄。可以直接使用：
```lua
io.stderr:write(message)
```
大多数系统中io.stderr没有buffer，而io.stdout为line buffer。

* 简单模式
    * `local file = assert(io.open(filename [,mode]))`
        - r
        - w
        - a
        - r+ 
        - w+
        - a+
        - b
        - +
    * io.input([file])
        - 以文件名调用，以文本模式打开该名字的文件，并将文件句柄设为默认输入文件
        - 以文件句柄调用，将该句柄设为默认输入文件
        - 不传参数，返回当前的默认输入文件
    * io.read(...) 从5.3开始，选项名不再以*开头，忽略
        - 等价于io.input():read(args)
        - "n" 读取一个数字并返回它
        - "a" 从当前位置读取整个文件
        - "l" 小L，读取一行，并忽略行结束标记，读取下一行，EOF处返回nil
        - "L" 读取一行，保留结束标记
        - number 返回指定字符个数的字符串
    * io.close(file)
    * io.output(file)
    * io.write(...)
        - 等价于io.output():write(...)
        - 避免io.write(a..b..c)，尽量使用io.write(a,b,c)可以达到相同的效果而不需要拼接
    * io.tmpfile() 临时文件，程序结束时自动删除
    * io.type(file) 检测obj是否一个可用的文件句柄
    * io.flush() 向文件写入缓冲中的所有数据
    * io.lines(optional file name) 返回一个迭代函数，每次调用获取一行内容，文件尾部返回nil，但不关闭文件
        - 从5.2开始跟io.read接受同样的选项参数
        - 如果没有指定格式，使用默认值"l",即不保留换行符。如果需要保留换行符，传入参数"L"

* 完全模式
    - 用file:function_name()代替io:function_name file是由io.open或者io.input返回的文件句柄

## 错误处理
* assert(bool, msg) 先检查第一个参数没有问题不做任何事情，否则以第二个参数作为错误信息抛出
* error(message [, level]) level指示获得错误的位置
    - level = 1 默认，调用error位置
    - level = 2 调用error的函数的函数
    - level = 0 不添加错误位置

* pcall(protected call) 接收一个函数和要传递的参数，并执行，返回true或者false,errorinfo
    - 捕获函数执行中的任意错误，已经销毁了部分调用栈
* xpcall 接受第二个参数，一个错误处理函数，当错误发生时，lua会在调用栈unwind前调用错误处理函数，于是可以在这个函数中使用debug库来获取关于错误的额外信息
    - 错误处理函数可以接受一个参数，这个参数就是error信息
    - debug.debug 提供一个Lua提示符，让用户来检查错误的原因
    - debug.traceback 根据调用栈来构建一个扩展的错误消息

## Debug
debug库：
* debug() 进入用户交互模式
    - 检阅全局变量和局部变量
    - 改变变量的值
    - 计算一些表达式
* getfenv(object) 返回对象的环境变量
* gethook(optional thread) 返回三个表示线程钩子设置的值
    - 当前钩子函数
    - 当前钩子掩码
    - 当前钩子计数

* 命令行调试
    - RemDebug
    - clidebugger
    - ctrace
    - xdbLua
    - LuaInterface-Debugger
    - RIdb
    - ModDebug

* 图形调试器
    - SciTE
    - Decoda
    - ZeroBrane Studio
    - akdebugger
    - luaedit

## 垃圾回收
增量标记-扫描收集器。
* 间歇率：开启新的循环前等待多久，增大这个值会减少积极性。小于100时不会等待，200等到内存使用量达到之前2倍时才开始新的循环。
* 步进倍率：相对于内存分配速率的倍率。增加这个值会增加积极性，还会增加每个增量步骤的长度。不要小于100，默认是200

函数collectgarbage([opt [, arg]])
* collectgarbage("collect") 做一次完整的垃圾收集循环
* collectgarbage("count") 以k字节数为单位返回Lua使用的总内存数。有小数部分，*1024得到准确字节数。
* collectgarbage("restart")
* collectgarbage("setpause") 间歇率
* collectgarbage("setstepmul") 返回步进倍率的前一个值
* collectgarbage("step") 
* collectgarbage("step")

## LuaRocks

package manager for lua modules.
self-contained packages called rocks.
* unix
* windows



Modules
* lua-cjson
* lua-geoip
* LuaFileSystem
* LuaSocket
* LPeg

## LuaJIT

## Compilation

* loadfile() load lua chunk from a file
* dofile()


* https://luarocks.org/
* http://www.runoob.com/manual/lua53doc/
* http://www.runoob.com/manual/lua53doc/contents.html
* http://www.runoob.com/manual/lua53doc/manual.html