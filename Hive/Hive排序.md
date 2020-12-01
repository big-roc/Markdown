## Hive 排序

### 1. 全局排序（Order By）

Order By：全局排序，只有一个Reducer。

### 2. 每个MapReduce内部排序（Sort By）

Sort By：对于大规模的数据集order by的效率非常低。在很多情况下，并不需要全局排序，此时可以使用sort by。

sort by为每个reducer产生一个排序文件。每个Reducer内部进行排序，对全局结果集来说不是排序。

```shell
# 设置reduce个数
set mapreduce.job.reduces=3;
# 查看设置的reduce个数
set mapreduce.job.reduces;
```

### 3. 分区排序（Distribute By）

Distribute By：在有些情况下，我们需要控制某个特定行应该到那个reducer，通常是为了进行后续的聚集操作。
distribute by子句可以做这件事。distribute by类似MR中partition（自定义分区），进行分区，结合sort by使用。

### 4. Cluster By

当distribute by和sort by字段相同时，可以使用cluster by方式。