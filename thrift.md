### 一、thrift
thrift 是一个支持跨语言通信的RPC框架，Thrift为数据传输，数据序列化和应用程序级处理提供了抽象和实现。其代码生成系统将简单的定义语言作为输入，并跨编程语言生成代码，使不同语言能够构建可互操作的RPC客户端和服务器。

### 二、thrift网络栈

```
  +-------------------------------------------+
  | Server                                    |
  | (single-threaded, event-driven etc)       |
  +-------------------------------------------+
  | Processor                                 |
  | (compiler generated)                      |
  +-------------------------------------------+
  | Protocol                                  |
  | (JSON, compact etc)                       |
  +-------------------------------------------+
  | Transport                                 |
  | (raw TCP, HTTP etc)                       |
  +-------------------------------------------+
```

####  1. Transport 传输层
传输层为从网络读取/向网络写入提供了简单的抽象。这使Thrift可以将基础传输与系统的其余部分解耦（例如，序列化/反序列化）。

#### 2. Protocol 协议
协议抽象定义了一种将内存中的数据结构映射为有线格式的机制。换句话说，协议指定数据如何进行编码/解码即序列化和反序列化

#### 3. Processor 处理器
处理器封装了从输入流读取数据并写入输出流的功能。

#### 4. Server 服务器
服务器将上述所有各种功能汇总在一起:
+ 创建一个传输
+ 创建用于传输的输入/输出协议
+ 根据输入/输出协议创建处理器
+ 等待传入的连接并将其交给处理器

### 三、IDL
IDL（Interface Description Language）通过一种中立的方式来描述接口，使得在不同平台上运行的对象和用不同语言编写的程序可以相互通信交流。比如，一个组件用C++写成，另一个组件用Java写成。

Thrift接口定义语言（IDL）允许定义Thrift类型。Thrift IDL文件被Thrift代码生成器处理以生成用于各种目标语言的代码，代码中包含IDL文件中定义的结构和服务。


### 四、python例子

#### 1. 安装thrift编译器

thrift 命令可以根据idl文件直接生成相应语言的代码
+ winows
https://downloads.apache.org/thrift/0.13.0/thrift-0.13.0.exe

+ macos
`brew install thrift`

#### 2. 安装thrift 模块
`pip install thrift`

#### 3. 使用thrift编译器生成python代码
`thrift.exe -out . --gen py tutorial.thrif`
+ `-out .`  指定生成代码的目录为当前目录
+ `--gent py` 生成python语言的thrift代码
+ `tutorial.thrif`  指定idl文件

该命令就是根据指定idl文件生成python相关代码放在tutorial目录,这些代码我们将在服务端和客户端中使用。它们用来定义数据类型，服务和方法。

#### 4. 运行服务端
PythonServer.py
```
from tutorial import Calculator


from thrift.transport import TSocket
from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol
from thrift.server import TServer


class CalculatorHandler:
    def __init__(self):
        self.log = {}

    def ping(self):
        print('ping()')

    def add(self, n1, n2):
        print('add(%d,%d)' % (n1, n2))
        return n1 + n2

if __name__ == '__main__':
    handler = CalculatorHandler()
    processor = Calculator.Processor(handler)
    transport = TSocket.TServerSocket(host='127.0.0.1', port=9090)
    tfactory = TTransport.TBufferedTransportFactory()
    pfactory = TBinaryProtocol.TBinaryProtocolFactory()

    server = TServer.TSimpleServer(processor, transport, tfactory, pfactory)

    print('Starting the server...')
    server.serve()
    print('done.')

```


`python .\PythonServer.py`


#### 5. 运行客户端
```

from tutorial import Calculator


from thrift import Thrift
from thrift.transport import TSocket
from thrift.transport import TTransport
from thrift.protocol import TBinaryProtocol


def main():
    # 创建socket，连接localhost的9090端口
    transport = TSocket.TSocket('localhost', 9090)

    # 使用带buffer 传输层协议
    transport = TTransport.TBufferedTransport(transport)

    # 使用TBinaryProtocol protocal
    protocol = TBinaryProtocol.TBinaryProtocol(transport)

    # 创建client
    client = Calculator.Client(protocol)

    # 连接服务端
    transport.open()
    # 调用服务ping方法
    client.ping()
    print('ping()')
    # 调用服务端add方法
    sum_ = client.add(1, 1)
    print('1+1=%d' % sum_)


    # 关闭连接
    transport.close()


if __name__ == '__main__':
    try:
        main()
    except Thrift.TException as tx:
        print('%s' % tx.message)

```

`python .\PythonClient.py`

[源码地址](https://github.com/jiam/docs/tree/master/thrift)









