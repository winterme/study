# tomcat管理配置

## 1.1 host-manager

```xml
<role rolename="admin-gui"/>
<role rolename="admin-script"/>
<user username="tomcat-host" password="pkusoft" roles="admin-gui,admin-script"/>
```
## 1.2 manager

```xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="tomcat-manager" password="pkusoft" roles="manager-gui,manager-jmx,manager-script,manager-status"/>
```