
http2

IDL, short for Interactive Data Language

* Unary RPCs
    rpc SayHello(HelloRequest) returns (HelloResponse){}
* Server streaming RPCs
    rpc LotsOfReplies(HelloRequest) returns (stream HelloResponse){}
* Client streaming RPCs
    rpc LotsOfGreetings(stream HelloRequest) returns (HelloResponse) {}
* Bidirectional streaming RPCs
    rpc BidiHello(stream HelloRequest) returns (stream HelloResponse){}

https://grpc.io/



需要protoc.exe以及protoc-gen-go.exe在path中。

* [protocol buffers v3](https://github.com/google/protobuf/releases) 解压，加入path
* protoc plugin
    - go get -u github.com/golang/protobuf/protoc-gen-go
* protoc -I ../helloworld --go_out=plugins=grpc:../helloworld ../helloworld/helloworld.proto
* go run greeter_server/main.go
* go run greeter_client/main.go


proto3
    service ServiceName 
        rpc RpcName (Request) returns (Reply) {}
        NewServiceNameClient
        NewServiceNameServer
    message

grpc 
    Dial(address, grpc.WithInsecure())
    
client gRPC Stub
    grpc.Dial(address, grpc.WithInsecure())

server gRPC Server
    grpc.NewServer()
