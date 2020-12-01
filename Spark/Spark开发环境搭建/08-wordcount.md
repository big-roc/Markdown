1. 安装pyspark

   - 通过拷贝pyspark包安装
   - 源目录：D:\software\spark-2.2.0-bin-hadoop2.6\python\pyspark
   - 目标目录：D:\software\Anaconda3\Lib\site-packages

2. 安装py4j

3. 新建wordcount

   ```python
   # coding:utf8
   from __future__ import print_function
   
   from operator import add
   from pyspark.sql import SparkSession
   
   if __name__ == "__main__":
       spark = SparkSession \
           .builder \
           .appName('PythonWordCount') \
           .getOrCreate()
   
       path = "D:\\workspace\\IdeaProjects\\learning\\src\\main\\resources\\word_count.txt"
       lines = spark.read.text(path).rdd.map(lambda r: r[0])
       counts = lines.flatMap(lambda x: x.split(' ')) \
           .map(lambda x: (x, 1)) \
           .reduceByKey(add)
       output = counts.collect()
       for (word, count) in output:
           print("%s: %i" % (word, count))
   
       spark.stop()
   ```

4. 运行验证
