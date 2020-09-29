### ([详解Tomcat系列博文](https://www.cnblogs.com/tanshaoshenghao/p/10932306.html))



### 一、基础知识（Servlet 和 HTTP协议）

#### 1、HTTP协议

#### 2、Servlet规范

#### 3、web容器

### 二、整体架构

#### 1、Tomcat系统架构

我们知道如果要设计一个系统，首先是要了解需求。通过专栏前面的文章，我们已经了解了 Tomcat 要实现 2 个核心功能：

- 处理 Socket 连接，负责网络字节流与 Request 和 Response 对象的转化。
- 加载和管理 Servlet，以及具体处理 Request 请求。

**因此 Tomcat 设计了两个核心组件连接器（Connector）和容器（Container）来分别做这两件事情。连接器负责对外交流，容器负责内部处理。**

在开始讲连接器前，我先铺垫一下 Tomcat 支持的多种 I/O 模型和应用层协议。



Tomcat 支持的 I/O 模型有：

- NIO：非阻塞 I/O，采用 Java NIO 类库实现。
- NIO.2：异步 I/O，采用 JDK 7 最新的 NIO.2 类库实现。
- APR：采用 Apache 可移植运行库实现，是 C/C++ 编写的本地库。

Tomcat 支持的应用层协议有：

- HTTP/1.1：这是大部分 Web 应用采用的访问协议。
- AJP：用于和 Web 服务器集成（如 Apache）。
- HTTP/2：HTTP 2.0 大幅度的提升了 Web 性能。



> Tomcat 为了实现支持多种 I/O 模型和应用层协议，一个容器可能对接多个连接器。但是单独的连接器或者容器都不能对外提供服务，需要把它们组装起来才能工作，组装后这个整体叫作 Service 组件。这里请你注意，Service 本身没有做什么重要的事情，只是在连接器和容器外面多包了一层，把它们组装在一起。Tomcat 内可能有多个 Service，这样的设计也是出于灵活性的考虑。通过在 Tomcat 中配置多个 Service，可以实现通过不同的端口号来访问同一台机器上部署的不同应用

<img src=".\image\tomcat\ee880033c5ae38125fa91fb3c4f8cad6.jpg" alt="ee880033c5ae38125fa91fb3c4f8cad6" style="zoom:33%;" />

> 从图上你可以看到，最顶层是 Server，这里的 Server 指的就是一个 Tomcat 实例。一个 Server 中有一个或者多个 Service，一个 Service 中有多个连接器和一个容器。连接器与容器之间通过标准的 ServletRequest 和 ServletResponse 通信。



##### 1、连接器

连接器对 Servlet 容器屏蔽了协议及 I/O 模型等的区别，无论是 HTTP 还是 AJP，在容器中获取到的都是一个标准的 ServletRequest 对象。我们可以把连接器的功能需求进一步细化，比如：

- 监听网络端口。
- 接受网络连接请求。
- 读取网络请求字节流。
- 根据具体应用层协议（HTTP/AJP）解析字节流，生成统一的 Tomcat Request 对象。
- 将 Tomcat Request 对象转成标准的 ServletRequest。
- 调用 Servlet 容器，得到 ServletResponse。
- 将 ServletResponse 转成 Tomcat Response 对象。
- 将 Tomcat Response 转成网络字节流。
- 将响应字节流写回给浏览器。

需求列清楚后，我们要考虑的下一个问题是，连接器应该有哪些子模块？优秀的模块化设计应该考虑**高内聚、低耦合**。

- 高内聚是指相关度比较高的功能要尽可能集中，不要分散。
- 低耦合是指两个相关的模块要尽可能减少依赖的部分和降低依赖的程度，不要让两个模块产生强依赖。

通过分析连接器的详细功能列表，我们发现连接器需要完成 3 个高内聚的功能：

- 网络通信。
- 应用层协议解析。
- Tomcat Request/Response 与 ServletRequest/ServletResponse 的转化。



因此 Tomcat 的设计者设计了 3 个组件来实现这 3 个功能，分别是 ：

- Endpoint

  Endpoint 是通信端点，即通信监听的接口，是具体的 Socket 接收和发送处理器，是对传输层的抽象，因此 Endpoint 是用来实现 TCP/IP 协议的。

  

  Endpoint 是一个接口，对应的抽象实现类是 AbstractEndpoint，而 AbstractEndpoint 的具体子类，比如在 NioEndpoint 和 Nio2Endpoint 中，有两个重要的子组件：Acceptor 和 SocketProcessor。

  

  其中 Acceptor 用于监听 Socket 连接请求。SocketProcessor 用于处理接收到的 Socket 请求，它实现 Runnable 接口，在 run 方法里调用协议处理组件 Processor 进行处理。为了提高处理能力，SocketProcessor 被提交到线程池来执行。而这个线程池叫作执行器（Executor)，我在后面的专栏会详细介绍 Tomcat 如何扩展原生的 Java 线程池。

