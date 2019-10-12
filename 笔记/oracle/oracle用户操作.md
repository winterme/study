# oracle 用户操作

### 用户锁定和解锁/密码设置：

    alter user [USER] account lock;
    alter user [USER] account unlock;
    alter user scott identified by tiger;
    select * from dba_users; --查看用户信息表
    创建用户：create user [USER] identified by [PASSWD];
    删除用户：drop user [USER] cascade;
