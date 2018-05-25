# sproto

sprotodump

param

core

util

.sproto -> .spb/.cs/.go

自定义类型用.开头。可嵌套。
* string
* integer
* boolean
* 数组 *类型
* #后面是注释

每条协议由两个类型构成。
* request
* response 可选
必须是结构。不能是基本类型和数组。


## sprotoloader
* register(filename, index)
* load(index)
* save(bin, index)

## sprotoparser
* sparser.dump(str)
* sparser.parse(text, name)

## sproto.core
* core.newproto(parsedata)
* core.saveproto(sp, index)
* core.loadproto(index)

## sproto
* request_encode(protoname, tbl)
* response_encode(protoname, tbl)
* request_decode(protoname, ...)
* response_decode(protoname, ...)

```lua
local P = sproto.parse(content)

local data, tag = P:request_encode(type, obj)

local data = sproto.unpack(blob)
P:response_decode(type, data)

```


sproto:host([packagename]) create a host object to deliver the rpc message
host:dispatch(blob [,sz]) unpack and decode (sproto:pdecode) the binary string with type the host created
host:attach(sprotoobj) create a function(protoname, message, session, ud) to pack and encode request message with sprotoobj
