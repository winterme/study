# kafka基本操作

-- myfirst 为主题名称

### 产看所有主题
bin/kafka-topics.sh --list --zookeeper 127.0.0.1:2181

### 创建主题
bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --create --replication-factor 1 --partitions 2 --topic myfirst

    partitions 分区=>分区，跟oracle 分区一个概念
    replication-factor 副本数 => 在有子节点得时候生效，简单点说集群模式生效

### 查看主题

bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --describe --topic myfirst

### 删除主题

bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --delete --topic mysed

    只有 delete.topic.enable 设置为true 才会真正的删除

### 控制台创建生产者    

    进行这一步之前，先看一下kafka配置文件里面的 server.properties 里面的 listener 配置了没有

./kafka-console-producer.sh --topic msgtopic --broker-list 103.118.42.35:9092

### 控制台接受消息

    以前老版本写 zookeeper:2181 ，新版本写 kafak地址

./kafka-console-consumer.sh --topic msgtopic --bootstrap-server 103.118.42.35:9092
