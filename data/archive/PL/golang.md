# GO

go env [-json] [var ...] 打印环境变量

vim .bashrc
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=/home/wudeng/golib
export PATH=$PATH:$GOPATH/bin
export GOPATH=$GOPATH:/home/wudeng/code

GOPATH的第一部分被当成下载的默认地址，其他部分用于查找。

linux下设置GOPATH：
```bash
export GOPATH=`pwd`
```

windows下设置GOPATH:
```cmd
set GOPATH=%cd%
```

mkdir -p src/github.com
mkdir pkg
mkdir bin

go get -u github.com/nsf/gocode

go run src/github.com/wudeng/firstapp/Main.go
go build github.com/wudeng/firstapp
go install github.com/wudeng/firstapp

bin/firstapp


code.visualstdio.com

```
all:
	$(shell \
		export GOPATH=`pwd`; \
		export GOARCH=amd64; \
		export GOOS=linux; \
		cd bin; \
		go build -o admins -ldflags "-w -s -X main.version=`date +%s`" ../src/admin.go)
```

GO runtime: embedded in every executable
* memory allocation
* garbage collection: mark-and-sweep
* stack handling
* goroutines
* channels
* slice
* maps
* reflection
* ...

## 代码风格
文件名：小写字母用下划线连起来。
package: 小写。
函数Camel风格，需要导出的函数首字母大写。
变量Camel风格，需要导出的变量首字母大写。局部变量首字母小写。inputFieldName

application由不同的包package构成，每个应用有一个main包。
一个包含有多个文件

## 类型转换
string => int ： strconv:ParseInt(s, base, bits)

gopath
    查看当前GOPATH：go env GOPATH
    src: source code, import path
    bin: compiled commands
    pkg/GOOS_GOARCH: installed package objects

    internal
        import "foo/internal/baz"
    vendor  
        优先级更高
        import "baz"

目前版本：
go version: go version go1.9.1 windows/amd64
go help

go run
go build


go build -o main .

导出函数或变量首字母大写

fmt格式
* %v Value,
* %+v instance with field names
* %#v instance with its fields, syntax representation
* %T Type
* %q quot string
* %g 浮点数

## variable
var i int
i = 52
var j int = 213
k := 11

scope
* package
* global
* block

# 基本类型

statically typed: every variable has a static type, that is, exactly one type known and fixed at compile time;

bool
string
int int8 int16 int32 int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8
rune // int32, unicode
float32 float64
complex64 complex128

类型转换：
string   to int


## const

iota

## for 循环

for i := 0; i < 10; i++ {
    sum += i
}

sum := 1
for sum < 1000 {
    sum += sum
}

死循环
for {
}

## switch

func do(i interface{}) {
    switch v := i.(type) {
        case int:
    }
}

## [defer](https://blog.golang.org/defer-panic-and-recover)

* A deferred function's arguments are evaluated when the defer statement is evaluated.
* Deferred function calls are executed in Last In First Out order after the surrounding function returns.
* Deferred functions may read and assign to the returning function's named return values.

当使用os.Exit结束函数时不会执行defer。

## panic and recover
Panic is a built-in function that stops the ordinary flow of control and begins panicking.

When the function F calls panic, execution of F stops, any deferred functions in F are executed normally, and then F returns to its caller.
To the caller, F then behaves like a call to panic.
The process continues up the stack until all functions in the current goroutine have returned, at which point the program crashes.

Panics can be initiated by invoking panic directly.
They can also be caused by runtime errors, such as out-of-bounds array accesses.

Recover is a built-in function that regains control of a panicking goroutine.
Recover is only useful inside deferred functions.
During normal execution, a call to recover will return nil and have no other effect.
If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.

## 指针

nil

## 结构体

## 数组

var a [5]int
[...]string{"hello", "world"}

## slices

make([]int, 5)

## map
map[KeyType]ValueType
    KeyType: any type that is comparable
    ValueType: any type at all, including another map

使用之前必须用make来创建，值为nil的map是空的，并且不能对其赋值。
var m map[string]int
m = make(map[string]int)

key equality operator is defined
    integers
    floating point
    complex numbers
    strings
    pointers
    interfaces as long as the dynamic type supports equality
    structs
    arrays
slice can not be used as map keys, because equality is not defined on them.

## struct

embedded field
    promoted

## type assertions

t := i.(T)
This statement asserts that the interface value i holds the concrete type T and assigns the underlying T value to the variable t.


## packages

* io
    * Reader
    * ReadFull
* fmt
* strings
    * NewReader()
* strconv
    * func ParseUint(s string, base int, bitSize int) (uint64, error)
* math
    * math/rand
    * math/cmplx
    * MaxInt32
* runtime
    * GOOS
* time
    Minute
* sync
    * sync/atomic
    * type Mutex struct {}
    * type RWMutex struct {}
    * WaitGroup
        - Add
        - Done
        - Wait
