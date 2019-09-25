# redis控制台泛删除

    /usr/local/soft/redis/bin/redis-cli -p 9876 -a zhangzq keys "MUSIC_INFO*" | xargs /usr/local/soft/redis/bin/redis-cli -p 9876 -a zhangzq del