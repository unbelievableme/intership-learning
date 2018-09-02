### rpc流程
client->client stub ->network -> server stub -> server
序列化 -> NIO

### rpc和restful区别
#### restful构架
url:资源定位
method:操作
status code:操作结果

### rpc框架
#### grpc
实例：people同步workday调用rpc接口
序列化工具：protohub
- 客户端
1. client(拥有和远程的连接)主动调用rpc接口
2. 客户端和服务器端通过protohub的数据格式来进行传输，client stub对于请求数据进行编码格式化，发起请求
- 服务器端
1. 对于protohub中的service都有具体的实现
2. grpc启动server并绑定这些service维护servicename+service的map，对于到达的请求通过servicename找到service服务

#### thrift

#### dubbo

