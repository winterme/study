# kafka安装

kafka 入门从安装开始

1. 下载kafka

    wget http://mirror.bit.edu.cn/apache/kafka/2.1.1/kafka_2.12-2.1.1.tgz

2. 解压

    tar -zxvf kafka_2.12-2.1.1.tgz

3. 修改配置

    vim kafka/config/server.properties

    修改logdata 存储文件夹，zk 链接地址

4. 启动

    bin/kafka-server-start.sh config/server.properties

    