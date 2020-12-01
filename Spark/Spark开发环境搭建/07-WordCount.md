1. 新建Maven项目

   - 选择模板：scala-archetype-simple

2. 配置pom文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <groupId>org.example</groupId>
       <artifactId>learning</artifactId>
       <version>1.0-SNAPSHOT</version>
   
       <properties>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
           <spark.version>2.2.0</spark.version>
           <scala.version>2.11.8</scala.version>
           <hadoop.version>2.6.5</hadoop.version>
           <hive.version>1.2.1</hive.version>
       </properties>
   
       <dependencies>
           <!--Spark相关的依赖-->
           <dependency>
               <groupId>org.apache.spark</groupId>
               <artifactId>spark-core_2.11</artifactId>
               <version>${spark.version}</version>
           </dependency>
           <dependency>
               <groupId>org.apache.spark</groupId>
               <artifactId>spark-sql_2.11</artifactId>
               <version>${spark.version}</version>
           </dependency>
           <dependency>
               <groupId>org.apache.spark</groupId>
               <artifactId>spark-hive_2.11</artifactId>
               <version>${spark.version}</version>
           </dependency>
           <dependency>
               <groupId>org.apache.spark</groupId>
               <artifactId>spark-streaming_2.11</artifactId>
               <version>${spark.version}</version>
           </dependency>
           <!--hadoop依赖-->
           <dependency>
               <groupId>org.apache.hadoop</groupId>
               <artifactId>hadoop-common</artifactId>
               <version>${hadoop.version}</version>
           </dependency>
           <!--hive依赖包-->
           <dependency>
               <groupId>org.apache.hive</groupId>
               <artifactId>hive-exec</artifactId>
               <version>${hive.version}</version>
           </dependency>
           <!--mysql驱动包-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>5.1.38</version>
           </dependency>
       </dependencies>
   </project>
   ```

3. 新建WordCount

   ```scala
   package src.main.scala.spark
   
   import org.apache.spark.{SparkConf, SparkContext}
   
   object WordCount {
     def main(args: Array[String]) {
       //创建一个Scala版本的Spark Context
       val conf = new SparkConf().setMaster("local[*]").setAppName("WordCount")
       val sc = new SparkContext(conf)
       //读取本地数据
       val lines = sc.textFile("D:\\workspace\\IdeaProjects\\learningscala\\src\\main\\resources\\word_count.txt")
       //把它切分成一个
       val words = lines.flatMap(line => line.split(" "))
       //转化为键值对并计数
       val wordCounts = words.map(word => (word, 1)).reduceByKey { case (x, y) => x + y }
       //打印统计结果
       wordCounts.collect().foreach(println)
       //关闭Spark
       sc.stop()
     }
   }
   ```

4. 在resources目录下新建log4j.properties和word_count.txt

   log4j.properties文件

   ```properties
   #
   # Licensed to the Apache Software Foundation (ASF) under one or more
   # contributor license agreements.  See the NOTICE file distributed with
   # this work for additional information regarding copyright ownership.
   # The ASF licenses this file to You under the Apache License, Version 2.0
   # (the "License"); you may not use this file except in compliance with
   # the License.  You may obtain a copy of the License at
   #
   #    http://www.apache.org/licenses/LICENSE-2.0
   #
   # Unless required by applicable law or agreed to in writing, software
   # distributed under the License is distributed on an "AS IS" BASIS,
   # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   # See the License for the specific language governing permissions and
   # limitations under the License.
   #
   
   # Set everything to be logged to the console
   log4j.rootCategory=WARN, console
   log4j.appender.console=org.apache.log4j.ConsoleAppender
   log4j.appender.console.target=System.err
   log4j.appender.console.layout=org.apache.log4j.PatternLayout
   log4j.appender.console.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n
   
   # Set the default spark-shell log level to WARN. When running the spark-shell, the
   # log level for this class is used to overwrite the root logger's log level, so that
   # the user can have different defaults for the shell and regular Spark apps.
   log4j.logger.org.apache.spark.repl.Main=WARN
   
   # Settings to quiet third party logs that are too verbose
   log4j.logger.org.spark_project.jetty=WARN
   log4j.logger.org.spark_project.jetty.util.component.AbstractLifeCycle=ERROR
   log4j.logger.org.apache.spark.repl.SparkIMain$exprTyper=INFO
   log4j.logger.org.apache.spark.repl.SparkILoop$SparkILoopInterpreter=INFO
   log4j.logger.org.apache.parquet=ERROR
   log4j.logger.parquet=ERROR
   
   # SPARK-9183: Settings to avoid annoying messages when looking up nonexistent UDFs in SparkSQL with Hive support
   log4j.logger.org.apache.hadoop.hive.metastore.RetryingHMSHandler=FATAL
   log4j.logger.org.apache.hadoop.hive.ql.exec.FunctionRegistry=ERROR
   ```

   word_count.txt文件

   ```tex
   Give me the strength lightly to bear my joys and sorrows.
   Give me the strength to make my love fruitful in service.
   Give me the strength never to disown the poor or bend my knees before insolent might.
   Give me the strength to raise my mind high above daily trifles.
   And give me the strength to surrender my strength to thy will with love.
   ```

5. 运行验证