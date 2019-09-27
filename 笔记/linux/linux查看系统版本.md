# linux查看系统版本

> 查看linux系统版本信息（Oracle Linux、Centos Linux、Redhat Linux、Debian、Ubuntu）

一、查看Linux系统版本的命令（3种方法）

    1、cat /etc/issue，此命令也适用于所有的Linux发行版。

    　　[root@S-CentOS home]# cat /etc/issue
    　　CentOS release 6.5 (Final)
    　　Kernel \r on an \m

    2、cat /etc/redhat-release，这种方法只适合Redhat系的Linux：

    　　[root@S-CentOS home]# cat /etc/redhat-release
    　　CentOS release 6.5 (Final)

    

    3、lsb_release -a，即可列出所有版本信息：

    　　[root@S-CentOS ~]# lsb_release -a
    　　LSB Version: :base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch:graphics-4.0-amd64:graphics-4.0-noarch:printing-4.0-amd64:printing-4.0-noarch
    　　Distributor ID: CentOS
    　　Description: CentOS release 6.5 (Final)
    　　Release: 6.5
    　　Codename: Final

    

    二、查看Linux内核版本命令（两种方法）：

    1、cat /proc/version

    　　[root@S-CentOS home]# cat /proc/version
    　　Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013

    2、uname -a

    　　[root@S-CentOS home]# uname -a

    　　Linux S-CentOS 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

    