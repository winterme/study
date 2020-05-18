# Socket5 代理

socket是什么

有两个程序，通过一个双向的通信连接实现数据交换，这个连接的一端就是一个soccet，也叫【套接字】；socket包括IP+端口，一个连接至少需要两个socket，因为连接的一端就是一个socket。

一个应用程序通过一个socket和其他应用程序建立连接；

socket用于描述IP地址和端口，是一个通信链的句柄，可以用来实现不同虚拟机或不同计算机之间的通信。在Internet上的主机一般运行了多个服务软件，同时提供几种服务。每种服务都打开一个Socket，并绑定到一个端口上，不同的端口对应于不同的服务。

 

socket5是什么？

socket5 是一种代理协议，实际上是一个传输层代理协议，比http协议底层。

SOCKS5 是一个代理协议，它在使用TCP/IP协议通讯的前端机器和服务器机器之间扮演一个中介角色，使得内部网中的前端机器变得能够访问Internet网中的服务器，或者使通讯更加安全。SOCKS5 服务器通过将前端发来的请求转发给真正的目标服务器， 模拟了一个前端的行为。在这里，前端和SOCKS5之间也是通过TCP/IP协议进行通讯，前端将原本要发送给真正服务器的请求发送给SOCKS5服务器，然后SOCKS5服务器将请求转发给真正的服务器。

SOCKS5服务器在将通讯请求发送给真正服务器的过程中，对于请求数据包本身不加任何改变。SOCKS5服务器接收到真正服务器的响应后，也原样转发给前端。因此，SOCKS5 协议是一种代理协议，对于各种基于 TCP/IP的应用层协议都能够适应，几乎是万能的。它虽然不能理解自己转发的数据的内部结构，但是它能够忠实地转发通讯包，完成协议本来要完成的功能。

 

socket5正向代理流程如下：

client 《----------使用TCP/IP协议通信---------》socket5代理服务器 《------使用TCP/IP协议通信-----》后端真正服务器

 

 

socket5代理和https代理(例如timyproxy)区别？

socket5代理服务器：使用TCP/IP协议通信。SOCKet5服务器在将通讯请求发送给真正服务器的过程中，对于请求数据包本身不加任何改变。SOCKS5服务器接收到真正服务器的响应后，也原样转发给前端。

 

http代理服务器：与SOCKS5协议不同，HTTP代理是通过HTTP协议进行的，HTTP代理服务器软件了解通讯包的内部结构，在转发过程中还要对通讯进行某种程度的修改和转换。和HTTP代理协议不同，SOCKS5实际上是一个传输层的代理协议。我们可以想象，如果每个具体的应用层协议都要设计对应的代理协议表达办法，一个特定的代理服务器无论如何也支持不过来那么多新出现的协议。因此，可以说SOCKS5的出现缓解了各种具体协议需要专门设计代理协议的困难局面。不过，并不是凡是使用基于TCP/IP协议的应用协议的软件，都可以无条件地透过SOCKS5服务器进行通讯，还要求前端软件本身具有SOCKS5的接口，才能利用SOCKS5代理服务器。

 

centos7.4搭建socket5代理服务器

一、安装依赖

# yum install pam-devel openldap-devel openssl-devel

二、安装socket5

# wget http://downloads.sourceforge.net/project/ss5/ss5/3.8.9-8/ss5-3.8.9-8.tar.gz

# tar -xzvf ss5-3.8.9-8.tar.gz

# cd ss5-3.8.9

# ./configure

# make

# make install

三、配置socket5代理服务器

1.安装成功后配置文件位置：

[root@test-01 ~]# cd /etc/opt/ss5/

[root@test-01 ss5]# ls

ss5.conf ss5.ha ss5.passwd

[root@test-01 ss5]# pwd

/etc/opt/ss5

2.修改配置文件，不使用密码：

# vim /etc/opt/ss5/ss5.conf

auth 0.0.0.0/0 - -

permit - 0.0.0.0/0 - 0.0.0.0/0 - - - - -

 

3.u：使用ss5.passwd帐号密码登录，-：默认任何人都可使用

如果需要配置客户端连接这个服务器需要密码的话，配置如下：

auth 0.0.0.0/0 - u

permit u 0.0.0.0/0 - 0.0.0.0/0 - - - - -

 

配置用户、密码：

# vim /etc/opt/ss5/ss5.passwd

##用户  密码

ttlsa 123456

 

4.默认端口是1080，修改默认的链接端口

vim /etc/sysconfig/ss5

在/etc/sysconfig/ss5这个文件中，添加下面这一行命令，-b后面的参数代表监听的ip地址和端口号

SS5_OPTS=" -u root -b 0.0.0.0:8080"

 

四、 启动socket 5

# sh /etc/rc.d/init.d/ss5 start

doneting ss5 ...

默认情况ss5文件没有执行权限，如果觉得使用sh来启动麻烦，那么按如下方法：

# chmod u x /etc/rc.d/init.d/ss5

# chkconfig --add ss5 //可选

# chkconfig ss5 on //可选

# service ss5 start

 

五、测试：

1.使用qq客户端 socket5代理

2.使用python程序：

 

import socket # 内置库

import requests # 需要安装：pip install requests

import socks # 需要安装：pip install pysocks

 

# 代理服务器IP（域名）

socks5_proxy_host = 'xxxxxxxxx'

# 代理服务器端口号

socks5_proxy_port = 8888

 

# 设置代理

socks.set_default_proxy(socks.SOCKS5,socks5_proxy_host,socks5_proxy_port)

socket.socket = socks.socksocket

 

# 某个必须通过代理才能访问的网站

url = 'https://www.qlchat.com/'

resp = requests.get(url)

 

# 查看响应结果

 

print(resp.status_code)

print(resp.text)

 

在socket5代理服务器上进行抓包验证：

tcpdump -i any port 8888 -w test.cap

 

————————————————
版权声明：本文为CSDN博主「oOo右右」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/JOJOY_tester/article/details/89411532







![image-20200516113305027](..\images\℃-01)





![image-20200516113402478](..\images\ss5-02)