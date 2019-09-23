# linux控制台开放关闭端口

## 开启
iptables -I INPUT -p tcp --dport 8889 -j ACCEPT

## 关闭
iptables -A OUTPUT -p tcp --dport 端口号-j DROP