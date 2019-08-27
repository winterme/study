# java-jar启动后台运行

nohup /usr/local/jdk1.8.0_121/bin/java -jar /usr/local/spring-music-1.0-SNAPSHOT.jar  --server.port=80 >msg.log 2>&1 &