* os
    * os/signal
        * func Notify(c chan<- os.Signal, sig ...os.Signal)
    * os/exec
    * Args
    * Exit
* syscall
* reflect
    - Type
    - Value
    - ValueOf
    - TypeOf
* net
    - Conn
    - Listener
    - Listen
* log
    - Fatalf
* log "github.com/Sirupsen/logrus"
    - Panic
    - Panicf
    - Info
    - Infof
    - Error
* context
    - Background

查看帮助：go doc http

## Container
* container/heap

## MockGen

mocking is creating objects that simulate the behaviour of real objects

mode
    - source : generates mock interface from a source file
        -source
        -imports
        -aux_files
    - reflect : generates mock interfaces by building a program that use reflection to understand interface
        import path
        comma-seperated list of symbols

-destination=mocks/mock_doer.go
-package=mocks

comment:
//go:generate mockgen -destination=../mocks/mock_doer.go -package=mocks github.com/sgreben/testing-with-gomock/doer Doer
from project root: `go generate ./...`

go get github.com/golang/mock/gomock
go get github.com/golang/mock/mockgen
    $GOPATH/bin/mockgen.exe
go doc github.com/golang/mock/gomock

https://blog.codecentric.de/en/2017/08/gomock-tutorial/
https://martinfowler.com/articles/mocksArentStubs.html


## 开源资源
* github.com/boltdb/bolt            基于文件的kv数据库
* gopkg.in/vmihailenco/msgpack.v2   串行化

## 热更新
http://www.cppblog.com/sunicdavy

http无状态热更新，已有成熟方案。
游戏服务器带状态的热更新。

使用go1.8提供的plugin包机制实现，不支持windows。

高并发
Gorouting
channel



gofmt自动排版

gofix
govet


Session

export GOPATH=`pwd`


go get -u github.com/gorilla/sessions
go run session.go



## 问题

```
path_triangle = append([][3]int32{triangles[cur_id]}, path_triangle...)
```

### 已解决

import(
    . "game/types"
)
是啥意思

If an explicit period (.) appears instead of a name, all the package's exported identifiers will be declared in the current file's file block and can be accessed without a qualifier.

Assume we have compiled a package containing the package clause package math, which exports function Sin, and installed the compiled package in the file identified by "lib/math".
This table illustrates how Sin may be accessed in files that import the package after the various types of import declaration.

Import declaration          Local name of Sin

import   "lib/math"         math.Sin
import M "lib/math"         M.Sin
import . "lib/math"         Sin


## 依赖管理
https://ieevee.com/tech/2017/07/10/go-import.html

[go get](https://golang.org/cmd/go/#hdr-Download_and_install_packages_and_dependencies)
    go get -u github.com/labstack/echo/...
[dep](https://github.com/golang/dep)
    * dep ensure -add github.com/labstack/echo@^3.1
[glide](http://glide.sh/)
    * glide get github.com/labstack/echo#~3.1


gvt - recursively retrive and vendor packages

外部依赖
    github.com
    k8s.io
    golang.org

go get需要翻墙时可以设置：
set http_proxy=127.0.0.1:1080
set https_proxy=127.0.0.1:1080

go get -u github.com/labstack/gommon
    * $GOPATH/src/github.com/labstack/gommon
    * import github.com/labstack/gommon
    -d : not install
    -f : -u, not to verify
    -fix
    -insecure
    -t
    -u


go help gopath
go help packages
go help importpath

## cross platform compile
go 1.5以后的跨平台编译很简单，只要指定GOOS, GOARCH为目标平台就可以了。
```
cc:
	$(shell \
		export GOPATH=`pwd`; \
		export GOARCH=amd64; \
		export GOOS=linux; \
		cd bin; \
		go build -o admins -ldflags "-w -s -X main.version=`date +%s`" ../src/admin.go)
	tar zcf admin.tar.gz ./bin
	scp -i $(IDENTITY) admin.tar.gz $(HOST):$(ADMINS)
	ssh -i $(IDENTITY) $(HOST) "cd $(ADMINS); tar zxf admin.tar.gz; cd bin; chmod +x admins; pkill admins; bash run"

```

## web 框架

https://echo.labstack.com/

## IDE

IntelliJ IDEA Community 2017.2
    添加GOPATH
    File -> Settings -> Language & Frameworks -> GO -> Go Libraries

## testing
    go test -v package
    go help test
    go help testflag
        -v verbose output
    go help testfunc
    xxx_test.go
        test
            func TestXxx(t *testing.T)
        benchmark
            func BenchmarkXxx(b *testing.B)
                b.N
        example
    *testing.T
        t.Log(args ...interface{})  fmt.Sprintln(args...)
        t.Fail
    *testing.B

### 未解决

* https://tour.golang.org/
* https://gobyexample.com/
* https://play.golang.org/
