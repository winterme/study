# mysql-主从

1. 主服务器要配置 binlog
2. 从服务器要配置 relaylog
3. 从服务器如何有权读取master的binlog？
   1. 授权，master 授予 slave 账号
4. 从服务器用账号连接 master 

