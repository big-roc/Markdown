### JavaWordCount

1. 新建Maven项目
- 工作目录：D:\workspace\IdeaProjects

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

3. 新建JavaWordCount

   ```java
   package spark;
   
   import org.apache.spark.api.java.JavaPairRDD;
   import org.apache.spark.api.java.JavaRDD;
   import org.apache.spark.sql.SparkSession;
   import scala.Tuple2;
   
   import java.util.Arrays;
   import java.util.List;
   import java.util.regex.Pattern;
   
   public class JavaWordCount {
       private static final Pattern SPACE = Pattern.compile(" ");
   
       public static void main(String[] args) {
           SparkSession spark = SparkSession
                   .builder()
                   .master("local[*]")
                   .appName("WordCount")
                   .getOrCreate();
           String paths = "D:\\workspace\\IdeaProjects\\learning\\src\\main\\resources\\word_count.txt";
           JavaRDD<String> lines = spark.read().textFile(paths).javaRDD();
           JavaRDD<String> words = lines.flatMap(s -> Arrays.asList(SPACE.split(s)).iterator());
           JavaPairRDD<String, Integer> ones = words.mapToPair(s -> new Tuple2<>(s, 1));
           JavaPairRDD<String, Integer> counts = ones.reduceByKey((i1, i2) -> (i1 + i2));
           List<Tuple2<String, Integer>> output = counts.collect();
           for (Tuple2<?, ?> tuple : output) {
               System.out.println(tuple._1() + ": " + tuple._2());
           }
           spark.stop();
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