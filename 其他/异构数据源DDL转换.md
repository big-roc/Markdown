### MySQL建表转化为Hive建表

1. 建一张维表

   ```mysql
   CREATE TABLE dim_ddl_convert (
   	source VARCHAR (100) NOT NULL,
   	data_type1 VARCHAR (100) NOT NULL,
   	target VARCHAR (100) NOT NULL,
   	data_type2 VARCHAR (100),
   	update_time VARCHAR (26),
   	PRIMARY KEY (source, data_type1, target)
   ) ENGINE = INNODB DEFAULT CHARSET = utf8 COMMENT = '数据库表结构转换';
   ```

2. 插入MySQL数据类型与Hive数据类型的映射关系（Hive 0.11.0）

   ```mysql
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'tinyint', 'hive', 'tinyint', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'smallint', 'hive', 'smallint', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'mediumint', 'hive', 'int', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'int', 'hive', 'int', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'bigint', 'hive', 'bigint', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'float', 'hive', 'float', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'double', 'hive', 'double', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'numeric', 'hive', 'decimal', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'decimal', 'hive', 'decimal', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'char', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'varchar', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'text', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'binary', 'hive', 'binary', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'varbinary', 'hive', 'varbinary', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'time', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'date', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'datetime', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'timestamp', 'hive', 'string', '2020-03-14 00:00:00');
   INSERT INTO dim_ddl_convert (source, data_type1, target, data_type2, update_time) VALUES ('mysql', 'json', 'hive', 'map<string,string>', '2020-03-14 00:00:00');
   ```

   | MySQL     | Hive               |                              |
   | --------- | :----------------- | ---------------------------- |
   | tinyint   | tinyint            |                              |
   | smallint  | smallint           |                              |
   | mediumint | int                |                              |
   | int       | int                |                              |
   | bigint    | bigint             |                              |
   | float     | float              |                              |
   | double    | double             |                              |
   | numeric   | decimal            | 从Hive0.11.0开始支持         |
   | decimal   | decimal            | 从Hive0.11.0开始支持         |
   | char      | string             |                              |
   | varchar   | string             |                              |
   | text      | string             |                              |
   | binary    | binary             | 从Hive0.8.0开始支持          |
   | varbinary | varbinary          |                              |
   | time      | string             | 不支持time                   |
   | date      | date               | **从Hive0.12.0开始支持**     |
   | datetime  | string             | 不支持datetime               |
   | timestamp | string             | 从Hive0.8.0开始支持timestamp |
   | char      | char               | **从Hive0.13.0开始支持**     |
   | varchar   | varchar            | **从Hive0.12.0开始支持**     |
   | json      | map<string,string> |                              |
   | 不支持    | array              |                              |
   | 不支持    | map<key,value>     |                              |
   | 不支持    | struct             |                              |
   | 不支持    | union              |                              |

3. 打开SQL查询工具，执行以下查询

   ```mysql
   set session group_concat_max_len = 102400;
   select
   	a.table_name,
   	b.table_comment,
   	concat(
   		'drop table if exists ',a.table_name,';'
   		'create table if not exists ',a.table_name,' (',
   		group_concat(
   			concat(
   				a.column_name,' ',c.data_type2," comment '",column_comment,"'"
   			)
   			order by
   				a.table_name,
   				a.ordinal_position
   		),
   		") comment '",b.table_comment,"'",
   		"row format delimited fields terminated by '\\t' stored as textfile;"
   	) as col_name
   from
   	(
   		select
   			table_schema,
   			table_name,
   			column_name,
   			ordinal_position,
   			data_type,
   			column_comment
   		from
   			information_schema.columns
   		where
   			table_schema = 'test_dp'
   		and table_name = 'ods_cdn_url_akamai_bk'
   	) as a
   left join information_schema.tables as b 
   on a.table_name = b.table_name and a.table_schema = b.table_schema
   left join (
   	select
   		*
   	from
   		dim_ddl_convert
   	where
   		source = 'mysql'
   	and target = 'hive'
   ) as c on a.data_type = c.data_type1
   group by
   	a.table_name,
   	b.table_comment;
   ```
   

