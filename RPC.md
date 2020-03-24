### 一、RPC概念
什么是RPC?简单来说就是像调用本地函数或方法一样调用远程(另一个服务的，一般在另一台服务器上)函数或方法。我们先来看下本地函数调用

```
def add(x,y):
    return x + y

s = add(1,2)
print(s)
```
`add(x,y)` 使用我们一个加法函数，当用使用它时，我们直接调用即可`add(1,2)`及提供函数名和参数就可以了。
那么如果这个函数实在远程呢？

### 二、远程调用
我们来看一个简单rpc例子，使用python3的自带xmlrpc库
server.py
```
from xmlrpc.server import SimpleXMLRPCServer

# 创建一个服务
with SimpleXMLRPCServer(('localhost', 8000)) as server:

    # 定义函数add
    def add(x, y):
        return x + y
    # 注册函数add
    server.register_function(add)
    # 启动服务
    server.serve_forever()
```

client.py
```
import xmlrpc.client

s = xmlrpc.client.ServerProxy('http://localhost:8000')
print(s.add(2,3))
```

运行服务端启动服务
`python server.py`
运行客户端得到结果5
`python client.py`

注意：这里是在同一台机器上执行两个程序，当然也可以在不同的机器上执行，这是localhost就要换成sever.py运行的机器的ip

这里可以看到当客户端想使用服务端的add函数时，只需要实例化一个ServerProxy，然后调用函数与本地调用几乎相同。
xmlrpc库帮我们解决了两大问题
1. 将函数和参数封装成一个xml的消息
2. 客户端与服务端使用http协议进行通信
这两个问题对我们来说是无需关注的rpc库帮我们做了，我们只是调用函数即可,

### 三、RPC的主要技术
#### 序列化
上面的例子我们知道xmlrpc帮我们封装了消息，这个封装消息的过程就是序列化(可以简单理解为对象 –> 字节的过程)，将消息解封装的过程就是反序列化
常见序列化技术
XML、JSON、Protobuf、Thrift等

####  远程通信协议
上面xmlrpc例子使用http协议进行了通信，常见rpc通信协议有:
rmi,hessian,http,webservice,thrift等

### 四、rpc框架
什么是rpc框架？刚才我们xmlrpc就是一种简单的rpc框架
常见的rpc框架
grpc，thrift，dubbo等

这里注意，比如thrift。因为thrift自己实现了自己序列化方式(thrift),远程通信方式(thrift),所以既有thrift序列化，又有thrift通信协议





