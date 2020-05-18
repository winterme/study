# LINUX 安装mysql

下载地址： https://dev.mysql.com/downloads/mysql/5.7.html#downloads

![img](..\images\msyql-01)

## 解压

tar -zxvf mysql-5.7.30-linux-glibc2.12-i686.tar.gz



**再移动并重命名一下**

mv mysql-5.7.30-linux-glibc2.12-i686 5.7.30/



![image-20200508214511229](..\images\mysql-02)

## 创建mysql用户组和用户并修改权限
groupadd mysql
useradd -r -g mysql mysql

## 创建数据目录并赋予权限

mkdir -p  data             #创建目录

![image-20200508214732149](..\images\mysql-03)

在 mysql 根目录执行，参考图中 pwd

chown mysql:mysql -R data   #赋予权限



**配置my.cnf**

vi /etc/my.cnf

配置如下：

```
[mysqld]
bind-address=0.0.0.0
port=3306
user=mysql
basedir=/usr/local/mysql/5.7.30.x64/mysql-5.7.30-linux-glibc2.12-x86_64
datadir=/usr/local/mysql/5.7.30.x64/mysql-5.7.30-linux-glibc2.12-x86_64/data
socket=/tmp/mysql.sock
log-error=/usr/local/mysql/5.7.30.x64/mysql-5.7.30-linux-glibc2.12-x86_64/data/mysql.err
pid-file=/usr/local/mysql/5.7.30.x64/mysql-5.7.30-linux-glibc2.12-x86_64/data/mysql.pid
#character config
character_set_server=utf8mb4
symbolic-links=0
explicit_defaults_for_timestamp=true

```



## 初始化数据库
进入mysql的bin目录

cd /usr/local/mysql/bin/

## 初始化

./mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql/5.7.30/mysql-5.7.30-linux-glibc2.12-i686/ --datadir=/usr/local/mysql/5.7.30/mysql-5.7.30-linux-glibc2.12-i686/data --user=mysql --initialize

#### 如果报错：

./mysqld: error while loading shared libraries: libaio.so.1

#### 那就执行以下代码。

yum install -y libaio



## 查看密码

cat mysql.err

![image-20200508221215612](..\images\mysql-04)



#### 启动！！！

```
service mysql start
 
ps -ef|grep mysql

```

如果启动报错：

```
[root@CubeCloud-2019113523 data]# service mysql start
Redirecting to /bin/systemctl start mysql.service
Failed to start mysql.service: Unit not found.

```

那就 ：

##### 1、查询/etc/init.d/下是否存在mysql



```kotlin
ll /etc/init.d/ | grep mysql
```

发现该目录下并没有mysql的文件，若存在，请备份一下

##### 2、查询mysql.server



```swift
find / -name mysql.server
```

mysql.server一般在你安装的mysql目录下，自己是编译安装，所以知道目录在哪里，不知道的这样看一下

##### 3、执行复制操作，/usr/local/mysql/是我自己的mysql安装目录，大家根据查询出来的结果复制



```bash
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
```

然后  service mysql start 启动成功！



## 使用密码登录

![image-20200508221711969](..\images\mysql-05)

### 修改密码

![image-20200508221944171](..\images\mysql-06)



### 授权用户

![image-20200508222918134](..\images\msyql-02)