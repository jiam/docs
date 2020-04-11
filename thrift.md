### thrift
thrift 是一个支持跨语言通信的RPC框架，Thrift为数据传输，数据序列化和应用程序级处理提供了抽象和实现。其代码生成系统将简单的定义语言作为输入，并跨编程语言生成代码，使不同语言能够构建可互操作的RPC客户端和服务器。

### thrift网络栈

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

####  Transport 传输层
传输层为从网络读取/向网络写入提供了简单的抽象。这使Thrift可以将基础传输与系统的其余部分解耦（例如，序列化/反序列化）。

#### Protocol 协议
协议抽象定义了一种将内存中的数据结构映射为有线格式的机制。换句话说，协议指定数据如何进行编码/解码即序列化和反序列化

#### Processor 处理器
处理器封装了从输入流读取数据并写入输出流的功能。

#### Server 服务器
服务器将上述所有各种功能汇总在一起:
+ 创建一个传输
+ 创建用于传输的输入/输出协议
+ 根据输入/输出协议创建处理器
+ 等待传入的连接并将其交给处理器

### IDL
IDL（Interface Description Language）通过一种中立的方式来描述接口，使得在不同平台上运行的对象和用不同语言编写的程序可以相互通信交流。比如，一个组件用C++写成，另一个组件用Java写成。

Thrift接口定义语言（IDL）允许定义Thrift类型。Thrift IDL文件被Thrift代码生成器处理以生成用于各种目标语言的代码，代码中包含IDL文件中定义的结构和服务。


### python例子

#### 安装thrift编译器

thrift 命令可以根据idl文件直接生成相应语言的代码
+ winows
https://downloads.apache.org/thrift/0.13.0/thrift-0.13.0.exe

+ macos
`brew install thrift`

#### 安装thrift 模块
`pip install thrift`

#### 使用thrift编译器生成python代码
`thrift.exe -out . --gen py tutorial.thrif`
+ `-out .`  指定生成代码的目录为当前目录
+ `--gent py` 生成python语言的thrift代码
+ `tutorial.thrif`  指定idl文件

该命令就是根据指定idl文件生成python相关代码放在tutorial目录

#### 运行服务端
```
python .\PythonServer.py
Starting the server...
```

#### 运行客户端
```
python .\PythonClient.py
ping()
1+1=2
```

[源码地址](https://github.com/jiam/docs/tree/master/thrift)









