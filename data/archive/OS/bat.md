# Windows Bat File

脚本后缀名：`.cmd`，不区分大小写。

## 命令

* 帮助
    - help [command]
    - command /?
* setlocal
    - setlocal enabledelayedexpansion
* 注释
    - `REM`
    - `::`
* 关闭回显
    - `@ECHO OFF`
* 定义变量：help set
    * `SET FOO="bar"` 等号前后没有空格
    * `SET /A four=2+2`
    * `SET /P "Continue [y/n]>" %confirm%`
    * `set path=E:\erlang\erl5.10.4\bin\;%path%`
* 使用变量：
    - `echo %FOO%`
    - 命令行参数 %1, %2 等
    - `if NOT DEFINED FOO SET FOO="bar"`
    - `if EXIST "%~f1" set filepath=%~f1`
* 特殊变量
    - `%*` 参数列表
    - `%0` 脚本文件名
    - `%ERRORLEVEL%`
    - `%TEMP%` `C:\Users\ADMINI~1\AppData\Local\Temp`
    - `%PATH%`
* 分支 if /?
    - `if "%1"=="" (goto WERL) else (goto ERL)`
* 循环：for /?
    - `for /D %a in (deps/*) do @echo %a` D打印deps下的所有文件夹名字
    - `for /R deps %a in (*) do @echo %a` R递归打印deps目录下的所有问价
    - `for /L %A in (1, 1, 10) DO @echo hello` L增量(start,step,end)
    - 在脚本文件中使用的变量需要带两个%
    - 路径处理：dpnx分别代表Driver、Path、Name、Extension，可以组合
        - `%~dp0` 变量0即脚本文件所在目录，d表示driver，p表示path
        - `%~I ` - expands %I removing any surrounding quotes (")
        - `%~fI`  - expands %I to a fully qualified path name
        - `%~dI`  - expands %I to a drive letter only，变量i的所在驱动，Driver
        - `%~pI`  - expands %I to a path only，变量i代表文件所在的路径：Path
        - `%~nI`  - expands %I to a file name only，变量i代表文件的名称：Name
        - `%~xI`  - expands %I to a file extension only，变量i代表文件的扩展名：Extension，包括点
        - `%~sI`  - expanded path contains short names only
        - `%~aI`  - expands %I to file attributes of file
        - `%~tI`  - expands %I to date/time of file
        - `%~zI`  - expands %I to size of file
* 标签
    - `:label` 开启一段，用`goto lable`实现跳转
    - `goto :eof` 一般使用标签将代码分段。每段后面跟一个`goto :eof`结束当前段落。
* pause
* exit /b [exitCode]
* 函数类似goto label的实现，区别在于可以传递参数，用`CALL :label params`而不是`goto :label`


## 延迟环境变量扩展 Delayed Expansion
cmd中变量的展开默认发生的时刻是读取一条命令的时候而不是在执行它的时候。
这会导致一些需要在执行时才展开变量的程序出现问题。

也就是说变量的展开太早了，很多时候我们需要让变量延迟展开。

```cmd
set VAR=before
if %VAR% == "before" (
    set VAR=after
    if "%VAR%"=after @echo if you see this, it worked
)
```
这里不会打印出来。因为VAR变量都会被替换成before。同样下面这段也不行：
```cmd
set LIST=
for %i in (*) do set LIST=%LIST% %i
echo %LIST%
```
这里的LIST只会赋值给最后一个文件，所以得不到一个文件的列表。
因为在for被读取的时候已经发生了变量替换，所以我们实际执行的是：
```cmd
for %i in (*) do set LIST= %i
```

延迟展开允许使用`!`在执行的时候进行替换而不是读取的时候。所以上面的命令需要写成：
```cmd
setlocal enabledelayedexpansion
if %VAR% == "before" (
    set VAR=after
    if "!VAR!"=after @echo if you see this, it worked
)

set LIST=
for %i in (*) do set LIST=!LIST! %i
echo %LIST%
```

```cmd
@echo off
setlocal enabledelayedexpansion
set b=z1
for %%a in (x1 y1) do (
 set b=%%a
 echo !b:1=2!
)
```
这个例子会打印出x2,y2，1被替换成2。
如果没有`setlocal enabledelayedexpansion`，`!`只是个符号而已。
所以一般情况下，我们可以总是打开延迟展开，我们需要执行时展开就用`!`，不需要延迟展开的话就用`%`。

另外需要注意的是，`setlocal enabledelayedexpansion`只在cmd脚本中有效。如果要在cmd窗口中执行，
要用`cmd /V:ON`来开启。另外一个区别是，脚本中for循环的变量需要`%%i`来引用，而cmd窗口中只需要`%i`。


## 踩过的坑

* windows的bat不支持通配符展开。所以在linux下可以用`erl -pa deps/*/ebin -s main`来启动节点，
在windows下面不得不一个个手动展开。或者使用for循环自己拼接起来一个字符串。

```cmd
@echo off

setlocal enabledelayedexpansion

set ebins=
for /d %%i in (deps/*) do @set ebins=!ebins! -pa deps\%%~ni\ebin
set ebins=%ebins% -pa ebin
set args=-name dev@192.168.4.116 -setcookie tianshu -config etc/sys.config -s main start game -s reloader -boot start_sasl

::echo %ebins% %args%

if "%1"=="" (
    start werl %ebins% %args%
) else (
    erl %ebins% %args%
)

```

## 参考文档
* http://steve-jansen.github.io/guides/windows-batch-scripting/index.html
* https://blogs.msdn.microsoft.com/ben/2007/03/09/path-manipulation-in-a-batch-file/
* https://stackoverflow.com/questions/6679907/how-do-setlocal-and-enabledelayedexpansion-work
