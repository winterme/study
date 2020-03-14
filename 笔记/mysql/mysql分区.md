# mysql 分区

    CREATE TABLE `tbl_user_innodb` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `username` varchar(255) DEFAULT NULL,
    `email` varchar(20) DEFAULT NULL,
    `age` tinyint(4) DEFAULT NULL,
    `type` int(11) DEFAULT NULL,
    `create_time` datetime DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=5100002 DEFAULT CHARSET=utf8;
    
    CREATE TABLE `tbl_user_myisam` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `username` varchar(255) DEFAULT NULL,
    `email` varchar(20) DEFAULT NULL,
    `age` tinyint(4) DEFAULT NULL,
    `type` int(11) DEFAULT NULL,
    `create_time` datetime DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`)
    ) ENGINE=myisam AUTO_INCREMENT=5100002 DEFAULT CHARSET=utf8;




    -- 修改mysql默认的结束符号，默认是分号；但是在函数和存储过程中会使用到分号导致解析不正确
    delimiter $$


    -- 随机生成一个指定长度的字符串
    create function rand_string(n int) returns varchar(255) 
    begin 
    # 定义三个变量
    declare chars_str varchar(100) default 'abcdefghijklmnopqrstuvwxyzABCDEFJHIJKLMNOPQRSTUVWXYZ';
    declare return_str varchar(255) default '';
    declare i int default 0;

    while i < n do 
    set return_str = concat(return_str, substring(chars_str, floor(1+rand()*52), 1));
    set i = i + 1;
    end while;
    return return_str;
    end $$

    CREATE TABLE `tbl_user_no_part` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `username` varchar(255) DEFAULT NULL,
    `email` varchar(20) DEFAULT NULL,
    `age` tinyint(4) DEFAULT NULL,
    `type` int(11) DEFAULT NULL,
    `create_time` datetime DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (`id`)
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;




    -- 修改mysql默认的结束符号，默认是分号；但是在函数和存储过程中会使用到分号导致解析不正确
    delimiter $$

    -- 创建插入的存储过程
    create procedure insert_user(in start int(10), in max_num int(10))
    begin
        declare i int default 0; 
        set autocommit = 0;  
        repeat
            set i = i + 1;
            insert into tbl_user_no_part values ((start+i) ,rand_string(8), concat(rand_string(6), '@random.com'), 1+FLOOR(RAND()*100), 3, now());
            until i = max_num
        end repeat;
    commit;
    end $$

    -- 将命令结束符修改回来
    delimiter ;

    -- 调用存储过程，插入500万数据，需要等待一会时间，等待执行完成
    call insert_user(100001,5000000);



    select count(*) from tbl_user_no_part;

    DESCRIBE tbl_user_no_part


    CREATE TABLE `tbl_user_part`(
        `id` int(11) NOT NULL,
        `username` VARCHAR(255) DEFAULT NULL,
        `email` varchar(20)     DEFAULT NULL,
        `age` tinyint(4)        DEFAULT NULL,
        `type` int(11)          DEFAULT NULL,
        `create_time` datetime  DEFAULT CURRENT_TIMESTAMP
        -- PRIMARY KEY (`id`,`age`)
    ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8
    PARTITION BY RANGE (age) (
        PARTITION p0 VALUES LESS THAN (20),
        PARTITION p1 VALUES LESS THAN (40),
        PARTITION p2 VALUES LESS THAN (60),
        PARTITION p3 VALUES LESS THAN (80),
        PARTITION p4 VALUES LESS THAN MAXVALUE
    );

    INSERT INTO tbl_user_part 
    SELECT * FROM tbl_user_no_part




    select version();

    show plugins;


    SELECT * FROM tbl_user_no_part WHERE age > 25 and age <30

    SELECT * FROM tbl_user_part WHERE age > 25 and age <30

    -- 查看执行计划，可以看到加入分区的扫描rows 少只有 100w了
    EXPLAIN
    SELECT * FROM tbl_user_no_part WHERE age > 25 and age <30

    EXPLAIN
    SELECT * FROM tbl_user_part WHERE age > 25 and age <30


    CREATE INDEX age_index on tbl_user_no_part(age)

    CREATE INDEX age_index on tbl_user_part(age)







link：https://blog.csdn.net/vbirdbest/article/details/82461109