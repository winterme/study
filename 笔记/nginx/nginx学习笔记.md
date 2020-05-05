# nginx学习笔记

nginx 官网： http://nginx.org/en/docs/http/load_balancing.html

## 1. nginx 基本概念

### (1) nginx 是什么，做什么事情

​		Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。

### (2) 反向代理

* 正向代理
  * 比如说你想访问 google.com 发现访问不了，然后你通过某一个境外服务器来代理 google.com 然后再去访问 http://xxx.com/google/  这就是正向代理，比如说 vpn 就是个很典型的正向代理
* 反向代理
  * 客户端对代理是无感的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器自动去选择目标服务器，然后获取数据之后，返回给客户端。此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器的地址，隐藏了真实ip。![image-20200426154345584](..\images\nginx-01)

### (3) 负载均衡

![image-20200426160531624](..\images\nginx-02)

### (4) 动静分离

​		为了加快服务器的响应能力，将 静态资源和动态资源交给不同的服务器处理。加快解析速度，降低原来单个服务器的压力。

![image-20200426161855126](..\images\nginx-03)

## 2. nginx 常用命令

```
[root@CubeCloud-2019113523 sbin]# ./nginx -h
nginx version: nginx/1.13.11
Usage: nginx [-?hvVtTq] [-s signal] [-c filename] [-p prefix] [-g directives]

Options:
  -?,-h         : this help
  -v            : show version and exit
  -V            : show version and configure options then exit
  -t            : test configuration and exit
  -T            : test configuration, dump it and exit
  -q            : suppress non-error messages during configuration testing
  -s signal     : send signal to a master process: stop, quit, reopen, reload
  -p prefix     : set prefix path (default: /usr/local/nginx1.3//)
  -c filename   : set configuration file (default: conf/nginx.conf)
  -g directives : set global directives out of configuration file

```



### (1) nginx 查看版本信息

sbin/nginx -v

```
[root@CubeCloud-2019113523 sbin]# ./nginx -v
nginx version: nginx/1.13.11
```

### (2) 启动 nginx

./nginx

指定配置文件路径：./nginx -c [路径]

### (3) 关闭 nginx

./nginx -s stop

### (4) 重新加载 nginx

./nginx -s reload



## 3. nginx 配置文件

#### 1. nginx 配置文件构成

##### 1.1 全局 块

从 配置文件开始到 events 块之间的代码，主要会设置一些会影响到nginx服务器整体运行的配置指令。

	#user  nobody;
	worker_processes  1;	
	
	#error_log  logs/error.log;
	#error_log  logs/error.log  notice;
	#error_log  logs/error.log  info;
	
	#pid        logs/nginx.pid;
worker_processes  1;	比如说这个：这是 nginx 服务器的并发处理的关键配置，这里的数字越大，nginx能处理的并发越多，但是收到硬件，软件的制约。

##### 1.2 events 块

	events {
		worker_connections  1024;
	}



​	events块涉及的指令主要影响Nginx服务器与用户的网络连接，常用的设置包括是否开启对多 work process
下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个word
process可以同时支持的最大连接数等。

​	上述例子就表示每个work process支持的最大连接数为1024.
​	这部分的配置对Nginx的性能影响较大，在实际中应该灵活配置。

##### 1.3 http 块

