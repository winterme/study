# rabbitmq安装

1. 下载文件

wget http://erlang.org/download/otp_src_21.0.tar.gz

2. 解压 otp_src_21.0.tar.gz 

进入目录，进行编译[./otp_build autoconf]

如果出现 autoconf: command not found 则需要 yum install -y autoconf

然后： ./configure

make

如果报错说：[/usr/local/rabbitmq/otp_src_21.0/make/x86_64-unknown-linux-gnu/otp_ded.mk: No such file or directory],那就 sudo yum install ncurses-devel.x86_64，如果 ubuntu 提示不存在那就【apt install libncurses5-dev】

./configure --prefix=/usr/local/rabbitmq/erlang/


第二步，安装 rabbitmq

wget https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.7/rabbitmq-server-3.7.7-1.el7.noarch.rpm

















参考链接：https://www.cnblogs.com/SpaceKiller/p/11644625.html


