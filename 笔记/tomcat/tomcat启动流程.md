# tomcat启动流程

## 1.1 流程

![image-20200316211450275](..\images\tomcat-start-01.jpg)



```java
/**
 * 初始化 Catalina，然后Cataline创建Server，Server初始化Services，
 * Services初始化 Executor，Engine，Connector
 * Engine 初始化Host，Host初始化Context    Connector初始化protocolHandle
 */
daemon.load(args);

/**
 * 启动， Catalina, Catalina 启动 Server，Server启动Services，
 * Services 启动Executor，启动 Engine，启动Connector
 *  Engine 启动Host，Host启动Context，    Connector启动 protocolHandle
 */
daemon.start();
```



## 1.2 源码解析

### 1.2.1 Lifecycle

由于所有的组件均存在初始化、后劫、停止等生命周期方法,押有生命周期管理的特性，所以Tomcat在没計的吋候 ，基于生命周期管理抽象成了一个接口Lifecycle , 而組件server、 service、 container、 Executor、 connector 组件，都实现了一 个生命周期的接口,从而具有了以下生命周期中的核心方法:

1) init( ) :初始化組件

2) start( ) :后劫組件

3) stop( ) :停止組件

4) destroy( ) :销毁组件

![image-20200317115136481](..\images\tom)

### 1.2.2 各组件的默认实现

上面我们提到的sexver、service、Engine、Host、Contaxc都是接口，下图中罗列了这些接口的默认实现类。当前对于Endpoint组件来说，在Tomcat中没有对应的Endpoinc接口，但是有一个抽象类AbatractEndpoint，其下有三个实现类：NioEndpoin、Nio2Endpoint、AprEndpoint，这三个实现类，分别对应于前面讲解链接器coyote时，提到的链接器支持的三种x0横型：NIO，NIO2，APR，Tomcate8.5版本中，默认采用的是NioEndpoint。

![image-20200317115723771](..\images\tomcat-start-02.jpg)

ProtocolHandler : Coyote协议接口 ,通过封装Endpoint和Processor， 实现针对具体协议的处理功能。 Tomcat按照协议和Io提供了6个实现类。

  AJP协议:

  1) AjpNioProtocol : 采用NIO的IO模型。

  2) AjpNio2Protocol :采用NIO2的IO模型。

  3) AjpAprProtocol : 采用APR的IO模型,需要依赖于ARR库。

  HTTP协议: 

  1) Http11Nioprotocol : 采用NIO的IO模型,默认使用的协议(如果服务器没有安装APR).

  2) Http11Nio2Protocol :采用NI02的IO模型。

  3) Http11AprProtocol : 采用APR的IO模型,需要依赖于APR库。

![image-20200317120317472](D:\githubWorkSpace\m2\study\笔记\images\tomcat-start-03.jpg)