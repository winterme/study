# oracle删除外键
    --新增字段
    alter table advice add(state varchar2(10));

    删除字段的语法：alter table tablename drop (column);

    说明：alter table 表名 drop column 字段名;

    例：alter table users drop column Head;

     

    --删除外键

    alter table 表名 drop constraint 外键名称
    alter table users drop constraint EMP_USERSTYPE_FK