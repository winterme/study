# MYSQL 必知必会

## 3 使用数据库

### 3.3 了解数据库和表

​		数据库、表、列、用户、权限等的信息被存储在数据库和表中（MySQL 使用MySQL来存储这些信息）。不过，内部的表一般不直接访问。可用 MySQL的SHOW命令来显示这些信息（MySQL从内部表中提取这些信息）。 

**显示所有数据库**

show databases； 

**显示所有表**

show tables;

**show 还可以显示表得所有字段**

show columns from table_name

>  SHOW COLUMNS 要求给出一个表名（这个例子中的FROM customers），它对每个字段返回一行，行中包含字段名、数据 类型、是否允许NULL、键信息、默认值以及其他信息（如字段cust_id 的auto_increment）。 



>  **DESCRIBE语句  MySQL支持用DESCRIBE作为SHOW COLUMNS FROM的一种快捷方式**。换句话说，DESCRIBE customers;是 SHOW COLUMNS FROM customers;的一种快捷方式



所支持的其他SHOW语句还有： 

1. SHOW STATUS，用于显示广泛的服务器状态信息； 
2. SHOW CREATE DATABASE和SHOW CREATE TABLE，分别用来显示创 建特定数据库或表的MySQL语句； 
3. SHOW GRANTS，用来显示授予用户（所有用户或特定用户）的安 全权限； **(show grants for userName)**
4. SHOW ERRORS和SHOW WARNINGS，用来显示服务器错误或警告消息。

**进一步了解SHOW  请在mysql命令行实用程序中，执行命令 HELP SHOW;显示允许的SHOW语句**

```
mysql> help show;
Name: 'SHOW'
Description:
SHOW has many forms that provide information about databases, tables,
columns, or status information about the server. This section describes
those following:

SHOW {BINARY | MASTER} LOGS
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count]
SHOW CHARACTER SET [like_or_where]
SHOW COLLATION [like_or_where]
SHOW [FULL] COLUMNS FROM tbl_name [FROM db_name] [like_or_where]
SHOW CREATE DATABASE db_name
SHOW CREATE EVENT event_name
SHOW CREATE FUNCTION func_name
SHOW CREATE PROCEDURE proc_name
SHOW CREATE TABLE tbl_name
SHOW CREATE TRIGGER trigger_name
SHOW CREATE VIEW view_name
SHOW DATABASES [like_or_where]
SHOW ENGINE engine_name {STATUS | MUTEX}
SHOW [STORAGE] ENGINES
SHOW ERRORS [LIMIT [offset,] row_count]
SHOW EVENTS
SHOW FUNCTION CODE func_name
SHOW FUNCTION STATUS [like_or_where]
SHOW GRANTS FOR user
SHOW INDEX FROM tbl_name [FROM db_name]
SHOW MASTER STATUS
SHOW OPEN TABLES [FROM db_name] [like_or_where]
SHOW PLUGINS
SHOW PROCEDURE CODE proc_name
SHOW PROCEDURE STATUS [like_or_where]
SHOW PRIVILEGES
SHOW [FULL] PROCESSLIST
SHOW PROFILE [types] [FOR QUERY n] [OFFSET n] [LIMIT n]
SHOW PROFILES
SHOW RELAYLOG EVENTS [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count]
SHOW SLAVE HOSTS
SHOW SLAVE STATUS [FOR CHANNEL channel]
SHOW [GLOBAL | SESSION] STATUS [like_or_where]
SHOW TABLE STATUS [FROM db_name] [like_or_where]
SHOW [FULL] TABLES [FROM db_name] [like_or_where]
SHOW TRIGGERS [FROM db_name] [like_or_where]
SHOW [GLOBAL | SESSION] VARIABLES [like_or_where]
SHOW WARNINGS [LIMIT [offset,] row_count]

like_or_where:
    LIKE 'pattern'
  | WHERE expr

If the syntax for a given SHOW statement includes a LIKE 'pattern'
part, 'pattern' is a string that can contain the SQL % and _ wildcard
characters. The pattern is useful for restricting statement output to
matching values.

Several SHOW statements also accept a WHERE clause that provides more
flexibility in specifying which rows to display. See
http://dev.mysql.com/doc/refman/5.7/en/extended-show.html.

URL: http://dev.mysql.com/doc/refman/5.7/en/show.html
```

## 11 使用数据处理函数

### 11.2.2 常用日期和时间处理函数 

AddDate()         增加一个日期（天、周等） 
AddTime()         增加一个时间（时、分等） 
CurDate()         返回当前日期 
CurTime()         返回当前时间 
Date()            返回日期时间的日期部分 
DateDiff()        计算两个日期之差 
Date_Add()        高度灵活的日期运算函数 
Date_Format()     返回一个格式化的日期或时间串 
Day()             返回一个日期的天数部分 
DayOfWeek()       对于一个日期，返回对应的星期几 
Hour()            返回一个时间的小时部分 
Minute()          返回一个时间的分钟部分 
Month()           返回一个日期的月份部分 
Now()             返回当前日期和时间 
Second()          返回一个时间的秒部分 
Time()            返回一个日期时间的时间部分 
Year()            返回一个日期的年份部分





**字符串转日期格式： **

select date_format('1998-06-14 05:23:12', '%Y-%m-%d %H:%i:%s');