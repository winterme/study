# 安装storm

1. 下载安装包
> 地址 https://www.apache.org/dyn/closer.lua/storm/apache-storm-1.2.3/apache-storm-1.2.3.tar.gz

2. 解压，注意要先安装java环境，最好1.8+，创建在 storm目录下创建 data 目录，

3. 修改配置

    ########### These MUST be filled in for a storm configuration
    storm.zookeeper.servers:
        - "103.118.41.204"

    ``` 数据目录```
    storm.local.dir: "/usr/local/apache-storm-1.2.3/data"

    ``` 主机```
    nimbus.host: "192.168.31.103"

    ``` 工作进程```
    supervisor.slots.ports:
        - 6700
        - 6701
        - 6702
        - 6703

4. 启动

    storm nimbus &
    storm supervisor &
    storm ui &


