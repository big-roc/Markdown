### Hadoop集群配置

- hadoop-env.sh

  ```shell
  # 配置JAVA_HOME
  export JAVA_HOME=/data/soft/java/jdk1.8.0_121
  ```

- core-site.xml

  ```xml
  <!-- 指定HDFS中NameNode的地址 -->
  <property>
      <name>fs.defaultFS</name>
      <value>hdfs://master:9000</value>
  </property>
  
  <!-- 指定Hadoop运行时产生文件的存储目录 -->
  <property>
      <name>hadoop.tmp.dir</name>
      <value>/data/soft/hadoop/data/tmp</value>
  </property>
  ```

- hdfs-site.xml

  ```xml
  <!-- 指定namenode存储目录 -->
  <property>
      <name>dfs.namenode.name.dir</name>
      <value>/data/soft/hadoop/data/name</value>
  </property>
  
  <!-- 指定datenode存储目录 -->
  <property>
      <name>dfs.datenode.date.dir</name>
      <value>/data/soft/hadoop/data/date</value>
  </property>
  
  <!-- Hadoop副本数量 -->
  <property>
      <name>dfs.replication</name>
      <value>3</value>
  </property>
  
  <!-- 指定Hadoop辅助名称节点主机配置 -->
  <property>
      <name>dfs.namenode.secondary.http-address</name>
      <value>slave2:50090</value>
  </property>
  ```

- yarn-env.sh

  ```shell
  export JAVA_HOME=/data/soft/java/jdk1.8.0_121
  ```

- yarn-site.xml

  ```xml
  <!-- Reducer获取数据的方式 -->
  <property>
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
  </property>
  
  <!-- 指定YARN的ResourceManager的地址 -->
  <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>slave1</value>
  </property>
  
  <!-- 开启日志聚集功能 -->
  <property>
      <name>yarn.log-aggregation-enable</name>
      <value>true</value>
  </property>
  
  <!-- 日志保留时间设置7天 -->
  <property>
      <name>yarn.log-aggregation.retain-seconds</name>
      <value>604800</value>
  </property>
  ```

- mapred-env.sh

  ```sh
  export JAVA_HOME=/data/soft/java/jdk1.8.0_121
  ```

- mapred-site.xml

  ```xml
  <!-- 指定MR运行在Yarn上 -->
  <property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
  </property>
  
  <!-- 历史服务器地址 -->
  <property>
      <name>mapreduce.jobhistory.address</name>
      <value>slave3:10020</value>
  </property>
  <!-- 历史服务器web端地址 -->
  <property>
      <name>mapreduce.jobhistory.webapp.address</name>
      <value>slave3:19888</value>
  </property>
  ```

- sad

