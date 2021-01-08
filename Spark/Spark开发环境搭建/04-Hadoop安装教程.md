### Hadoop安装教程

1. 下载hadoop-2.7.6.tar.gz

2. 解压

   - 目录：D:\software\hadoop-2.7.6

3. 配置环境变量

   - 新建变量HADOOP_HOME：D:\software\hadoop-2.6.5
   - 配置PATH：%HADOOP_HOME%\bin

   说明：IDEA需要重启电脑才能识别新增的环境变量

4. 下载Windows工具类winutils，拷贝到%HADOOP_HOME%\bin文件夹，将其中的hadoop.dll拷贝一份到C:\Windows\System32

5. 在cmd输入hadoop version，有提示则说明安装成功

6. 配置Hadoop配置文件

   %HADOOP_HOME%\etc\hadoop\hadoop-env.cmd

   ```cmd
   set JAVA_HOME=D:\software\Java\jdk1.8
   set HADOOP_IDENT_STRING=%WS%
   set HADOOP_PREFIX=d:\software\hadoop-2.6.5
   set HADOOP_CONF_DIR=%HADOOP_PREFIX%\etc\hadoop
   set YARN_CONF_DIR=%HADOOP_CONF_DIR%
   set PATH=%PATH%;%HADOOP_PREFIX%\bin
   ```

   %HADOOP_HOME%\etc\hadoop\core-site.xml

   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://localhost:9000</value>
           <description>
               默认的HDFS端口，用于NameNode与DataNode之间到的通讯，IP为NameNode的地址
           </description>
       </property>
   </configuration>
   ```

   %HADOOP_HOME%\etc\hadoop\hdfs-site.xml

   ```xml
   <configuration>
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
           <value>file:///D:/software/hadoop-2.6.5/data/namenode</value>
       </property>
       <property>
           <name>dfs.datanode.data.dir</name>
           <value>file:///D:/software/hadoop-2.6.5/data/datanode</value>
       </property>
   </configuration>
   ```

   %HADOOP_HOME%\etc\hadoop\mapred-site.xml

   ```xml
   <configuration>
   	<property>
   		<name>mapreduce.job.user.name</name>
   		<value>%WS%</value>
   	</property>
   	<property>
   		<name>mapreduce.framework.name</name>
   		<value>yarn</value>
   	</property>
   	<property>
   		<name>yarn.apps.stagingDir</name>
   		<value>/user/%WS%/staging</value>
   	</property>
   	<property>
   		<name>mapreduce.jobtracker.address</name>
   		<value>local</value>
   	</property>
   
   </configuration>
   ```

   %HADOOP_HOME%\etc\hadoop\yarn-site.xml

   ```xml
   <configuration>
       <property>
           <name>yarn.server.resourcemanager.address</name>
           <value>0.0.0.0:8020</value>
       </property>
       <property>
           <name>yarn.server.resourcemanager.application.expiry.interval</name>
           <value>60000</value>
       </property>
       <property>
           <name>yarn.server.nodemanager.address</name>
           <value>0.0.0.0:45454</value>
       </property>
       <property>
           <name>yarn.nodemanager.aux-services</name>
           <value>mapreduce_shuffle</value>
       </property>
       <property>
           <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
           <value>org.apache.hadoop.mapred.ShuffleHandler</value>
       </property>
       <property>
           <name>yarn.server.nodemanager.remote-app-log-dir</name>
           <value>/app-logs</value>
       </property>
       <property>
           <name>yarn.nodemanager.log-dirs</name>
           <value>/dep/logs/userlogs</value>
       </property>
       <property>
           <name>yarn.server.mapreduce-appmanager.attempt-listener.bindAddress</name>
           <value>0.0.0.0</value>
       </property>
       <property>
           <name>yarn.server.mapreduce-appmanager.client-service.bindAddress</name>
           <value>0.0.0.0</value>
       </property>
       <property>
           <name>yarn.log-aggregation-enable</name>
           <value>true</value>
       </property>
       <property>
           <name>yarn.log-aggregation.retain-seconds</name>
           <value>-1</value>
       </property>
       <property>
           <name>yarn.application.classpath</name>
           <value> %HADOOP_CONF_DIR%,%HADOOP_COMMON_HOME%/share/hadoop/common/*,%HADOOP_COMMON_HOME%/share/hadoop/common/lib/*,%HADOOP_HDFS_HOME%/share/hadoop/hdfs/*,%HADOOP_HDFS_HOME%/share/hadoop/hdfs/lib/*,%HADOOP_MAPRED_HOME%/share/hadoop/mapreduce/*,%HADOOP_MAPRED_HOME%/share/hadoop/mapreduce/lib/*,%HADOOP_YARN_HOME%/share/hadoop/yarn/*,%HADOOP_YARN_HOME%/share/hadoop/yarn/lib/*
           </value>
       </property>
   </configuration>
   ```

   Windows配置参考：https://cwiki.apache.org/confluence/display/HADOOP2/Hadoop2OnWindows

7. 启动Hadoop

   - 跳转到Hadoop主目录：cd /d D:software\hadoop-2.6.5

   - 格式化HDFS文件系统：bin\hdfs namenode -format
   - 启动HDFS守护进程：sbin\start-dfs.cmd
   - 上传文件验证：bin\hdfs dfs -put README.txt /
   - 运行：sbin\start-all.cmd

8. 验证

   - 资源管理：http://localhost:8088/cluster

   - 节点管理：[http://localhost:50070 ](http://localhost:50070/)
   - 进程：jps

   说明：jps查看进程不显示，查看C:\Users\%USER%\AppData\Local\Temp\hsperfdata_%USER%属性 --> 安全，添加当前账号即可

   ### 参考资料

   - https://www.cnblogs.com/lyhero11/p/11590783.html
   - https://blog.csdn.net/CSDN_fzs/article/details/78985586
   - https://blog.csdn.net/qq_35535690/article/details/81976032
   - https://www.cnblogs.com/sunxucool/p/3957407.html
