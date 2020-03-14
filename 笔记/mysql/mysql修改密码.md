use mysql;

update user set password=password('123') where user='root' and host='localhost'; 

可能过时了，过时了就查一下 user 表

