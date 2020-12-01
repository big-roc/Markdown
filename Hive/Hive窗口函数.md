### Hive 窗口函数

- 数据准备：user_time.txt

  ```mysql
  UserA,LocationA,2018-01-01 08:00:00,60
  UserA,LocationA,2018-01-01 09:00:00,60
  UserA,LocationB,2018-01-01 10:00:00,60
  UserA,LocationA,2018-01-01 11:00:00,60
  ```

- 需求

  ```mysql
  --结果
  UserA,LocationA,2018-01-01 08:00:00,120
  UserA,LocationB,2018-01-01 10:00:00,60
  UserA,LocationA,2018-01-01 11:00:00,60
  ```

- 示例

  ```mysql
  # 建表
  create table user_time(
  user_name string,
  user_location string,
  start_time timestamp,
  minutes int
  ) row format delimited fields terminated by ',';
  
  # 加载数据
  load data local inpath '/data/tmp/user_time.txt' into table user_time;
  
  # 查询数据
  # step1
  select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime((unix_timestamp(start_time) + (minutes * 60)),'yyyy-MM-dd HH:mm:ss') as end_time,
      row_number() over(partition by user_name,user_location order by start_time) as rn
  from user_time;
  
  # step2
  select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime(unix_timestamp(end_time)-(rn*60*60),'yyyy-MM-dd HH:mm:ss') as end_time
  from (select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime((unix_timestamp(start_time) + (minutes * 60)),'yyyy-MM-dd HH:mm:ss') as end_time,
      row_number() over(partition by user_name,user_location order by start_time) as rn
  from user_time) t1;
  
  # step3
  select
      user_name,
      user_location,
      start_time,
      sum(minutes) over(partition by user_name,user_location,end_time) as sum_minutes,
      row_number() over(partition by user_name,user_location,end_time order by start_time) as rn
  from (select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime(unix_timestamp(end_time)-(rn*60*60),'yyyy-MM-dd HH:mm:ss') as end_time
  from (select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime((unix_timestamp(start_time) + (minutes * 60)),'yyyy-MM-dd HH:mm:ss') as end_time,
      row_number() over(partition by user_name,user_location order by start_time) as rn
  from user_time) t1) t2;
  
  # step4
  select
      user_name,
      user_location,
      start_time,
      sum_minutes
  from (select
      user_name,
      user_location,
      start_time,
      sum(minutes) over(partition by user_name,user_location,end_time) as sum_minutes,
      row_number() over(partition by user_name,user_location,end_time order by start_time) as rn
  from (select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime(unix_timestamp(end_time)-(rn*60*60),'yyyy-MM-dd HH:mm:ss') as end_time
  from (select
      user_name,
      user_location,
      start_time,
      minutes,
      from_unixtime((unix_timestamp(start_time) + (minutes * 60)),'yyyy-MM-dd HH:mm:ss') as end_time,
      row_number() over(partition by user_name,user_location order by start_time) as rn
  from user_time) t1) t2) t3
  where rn=1
  order by start_time;
  ```

