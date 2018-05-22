# sproto

sprotodump

param

core

util

.sproto -> .spb/.cs/.go

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