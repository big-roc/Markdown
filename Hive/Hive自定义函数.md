### Hive 自定义函数

#### 1. 自定义函数分类

- UDF：操作单行数据，产生单行数据
- UDAF：操作多行数据，产生单行数据
- UDTF：操作单行数据，产生多行数据

#### 2. UDF函数

- 编程步骤

  1）继承UDF类

  2）实现evaluate方法

  3）打包上传

  4）在hive中添加jar：add jar linux_jar_path;

  5）创建临时function：create [temporary] function [dbname.]function_name as class_name;

  6）在hive中调用

- 删除自定义函数

  drop [temporary] function [if exists] [dbname.]function_name;

- 示例

  ```shell
  # 1.创建maven工程
  # 2.导入依赖
  <!--hive依赖-->
  <dependency>
  	<groupId>org.apache.hive</groupId>
  	<artifactId>hive-exec</artifactId>
  	<version>1.2.1</version>
  </dependency>
  # 3.创建一个类
  public class UDFLower extends UDF {
      public String evaluate(String s) {
          if (s == null) {
              return null;
          }
          return s.toLowerCase();
      }
  }
  # 4.打成jar包上传服务器（临时）
  # 4.打成jar包上传hdfs（永久）
  hadoop fs -put udf.jar /user/hive/udf/
  # 5.将jar包添加到hive的classpath
  add jar /data/soft/hive/udf_jars/udf.jar;
  add jar hdfs:///user/hive/udf/udf.jar;
  # 6.创建临时函数
  create temporary function lower as 'demo.udf.UDFLower';
  # 6.创建永久函数
  create function find_json_array as 'demo.udf.UDFFindFromJSONArray';
  create function find_json_array as 'demo.udf.UDFFindFromJSONArray'
  using jar "hdfs:///user/hive/udf/udf.jar";
  # 7.在hql中使用自定义函数
  select name,lower(name) as lower_name from emp;
  # 8.删除自定义函数
  drop function dwd.find_json_array;
  ```

