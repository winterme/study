# tomcat安全

## 1.1配置安全

1. 删除webapps下得所有文件

2. 注释或者删除tomcat-user.xml 里面得用户权限

3. 定义错误页面，不让 404/500 等报错信息暴漏出来

4. 更改关闭tomcat指令或禁用

   tomcat.xml 得server.xml中提供了可以直接关闭tomcat实例得管理端口(8005)，可以通过telnet连接到服务器上之后，输入 shutdown 之后就可以关闭tomcat(此时进程关闭了，但是实例还在)，

解决方案：

1. ```xml
   改变关闭tomcat'的端口 和 指令
   <Server port="8015" shutdown="SHUTDOWN--ZZQ">
   ```

2. ```xml
   禁用tomcat 的关闭
   <Server port="-1" shutdown="SHUTDOWN">
   ```

## 1.2 传输安全

### 1.2.1 HTTPS 协议

​	HTTPS的全称是超文本传输安全协议（Hypertext Mranefer Protocol Secure），是一种网络安全传输协议。在HTTE的基础上加入ssL/TLS来进行数据加密，保护交换数据不被泄露、窃取。

​	SSL和TLS是用于网络通信安全的加密协议，它允许客户端和服务器之间通过安全链接通信。SSI协议的3个特性：

1）保密：通过sSL链接传输的数据时加密的。

2）鉴别：通信双方的身份鉴别，通常是可选的，单至少有一方需要验证。

3）完整性：传输数据的完整性检查。

从性能角度考虑，加解密是一项计算昂贵的处理，因为尽量不要将整个web应用采用SSL链接，实际部署过程中，选择有必要进行安全加密的页面（存在敏感信息传输的页面）采用SSL通信。



HTTPS和HTTP的区别主要为以下四点：

1）HTTPS协议需要到证书颁发机构CA申请sSL证书，然后与域名进行绑定，HTTP不用申请证书；

2）HTTP是超文本传输协议，属于应用层信息传输，HTTPS则是具有SSL加密传安全性传输协议，对数据的传输进行加密，相当于HTTP的升级版；



### 1.2.2 tomcat 支持 https

1）使用 jdk自带的 keytool 生成证书

C:\Program Files\Java\jdk1.8.0_171\bin> .\keytool.exe -genkey -alias tomcat -keyalg RSA -keystore D:/tomcatkey.keystore

![image-20200325140725262](..\images\tomcat-https)



2)  将生成的 key文件，放入 tomcat/conf 目录下

3）配置 tomcat/conf/server.xml 

```xml
<Connector port="443" protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150" SSLEnabled="true">
    <SSLHostConfig>
        <Certificate certificateKeystoreFile="conf/tomcat.keystore"
                     type="RSA" certificateKeystorePassword="123456" />
    </SSLHostConfig>
</Connector>
```
访问效果：

![image-20200325140918100](..\images\tomcat-https02)