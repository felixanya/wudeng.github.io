# Protocol Buffers

.proto

proto3 gRPC

Base 128 Varints

int32
string
bool

Packed Repeated Fields version 2.1.0
    primitive numeric type
    repeated
    [packed=true]

## GO
安装插件：
go get -u github.com/golang/protobuf/protoc-gen-go

protoc -I helloworld/ helloworld/helloworld.proto --go_out=plugins=grpc:helloworld



backwards-compatible:
* you must not change the tag numbers of any existing fields
* you may delete fields
* you may add new fields but you must use fresh tag number


## Erlang

### [erlang_protobuffs](https://github.com/basho/erlang_protobuffs)

用于编译的函数为：protobuffs_compile:scanfile(ProtoFile, Options)，这个函数输入proto协议文件，
输出一个hrl头文件，一个编译好的beam文件。主要有这三个option：
- imports_dir proto文件里面import的路径列表。**注意这里是一个路径列表，而不是一个路径。** 如果只填一个字符串路径就会出错。import的时候会从这些路径中去查找对应的proto文件。
- output_include_dir 输出hrl头文件的目录。
- output_ebin_dir 输出beam文件的目录。

如果需要对应的erl文件，可以用protobuffs_compile:generate_source(ProtoFile, Options)函数。这个文件输出hrl文件和erl源文件。
- output_src_dir

编译命令行：
```
erl -pa ebin \
  -eval 'protobuffs_compile:scan_file("application/network_proto/proto/common.proto",[{imports_dir, ["application/network_proto/proto/"]}, {output_ebin_dir,"ebin"}, {output_include_dir,"include"}]).' \
  -s init stop \
  -noshell \
  -boot start_sasl
```

### [gpb](https://github.com/basho/gpb)
bin/protoc-erl 是一段escript脚本。直接调用可以打印帮助。make以后就可以调用这个脚本编译proto文件生成erl文件和头文件。

## 协议号用csv编写。相当于简单文本。直接生成erl源文件。参考riak_pb
三个函数：
msg_type(Type) -> Atom;
msg_code(Atom) -> Type;
decoder_for(Type) -> riak_pb 路由




* https://developers.google.com/protocol-buffers/
* https://github.com/google/protobuf/releases
* https://github.com/tomas-abrahamsson/gpb