- Processor 

-  Adapter

<img src=".\image\tomcat\13850ee56c3f09cbabe9892e84502155.jpg" alt="13850ee56c3f09cbabe9892e84502155" style="zoom: 33%;" />





连接器模块用三个核心组件：Endpoint、Processor 和 Adapter 来分别做三件事情，其中 **Endpoint 和 Processor 放在一起抽象成了 ProtocolHandler 组件**，它们的关系如下图所示

<img src="G:\personal\learning-md\image\tomcat\6eeaeb93839adcb4e76c15ee93f545ce.jpg" alt="6eeaeb93839adcb4e76c15ee93f545ce" style="zoom:33%;" />



#### 2、Tomcat容器设计



**tomcat整体设计**

<img src=".\image\tomcat\ee880033c5ae38125fa91fb3c4f8cad6.jpg" alt="ee880033c5ae38125fa91fb3c4f8cad6" style="zoom:33%;" />



**容器的层次结构**

<img src="G:\personal\learning-md\image\tomcat\cc968a11925591df558da0e7393f06ed.jpg" alt="cc968a11925591df558da0e7393f06ed" style="zoom:33%;" />

**标准Server.xml配置**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">
	<Service name="Catalina">
    	<Connector /> 
        <Engine >
            <Realm ></Realm>
            <Host >
                <Valve/>
                <Context>
                    <Wrapper></Wrapper>
                </Context>
            </Host>
    </Engine>
  </Service>
</Server>
```



- Engine
- Host
- Context
- Wrapper (本质是Servlet)





Valve执行过程

<img src=".\image\tomcat\b014ecce1f64b771bd58da62c05162ca.jpg" alt="b014ecce1f64b771bd58da62c05162ca" style="zoom:33%;" />



#### 加速Tomcat启动

- 清理不必要的**WEB**应用

-  清理 XML 配置文件

- 清理 JAR 文件

  > 我们还可以删除所有不需要的 JAR 文件。JVM 的类加载器在加载类时，需要查找每一个 JAR 文件，去找到所需要的类。如果删除了不需要的 JAR 文件，查找的速度就会快一些。这里请注意：**Web 应用中的 lib 目录下不应该出现 Servlet API 或者 Tomcat 自身的 JAR**，这些 JAR 由 Tomcat 负责提供。如果你是使用 Maven 来构建你的应用，对 Servlet API 的依赖应该指定为<scope>provided</scope>。

- 清理其他文件

  > 及时清理日志，删除 logs 文件夹下不需要的日志文件。同样还有 work 文件夹下的 catalina 文件夹，它其实是 Tomcat 把 JSP 转换为 Class 文件的工作目录。有时候我们也许会遇到修改了代码，重启了 Tomcat，但是仍没效果，这时候便可以删除掉这个文件夹，Tomcat 下次启动的时候会重新生成.

- 禁止 Tomcat TLD 扫描

  



### 三、连接器

**LINUX系统5中IO模型：**

- 同步阻塞I/O模型（Blocking IO）
- 同步非阻塞模型（Non-Blocking IO）
- 多路复用模型（multiplexing IO）
- 信号驱动式I/O模型（Signal-driven IO）
- 异步I/O模型（asynchronous  IO）

**什么是I/O?**

> 所谓的 I/O 就是计算机内存与外部设备之间拷贝数据的过程。我们知道 CPU 访问内存的速度远远高于外部设备，因此 CPU 是先把外部设备的数据读到内存里，然后再进行处理

**Java I/O 模型**

> 对于一个网络 I/O 通信过程，比如网络数据读取，会涉及两个对象，一个是调用这个 I/O 操作的用户线程，另外一个就是操作系统内核。一个进程的地址空间分为用户空间和内核空间，用户线程不能直接访问内核空间。当用户线程发起 I/O 操作后，网络数据读取操作会经历两个步骤：用户线程等待内核将数据从网卡拷贝到内核空间。内核将数据从内核空间拷贝到用户空间。各种 I/O 模型的区别就是：它们实现这两个步骤的方式是不一样的。

​	



### 四、容器

### 五、通用组件

### 六、性能优化