​	http 全局块配置包含文件引入，MIME-TYPE 定义，日志自定义，连接超时，单链接请求上限。

	http {
		include       mime.types;
		default_type  application/octet-stream;
	
		#log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
		#                  '$status $body_bytes_sent "$http_referer" '
		#                  '"$http_user_agent" "$http_x_forwarded_for"';
	
		#access_log  logs/access.log  main;
	
		sendfile        on;
		#tcp_nopush     on;
	
		#keepalive_timeout  0;
		keepalive_timeout  65;
	
		#gzip  on;
	
		server {
			listen       80;
			server_name  localhost;
	
			#charset koi8-r;
	
			#access_log  logs/host.access.log  main;
	
			location / {
				root   html;
				index  index.html index.htm;
			}
	
			#error_page  404              /404.html;
	
			# redirect server error pages to the static page /50x.html
			#
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   html;
			}
	
			# proxy the PHP scripts to Apache listening on 127.0.0.1:80
			#
			#location ~ \.php$ {
			#    proxy_pass   http://127.0.0.1;
			#}
	
			# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
			#
			#location ~ \.php$ {
			#    root           html;
			#    fastcgi_pass   127.0.0.1:9000;
			#    fastcgi_index  index.php;
			#    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
			#    include        fastcgi_params;
			#}
	
			# deny access to .htaccess files, if Apache's document root
			# concurs with nginx's one
			#
			#location ~ /\.ht {
			#    deny  all;
			#}
		}


		# another virtual host using mix of IP-, name-, and port-based configuration
		#
		#server {
		#    listen       8000;
		#    listen       somename:8080;
		#    server_name  somename  alias  another.alias;
	
		#    location / {
		#        root   html;
		#        index  index.html index.htm;
		#    }
		#}


		# HTTPS server
		#
		#server {
		#    listen       443 ssl;
		#    server_name  localhost;
	
		#    ssl_certificate      cert.pem;
		#    ssl_certificate_key  cert.key;
	
		#    ssl_session_cache    shared:SSL:1m;
		#    ssl_session_timeout  5m;
	
		#    ssl_ciphers  HIGH:!aNULL:!MD5;
		#    ssl_prefer_server_ciphers  on;
	
		#    location / {
		#        root   html;
		#        index  index.html index.htm;
		#    }
		#}
	
	}
###### 1.3.1 server 块

​		这块和虚拟主机有密切的关系，虚拟主机从用户的角度来看，和一台独立的硬件主机是一模一样的。该技术是为了节约互联网硬件成本。

​		每个 http 块可以包含多个 server 块，而每个 server 就相当于一个虚拟主机。

​		而每个 server 块也分为全局server 块，以及可以同时包含多个 location 块。

​		**1.3.1.1 全局server**

​		最常见的配置是本虚拟主机的监听配置，和本虚拟主机的名称或IP配置。

​		**1.3.1.2 全局location**

​		一个 server 块可以配置多个 location 块。

​		这块的主要做用是基于 NGINX 服务器接收到的请求字符串(例如 /xx/uri-string) , 对虚拟主机名称(也可以是 IP 别名)之外的字符串 进行匹配，对特定的请求进行处理，地址定向，数据缓存，和应答控制等功能，还有许多第三方的模板也在这里进行。

​		

 ## 4. nginx 反向代理

![image-20200503142847087](..\images\nginx-04)

客户端，输入 www.123.com , 请求nginx服务器，nginx转发给本地的 tomcat来处理请求

### 4.1 nginx 配置

在 server 块里面配置 location 块。

比如这样，然后重启nginx，访问 www.123.com 请求就交给 http://192.168.1.105:8091/ 来处理了

		location /dfs/ {
			proxy_pass http://192.168.1.105:8091/;
			proxy_set_header Host                $host:$server_port;
			proxy_set_header X-Forwarded-For     $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto   $scheme;
			proxy_set_header X-Forwarded-Port    $server_port;
		}


www.123.com/dfs/  请求到 nginx 转发到 http://192.168.1.105:8091/

