# Makefile 那些事儿



## 变量
* `=` Lazy Set
* `:=` Immediate Set
* `?=` Set If Abasent
* `+=` Append

使用`$(variable)`或者`${variable}`来引用变量。简单的文本替换。

objects = program.o foo.o
program : $(objects)

windows下可以直接通过变量设置环境变量：

`Path = C:\Program Files\erl9.0\erts-9.0\bin;%Path%`

Substitution Reference
`$(var:a=b)` take the value of the variable var, replace every a at the end of a word with b in that value, and substitute the resulting string.

$(patsubst pattern,replacement,text)



$(var:pattern=replacement)   ==  $(patsubst pattern,replacement,$(var))

Finds whitespace-separated words in text that match pattern and replaces them with replacement. Here pattern may contain a ‘%’ which acts as a wildcard, matching any number of any characters within a word. If replacement also contains a ‘%’, the ‘%’ is replaced by the text that matched the ‘%’ in pattern.

## 依赖生成
生成文件依赖。
g++ -MM main.c > main.d

```
DEP=$(SRC:.c=.d)
-include $(DEP)
```
减号表示允许被包含的文件不存在。

## 回显
默认会输出执行的命令，如果不想输出，可以在命令前加`@`.


## .PHONY 目标

Make是为了解决文件依赖的，默认的目标都是文件。PHONY就是要告诉Make，这是一个假目标而不是文件目标，
总是执行规则。比如clean，就算你有一个clean的文件在当前目录下，make clean仍然会执行。

## 宏
CC=gcc -Wall -c
LD=gcc

`$@` : 依赖的对象
$^ : 被依赖的对象，依赖列表的所有文件
$< : 依赖列表的第一个文件



通配符：
```makefile
%o :%c
  $(CC) $^ -o $@
foo.o : foo.c
woo.o : woo.c
main.o : main.c
```
gnumake默认如果.c存在，.o就依赖对应的.c，而.o到.c的rule，是通过宏默认定义的。
你只要修改CC，LDLIBS这类的宏，就能解决大部分问题了。
```makefile
SRC=$(wildcard *.c)
OBJ=$(SRC:.c=.o)
```

可以不写makefile直接make，但是要显式指定目标。比如文件main.cpp，直接`make main`即可。
尝试了一下4.2版本下是支持的。但是windows下3.81版本不支持没有Makefile直接make。


存在的问题，如果有proto文件依赖其他proto，这个简单的规则就不适用了。
```Makefile
PROTO_DIR=application/network_proto/proto
PROTOS:=$(wildcard $(PROTO_DIR)/*.proto)
BEAMS:=$(PROTOS:$(PROTO_DIR)/%.proto=ebin/%_pb.beam)


all: $(BEAMS)
	@erl -make

$(BEAMS):ebin/%_pb.beam:$(PROTO_DIR)/%.proto
	@erl -pa ebin \
		-eval 'lib_common:compile_file("$^").' \
		-s init stop \
		-noshell

```



https://www.gnu.org/software/make/manual/html_node/index.html#SEC_Contents
