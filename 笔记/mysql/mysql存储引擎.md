# mysql存储引擎

## 1. MyISAM

建表语句：

```
CREATE TABLE `t1` (
  `a` int(11) NOT NULL,
  `b` int(11) DEFAULT NULL,
  `c` int(11) DEFAULT NULL,
  `d` int(11) DEFAULT NULL,
  `e` varchar(30) DEFAULT NULL,
  PRIMARY KEY (`a`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8
```

存储语句：

```
INSERT INTO t1 values(4,1,2,3,'a');
INSERT INTO t1 values(1,1,2,3,'s');
INSERT INTO t1 values(5,1,2,3,'q');
INSERT INTO t1 values(2,1,2,3,'w');
INSERT INTO t1 values(7,1,2,3,'r');
INSERT INTO t1 values(1,1,2,3,'g');
INSERT INTO t1 values(9,1,2,3,'s');
```



## 2. InnoDB

建表语句：

```
CREATE TABLE `t2` (
  `a` int(11) NOT NULL,
  `b` int(11) DEFAULT NULL,
  `c` int(11) DEFAULT NULL,
  `d` int(11) DEFAULT NULL,
  `e` varchar(30) DEFAULT NULL,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

存储语句：

```
INSERT INTO t2 values(4,1,2,3,'a');
INSERT INTO t2 values(1,1,2,3,'s');
INSERT INTO t2 values(5,1,2,3,'q');
INSERT INTO t2 values(2,1,2,3,'w');
INSERT INTO t2 values(7,1,2,3,'r');
INSERT INTO t2 values(1,1,2,3,'g');
INSERT INTO t2 values(9,1,2,3,'s');
```

查询 t1 和 t2 之后，可以发现， t1 得数据

你怎么存进去，查询出来就是什么样子，

t2 表得数据，存进去得不是顺序，查出来得是 根据 a 降序了的。





### 为什么 innodb 引擎的存储之后数据是有顺序的呢？

因为，innodb 存储就是有顺序的，在存的时候，就已经排好序了，按照主键排序

![image-20200417135310133](..\images\mysql-innodb)

### 如果没有主键，那 innodb 会怎样存呢

没有主键，innodb 则会找一个 唯一键，如果 唯一键也没有，则会自动添加一个 row_id 作为隐藏主键

所以 row_id 是在没有 主键 和 唯一键的情况下 innodb 自动生成的