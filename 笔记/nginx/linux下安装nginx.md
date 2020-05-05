# linux下安装nginx

安装步骤
安装前环境准备
1：安装wget,用来下载nginx安装包

cd /
yum install wget -y


2：因为Nginx以来与gcc的编译环境，所以，在mini centos中需要安装编译环境来使Nginx能够编译起来

yum install gcc-c++


3：Nginx的http模块需要使用pcre来解析正则表达式

yum -y install pcre pcre-devel


4：依赖的解压包

yum -y install zlib zlib-devel


5：openssl安装

yum install -y openssl openssl-devel


6：官网下载 http://nginx.org/en/download.html 找到自己需要的版本下载(以下为最新版本)

切换到opt目录下，新建文件夹nginx，然后进入nginx文件夹


执行如下命令：

wget  http://nginx.org/download/nginx-1.13.11.tar.gz


安装nginx
1：解决nginx文件

tar zxvf nginx-1.13.11.tar.gz


2：编译，安装

先切换到opt目录下，新建文件夹nginx-1-13


cd nginx-1.13.11
./configure  --prefix=/opt/nginx-1-13      #指定安装目录


3：在/opt/nginx/nginx-1.13.11目录下执行编译命令

make




4:执行安装命令

make install


5:切换到安装目录

cd /opt/nginx-1-13


6:启动

cd sbin/

./nginx 
./nginx -s stop
./nginx -s quit
./nginx -s reload
./nginx -s quit:此方式停止步骤是待nginx进程处理任务完毕进行停止。
./nginx -s stop:此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。


7：开放nginx默认端口号80

/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT