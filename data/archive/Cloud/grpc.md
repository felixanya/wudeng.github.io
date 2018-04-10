# gRPC

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
    - go_out 是相对于proto文件所在的文件夹，如果跟proto文件在相同文件夹，可以直接用.
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



API Design
- Idempotency, safe to retry an RPC
- Performance
    - requests: set limits
    - response: pagination
    - avoid long-runing operations
        - background, callbacks, email, pubsub, tracking token
- Defaults
- Erros
- Error Handling
    - Don't Panic
    - Proper Propagation
- Deadlines
    - Always use deadlines
