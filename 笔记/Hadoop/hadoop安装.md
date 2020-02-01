# hadoop安装

1. 下载源文件

    http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.7/

    http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.7/

2. 解压 tar -zxvf   hadoop-2.7.7.tar.gz

3. 修改配置文件 /etc/hadoop/core-site.xml,etc/hadoop/mapred-site.xml,etc/hadoop/hdfs-site.xml,etc/hadoop/yarn-site.xml

    3.1 core-site.xml

        ```
        <configuration>  
            <!-- nameNode主机 -->
            <property>  
               <name>fs.default.name</name>  
               <value>hdfs://localhost:9000</value>  
           </property>
            <property>  
                <!-- 此处为设置hadoop临时目录 -->
               <name>hadoop.tmp.dir</name>  
               <value>/hadoop/tmp</value>  
           </property>
        </configuration>
        ```

    3.2 mapred-site.xml

        ```
        <configuration>  
            <property>  
               <name>mapreduce.framework.name</name>  
               <value>yarn</value>  
           </property>
        </configuration>       
        ```

    3.3 hdfs-site.xml

        ```
        <configuration>  
            <!-- 这个参数设置为1，因为是单机版hadoop -->  
            <property>  
                <name>dfs.replication</name>  
                <value>1</value>  
            </property>  
            <property>   
             <name>dfs.permissions</name>   
             <value>false</value>   
          </property>  
           <property>  
               <name>dfs.namenode.name.dir</name>  
               <value>/usr/local/hadoop-2.7.7/data/namenode</value>  
           </property>  
           <property>  
                <name>fs.checkpoint.dir</name>  
                <value>/usr/local/hadoop-2.7.7/data/snn</value>  
            </property>  
            <property>  
                <name>fs.checkpoint.edits.dir</name>  
                <value>/usr/local/hadoop-2.7.7/data/snn</value>  
            </property>  
               <property>  
               <name>dfs.datanode.data.dir</name>  
               <value>/usr/local/hadoop-2.7.7/data/datanode</value>  
           </property>
        </configuration>
        ```    

    3.4. yarn-site.xml

        ```
        <configuration>  
        <!-- Site specific YARN configuration properties -->  
            <property>  
                <name>yarn.nodemanager.aux-services</name>  
                <value>mapreduce_shuffle</value>  
           </property>  
           <property>  
                <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>    
                <value>org.apache.hadoop.mapred.ShuffleHandler</value>  
           </property>
        </configuration>
        ```       

4. 进入bin/hadoop/目录，执行 ./hdfs namenode -format ,然后进入 sbin 目录， ./start-all.sh       

5. 浏览器打开 http://127.0.0.1:8088 看到管理界面
    http://127.0.0.1:50070 看到hdfs 的管理界面