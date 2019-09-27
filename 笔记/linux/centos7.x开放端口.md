# centos7.x开放端口

> https://blog.csdn.net/realjh/article/details/82048492

> 命令集合：

    （1）查看对外开放的端口状态
    查询已开放的端口 netstat -anp
    查询指定端口是否已开 firewall-cmd --query-port=666/tcp
    提示 yes，表示开启；no表示未开启。
    
    （2）查看防火墙状态
    查看防火墙状态 systemctl status firewalld
    开启防火墙 systemctl start firewalld  
    关闭防火墙 systemctl stop firewalld
    开启防火墙 service firewalld start 
    若遇到无法开启
    先用：systemctl unmask firewalld.service 
    然后：systemctl start firewalld.service    


    （3）对外开发端口
    查看想开的端口是否已开：
    firewall-cmd --query-port=6379/tcp

    添加指定需要开放的端口：
    firewall-cmd --add-port=123/tcp --permanent
    重载入添加的端口：
    firewall-cmd --reload
    查询指定端口是否开启成功：
    firewall-cmd --query-port=123/tcp

    移除指定端口：
    firewall-cmd --permanent --remove-port=123/tcp

    安装iptables-services ：
    yum install iptables-services 
    进入下面目录进行修改：
    /etc/sysconfig/iptables