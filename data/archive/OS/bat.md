# Windows Bat File

后缀名：`.cmd`，不区分大小写。

```
set path=E:\erlang\erl5.10.4\bin\;%path%
```

* 注释
    - `REM`
    - `::`
* 关闭回显
    - `@ECHO OFF`
* 定义变量：
    * `SET FOO="bar"` =前后没有空格
    * `SET /A four=2+2`
* 使用变量：
    - `echo %FOO%`
    - 命令行参数 %1, %2 等
    - `if NOT DEFINED FOO SET FOO="bar"`
* 特殊变量
    - `%0` 脚本文件名
* 分支 if /?
    - `if "%1"=="" (goto WERL) else (goto ERL)`
* 循环：for /?
    - `for /D %a in (deps/*) do @echo %a` D打印deps下的所有文件夹名字
    - `for /R deps %a in (*) do @echo %a` R递归打印deps目录下的所有问价
    - `for /L %A in (1, 1, 10) DO @echo hello` L增量(start,step,end)
    - 在脚本文件中使用的变量需要带两个%
    - 路径处理：dpnx分别代表Driver、Path、Name、Extension，可以组合
        - `%~dp0` 变量0即脚本文件所在目录，d表示driver，p表示path
        - `%~di` 变量i的所在驱动，Driver
        - `%~pi` 变量i代表文件所在的路径：Path
        - `%~ni` 变量i代表文件的名称：Name
        - `%~xi` 变量i代表文件的扩展名：Extension，包括点
* 标签
    - `:label` 开启一段，用`goto lable`实现跳转
    - `goto :eof` 一般使用标签将代码分段。每段后面跟一个`goto :eof`结束当前段落。
* pause



## 踩过的坑

* windows的bat不支持通配符展开。所以在linux下可以用`erl -pa deps/*/ebin -s main`来启动节点，
在windows下面不得不一个个手动展开。



变量替换问题

* http://steve-jansen.github.io/guides/windows-batch-scripting/index.html
* https://blogs.msdn.microsoft.com/ben/2007/03/09/path-manipulation-in-a-batch-file/
