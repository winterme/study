# tomcat服务器配置

tomcat服务器配置主要集中在 tomcat/conf 目录下的配置文件中。

## 1.1 server.xml 配置

server.xml 是tomcat 的核心配置之一，包含了tomcat servlet容器(Catalina) 的所有配置。

### 1.1.1 Server

Server 是 server.xml 的根元素，用于创建一个Server 实例，默认使用 StandardServer 类。

```xml
<Server port="8005" shutdown="SHUTDOWN">
...
</Server>
```

port：Tomcat 监听的关闭服务器的端口

shutdown：关闭服务器的指令字符串

Server 内嵌的listener

![image-20200319204152836](..\images\tomcat-config-01.jpg)

![image-20200319204308619](..\images\tomcat-config-02.jpg)



### 1.1.2 Service

该元素用于创建 Service 实例，默认使用 StandardService 类，默认情况下，Tomcat 仅指定了Service的名称，值为“Catalina”，Service可内嵌的元素是：“Listener，Executor，Connector，Engine”，其中，Listener用于Service的生命周期添加监视器，Executor 用于配置Service的共享线程池，Connector用于配置Service配置的链连接器，Engine 用于配置Service中链连接器对应的Servlet引擎。

```xml
<Service name="Catalina">
    <Listener className="zzq.listener.MyListener" ></Listener>
    <Executor name="tomcatThreadPool"
                  namePrefix="catalina-exec-"
                  maxThreds="1000"
                  minSpareThreads="100"
                  maxIdleTime="60000"
                  maxQueueSize="Integer.MAX_VALUE"
                  prestartminSpareThreads="false"
                  threadPriority="5"
                  className="org.apache.catalina.core.StandardThreadExecutor"
            ></Executor>
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>
    <Engine name="Catalina" defaultHost="localhost">
	...
    </Engine>
</Service>
```

一个Server可以包含多个Service。

### 1.1.3 Executor

```xml
<Executor name="tomcatThreadPool"
              namePrefix="catalina-exec-"
              maxThreds="1000"
              minSpareThreads="100"
              maxIdleTime="60000"
              maxQueueSize="Integer.MAX_VALUE"
              prestartminSpareThreads="false"
              threadPriority="5"
              className="org.apache.catalina.core.StandardThreadExecutor"
        ></Executor>
```
属性说明

![image-20200319211351847](..\images\tomcat-config-03.jpg)



### 1.1.4 Connector

Connector 用于创建链接器实例，默认情况下，server.xml 配置了两个 Connector，一个是支持HTTP协议的，一个是支持AJP协议。

```xml
<Connector port="8080" protocol="HTTP/1.1" executor="tomcatThreadPool"
           connectionTimeout="20000"
           redirectPort="8443"/>
<Connector port="8009" executor="tomcatThreadPool" protocol="AJP/1.3" redirectPort="8443"/>
```

![image-20200319212920823](..\images\tomcat-config-04.jpg)

![image-20200319213011612](..\images\tomcat-config-05.jpg)

### 1.1.5 HOST

​	Host元素用于配置-一个虚拟主机，它支持以下嵌入元素: Alias、Cluster. Listener、 valve、Realm、Context。 如果在Engine'下配置Realm，那么此配置将在当前Engine 下的所有Host中共享。同样 ,如果在Host中配置Realm，则在当前Host 下的所有Context中共享。Context中的Realm优先级> Host 的Realm优先级> Engine中的Realm优先级。



```xml
<Host name="lcm.zzq" appBase="D:\jar\xxx"
      unpackWARs="true" autoDeploy="true">
    <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
           prefix="localhost_access_log" suffix=".txt"
           pattern="%h %l %u %t &quot;%r&quot; %s %b"/>

</Host>
```

属性说明:

1) name: 当前Host通用的网络名称，必须与DNS服务器 上的注册信息-致。Engi ne中包含的Host必须存在一个名称与Engine的defaultHost设置-致。

2) appBase: 当前Host的应用基础目录， 当前Host.上部署的web应用均在该目录下(可以是绝对目录,相对路径)。默认为webapps.

3) unpackWARs :设置为true，Host在启动时会将appBase目录下war包解压为目录。 设置为false, Host将直接从war文件启动。

4) autoDeploy :控制tomcat是否在运行时定期检测并自动部署新增或变更的web应用。



![image-20200320092219304](..\images\tomcat-config-06.jpg)

### 1.1.6 Context

Context 用于配置一个web应用，属于Host的子标签，一个HOST下面可以有多个Context。

示例：

```xml
<Context docBase="D:\BaiduNetdiskDownload\HTML5网站模板\移动模版140套\【很适合新手学习】仿coffee手机wap企业网站模板下载\\" path="/coffee" >

</Context>
```

属性描述：

1. docBase：项目的根路径，可以是相对，也可以是绝对路径。
2. path：项目访问路径，/coffee

