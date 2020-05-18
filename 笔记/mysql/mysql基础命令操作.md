# mysql基础命令操作

### 查看表建表语句

show create table [table_name]

### 创建索引

create index [index_name]  on  ```[table_name](colunm1 ... )```

### 查看一张表的索引

show index from t1

### 查看系统参数

show variables like 'autocommit'

### 冲刷权限

flush privileges;