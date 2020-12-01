## Hive 行列转换

### 1. 行转列

- 需求

  把星座和血型一样的人归类到一起。结果如下：

  ```tex
  射手座,A            大海|凤姐
  白羊座,A            孙悟空|猪八戒
  白羊座,B            宋宋
  ```

- 数据准备

  ```tex
  name	constellation	blood_type
  孙悟空	白羊座	A
  大海	射手座	A
  宋宋	白羊座	B
  猪八戒	白羊座	A
  凤姐	射手座	A
  ```

- 示例

  ```mysql
  # 建表
  create table person_info(
  name string,
  constellation string,
  blood_type string)
  row format delimited fields terminated by '\t';
  
  # 加载数据
  load data local inpath 'file_path' into table person_info;
  
  # 查询数据
  select
      t1.base,
      concat_ws('|',collect_set(t1.name)) as name
  from (select
      name,
      concat(constellation,',',blood_type) as base
      from person_info) t1
  group by t1.base;
  ```
  
- 相关函数说明

  - concat(string a,string b, ...)：返回输入字符串连接后的结果，支持任意个输入字符串。
  - concat_ws(separator, str1, str2)：它是一个特殊形式的concat()。第一个参数为剩余参数的分隔符，如果分隔符为null，返回值也为null。
  - collect_set(col)：函数只接受基本数据类型，它的主要作用是将某字段的值进行去重汇总，产生array类型字段。

### 2. 列转行

- 需求

  将电影分类中的数组数据展开。结果如下：

  ```tex
  《疑犯追踪》     悬疑
  《疑犯追踪》     动作
  《疑犯追踪》     科幻
  《疑犯追踪》     剧情
  《Lie to me》   悬疑
  《Lie to me》   警匪
  《Lie to me》   动作
  《Lie to me》   心理
  《Lie to me》   剧情
  《战狼2》        战争
  《战狼2》        动作
  《战狼2》        灾难
  ```

- 数据准备

  ```tex
  movie	category
  《疑犯追踪》	悬疑,动作,科幻,剧情
  《Lie to me》	悬疑,警匪,动作,心理,剧情
  《战狼2》	战争,动作,灾难
  ```

- 示例

  ```mysql
  # 建表
  create table movie_info(
  movie string,
  category array<string>)
  row format delimited fields terminated by '\t'
  collection items terminated by ',';
  
  # 加载数据
  load data local inpath 'file_path' into table movie_info;
  
  # 查询数据
  select
  	movie,
  	category_name
  from movie_info lateral view explode(category) tmp_table as category_name;
  ```

- 相关函数说明

  - explode(col)：将hive一行中复杂的array或者map结构拆分成多行。
  - lateral view
    - 用法：lateral view udtf(expression) table_alias as column_alias
    - 解释：用于和split，explode等UDTF一起使用，它能够将一列数据拆分成多行数据，在此基础上可以对拆分后的数据进行聚合。

