### Hive 建表语法

```mysql
create [external] table [if not exists] table_name
[(col_name data_type [comment col_comment], ...)]
[comment table_comment]
[partitioned by (col_name data_type [comment col_comment], ...)]
[row format delimited fields terminated by char]
[stored as file_format]
[location hdfs_path]
```