www.123.com/user-center/  请求到 nginx 转发到 http://192.168.1.105:8081/user-center/



	server {
			listen       80;
			server_name  localhost;
	
			location / {
				root   /usr/share/nginx/html;
				index  index.html index.htm;
			}
			
			location /dfs/ {
				proxy_pass http://192.168.1.105:8091/;
				proxy_set_header Host                $host:$server_port;
				proxy_set_header X-Forwarded-For     $proxy_add_x_forwarded_for;
				proxy_set_header X-Forwarded-Proto   $scheme;
				proxy_set_header X-Forwarded-Port    $server_port;
			}
			
			location /user-center/ {
				proxy_pass http://192.168.1.105:8081/user-center/;
				proxy_set_header Host                $host:$server_port;
				proxy_set_header X-Forwarded-For     $proxy_add_x_forwarded_for;
				proxy_set_header X-Forwarded-Proto   $scheme;
				proxy_set_header X-Forwarded-Port    $server_port;
			}
			
			location /user-center/biz/ {
				root /usr/share/nginx/html/;
			}
			
			location /user-center/static/ {
				root /usr/share/nginx/html/;
			}
			
			location ~ /http {
				proxy_pass http://192.168.1.105:8080;
				proxy_set_header Host                $host:$server_port;
				proxy_set_header X-Forwarded-For     $proxy_add_x_forwarded_for;
				proxy_set_header X-Forwarded-Proto   $scheme;
				proxy_set_header X-Forwarded-Port    $server_port;
			}
			
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   /usr/share/nginx/html;
			}
		}
##### 4.1.1 location 配置

1.  = :用于不含正则表达式的uri 前，要求请求字符串与uri严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
2.  ~:用于表示uri包含正则表达式，并且区分大小写。。
3.  ~\*: 用于表示uri包含正则表达式，并且不区分大小写。
4.  ^: 用于不含正则表达式的uri 前，要求Nginx服务器找到标识uri和请求字符串匹配度最高的location 后，立即使用此location 处理请求，而不再使用location块中的正则uri和请求字符串做匹配。

注意:如果uri包含正则表达式，则必须要有~或者 ~*



## 5. nginx 负载均衡

### 格式：

```
http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```



### 示例：

	http {
	
		upstream requestInfo {
			server 192.168.1.106:8091;
			server 192.168.1.106:8092;
			server 192.168.1.106:8093;
		}
	
		server {
			listen       80;
			server_name  localhost;
			
			location /robin {
				proxy_pass http://requestInfo;
				proxy_set_header Host                $host:$server_port;
				proxy_set_header X-Forwarded-For     $proxy_add_x_forwarded_for;
				proxy_set_header X-Forwarded-Proto   $scheme;
				proxy_set_header X-Forwarded-Port    $server_port;
			}
			
			error_page   500 502 503 504  /50x.html;
			location = /50x.html {
				root   /usr/share/nginx/html;
			}
		}
	
	}
### upstream 解释

upstream 可以设置访问负载均衡策略。

默认 轮询。



1. round-robin    轮询(nginx 默认)

2. least-connected    最少链接的服务器，选择划水的服务器来干活

3. ip-hash    同一个客户端访问的给同一个服务器进行处理，可以解决 session 共享的问题

4. fair   根据请求的处理时间来进行分配。(**第三方插件**)

5. Weight    , 加权轮询，权重越大，越容器被命中

   ```
   upstream myapp1 {
           server srv1.example.com weight=3;
           server srv2.example.com;
           server srv3.example.com;
       }
   ```

## 6. stream 转发端口

根节点下写  stream 块，

例如： 配置本机的 mysql 代理107 的mysql

	stream {
		upstream mysql {
		   hash $remote_addr consistent;
		   server 192.168.1.107:3306 weight=5 max_fails=3 fail_timeout=30s;
		}
		server {
		   listen 3307;#数据库服务器监听端口
		   proxy_connect_timeout 10s;
		   proxy_timeout 300s;#设置客户端和代理服务之间的超时时间，如果5分钟内没操作将自动断开。
		   proxy_pass mysql;
		}
	}
## 7. 动静分离

​		Nginx动静分离简单来说就是把动态跟静态请求分开,不能理解成只是单纯的把动态页面和静态页面物理分离。严格意义上说应该是动态请求跟静态请求分开，可以理解成使用Nginx处理静态页面，Tomcat 处理动态页面。动静分离从目前实现角度来讲大致分为两种，。

