# BUG 

## 错误1

```
org.apache.hadoop.ipc.RemoteException: File /xx.jpg could only be replicated to 0 nodes instead of minReplication (=1).  There are 0 datanode(s) running and no node(s) are excluded in this operation.
```

解决办法：

```
看它的报错信息好像是节点没有启动，但是我的节点都启动起来了，使用jps也能查看到节点信息。
使用hadoop dfsadmin -report命令查看磁盘使用情况，发现出现以下问题：
Configured Capacity: 0 (0 B)Present Capacity: 0 (0 B)DFS Remaining: 0 (0 B)DFS Used: 0 (0 B)DFS Used%: NaN%Under replicated blocks: 0Blocks with corrupt replicas: 0Missing blocks: 0-------------------------------------------------Datanodes available: 0 (0 total, 0 dead)
节点下存储空间都是空的，问题应该就是出现在这了。
       查阅资料发现造成这个问题的原因可能是使用hadoop namenode -format格式化时格式化了多次造成那么spaceID不一致，解决方案：
1、停止集群（切换到/sbin目录下）
stop-all.sh
2、删除在hdfs中配置的data目录（即在core-site.xml中配置的hadoop.tmp.dir对应文件件）下面的所有数据;
rm -rf /root/training/hadoop-2.7.3/tmp
3、重新格式化namenode(切换到hadoop目录下的bin目录下)
hdfs namenode -format
4、重新启动hadoop集群（切换到hadoop目录下的sbin目录下）
start-all.sh
```

