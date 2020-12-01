题目：有销售表T。样例数据如下，请用sql查出每个员工的年累计销售额，查询结果如表L。

样例表T：

| Name(姓名) | Month(月份) | Sale(销售额) |
| ---------- | ----------- | ------------ |
| emi        | 201801      | 1000         |
| emi        | 201802      | 1100         |
| emi        | 201803      | 1200         |
| tommy      | 201801      | 2000         |
| tommy      | 201802      | 2100         |
| tommy      | 201803      | 2200         |

结果表K：

| Name(姓名) | Month(月份) | Sale(销售额) | Total(年销售额) |
| ---------- | ----------- | ------------ | --------------- |
| emi        | 201801      | 1000         | 1000            |
| emi        | 201802      | 1100         | 2100            |
| emi        | 201803      | 1200         | 3300            |
| tommy      | 201801      | 2000         | 2000            |
| tommy      | 201802      | 2100         | 4100            |
| tommy      | 201803      | 2200         | 6300            |

1.准备数据sale.txt

```shell
emi,201801,10000
emi,201802,11000
emi,201803,12000
tommy,201801,20000
tommy,201802,21000
tommy,201803,22000
```

2.建表

```mysql
use tmp;
create table if not exists tmp.sale
(
name    string  comment '名字',
month   bigint  comment '月份',
sale    int     comment '销售额'
)
comment '销售表'
row format delimited fields terminated by ',' stored as textfile;
```

3.加载数据

```mysql
load data local inpath '/home/odsrpdev/dapeng/test/sale.txt' into table tmp.sale;
```

4.查询语句和结果

```mysql
select
name,
month,
sale,
sum(sale) over (partition by name order by month) as total
from sale;

emi		201801	10000	10000
emi		201802	11000	21000
emi		201803	12000	33000
tommy	201801	20000	20000
tommy	201802	21000	41000
tommy	201803	22000	63000

select
name,
month,
sale,
lag(sale,1,0) over (partition by name order by month) as total
from sale;

emi		201801	10000	0
emi		201802	11000	10000
emi		201803	12000	11000
tommy	201801	20000	0
tommy	201802	21000	20000
tommy	201803	22000	21000
```