​		一种是纯粹把静态文件独立成单独 的域名，放在独立的服务器上，也是目前主流推崇的方案;+ 另外-种方法就是动态跟静态文件混合在一起发布， 通过nginx 来分开。。

​		通过location指定不同的后缀名实现不同的请求转发。通过expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之前的请求和流量。具体Expires定义:是给-一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。(如果经常 更新的文件,不建议使用Expires 来缓存)，我这里设置3d，表示在这3天之内访问这个URL，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304，如果有修改，则直接从服务器重新下载，返回状态码200。。

### 7.1 什么是动静分离



静态资源静态静态资源服务器处理， 动态请求转发给 tomcat 处理。

![image-20200504211524712](..\images\nginx-05)

### 7.2 动静分离例子

将静态文件请求转发给 静态服务器，或者直接就是nginx本机来处理

#### 例子1

		location ~* \.(gif|jpg|jpeg|png|css|js|ico|html)$ {
	        proxy_pass http://192.168.1.106:8880 ;
	    }
匹配**指定目录**下的所有以 xxx结尾的

	 		location ~* (/user-center).*\.(gif|jpg|jpeg|png|css|js|ico|html)$ {
	            proxy_pass http://192.168.1.106:8880 ;
	        }
			
			location /user-center/ {
	            proxy_pass http://127.0.0.1:8081/user-center/ ;
	        }
然后动态处理的交给后台处理

#### 例子2

静态文件处理得地方判断路径处理

		location /user-center/ {
			proxy_pass http://192.168.1.105:8081/user-center/;
			proxy_set_header Host                $host:$server_port;
			proxy_set_header X-Forwarded-For     $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto   $scheme;
			proxy_set_header X-Forwarded-Port    $server_port;
		}
		
		location /user-center/biz/ {
			proxy_pass http://192.168.1.105/webapp/biz/;
		}
		
		location /user-center/static/ {
			proxy_pass http://192.168.1.105/webapp/static/;
		}
## 8. nginx 高可用

假如只有一个 nginx，然后nginx 挂了，那岂不是系统就崩了，那肯定不行，就设置 nginx 主从

图示：

![image-20200505202636398](..\images\nginx-09)



### 准备工作

1. 准备两台服务器
2. 在两台服务器上安装 nginx
3. 在两台服务器上安装 keepalived

#### 在两台服务器安装 keepalived

使用 yum 命令进行安装

```
yum install keepalived -y
```

安装之后，在 etc 里面生成目录 keepalived ， 有文件 keepalived.conf



#### 完成高可用配置（主从配置）

keepalived 配置



分为三部分

1. 全局配置
2. 检测脚本配置
3. 虚拟ip配置



**全局配置里面的 router_id 后面写的是主机配置，LVS_DEVELBACK 就是 /etc/host 里面的 ip 配置，通过这个ip路由到主机**

![image-20200505201732135](..\images\nginx-06)

![image-20200505201850179](..\images\nginx-07)



脚本文件

![image-20200505202501952](..\images\nginx-08)

### nginx 处理请求流程

发送请求，先到nginx 中的 master ，master 相当于管理员，管理员得到任务之后把任务给下面的 worker，worker 通过争抢机制得到任务，然后可以进行反向代理或者直接处理请求。返回给客户端。

![image-20200505203118258](..\images\nginx-10)

![image-20200505203236173](..\images\nginx-11)

#### 一个 master 和多个 worker 这样设计有什么好处？

1. 可以使用 nginx -s reload 进行热部署
2. 每个 worker 都是独立的进程
   1. 因为是独立的，所以可以不加锁执行，节省了锁带来的性能消耗
   2. worker之间不会相互影响，一个进程退出后，其他进程还在工作，服务不会中断，master很快就会启动新的进程。还有可能就是 worker 进程异常退出，这会导致这个 worker 上的所有请求失败。不过不会影响到所有进程。所以降低了风险。

worker 数和 cpu 核数保持一致为最好。

![image-20200505204854412](..\images\nginx-12)