### 数据存储(断电时)
* 方案一：RDB，类似oracle的undo，数据刷盘(__不太稳妥__)
* 方案二：AOF，记录操作日志，断电之后重启的时候从 日志里面读取(`一般选择`)

### redis集群数据一致方案
* 主从模式(主从，主挂了，从可能就用不成了)
* 哨兵模式(在主从的模式下添加哨兵节点，在主节点挂掉的时候，在从节点内部进行选举出一台主节点。下次主节点修复之后，再加进来就成为从节点了，相当于可以自动设置主节点)(`2.x`)
* redis3.0 之后的集群模式(多主多从)

> redis多实例,群狼战猛虎

> 问题：redis慢，为什么？

为了保证高可用，一般都会开启 AOF 日志记录(写一条数据，记录一条日志，不开启可能会数据丢失)，多线程并发的写的时候，会多次写日志，此刻就可能会比较慢。那么可以多加几个 节点来解决

> `给关系型数据库降压`

>redis的优点缺点
* 优点：
    * 对数据进行高并发读写
    * 对海量数据的高效访问和存储
    * 对数据的可扩展性和高可用性
* 缺点
    * redis(ACID处理非常简单)，无法做到太复杂的关系数据库模型
     