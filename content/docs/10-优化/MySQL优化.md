---
title: MySQL优化
date: 2022-04-30 16:55:47
tags: my-notes
---
### MySQL优化

- SQL优化
- 参数配置优化



#### 对sql语句进行优化

缓存查询语句：

- 不开启缓存的情况：`cursor.execute("select username from user where singup_data>=curdate()");`
- 开启缓存进行查询：`cursor.execute("select * from user where signup_data>=%s", (datetime.now()))`但是有时会有 sql 注入攻击的风险

EXPLAIN：对要执行的 sql 语句的运行过程进行查看

只需要一条数据时使用 LIMIT 1 

- `cursor.execute("select 1 from user where country='china' limit 1");`

使用索引：

- `create index ix_tablename_colname on table(col1, col2);`

在Join 表时使用相同的类型的字段，并将其索引

- 如下语句中，两个 state 中应该是创建过索引的，而且是相同的类型，相同的字符集

```python
cursor.execute("select company_name from users left join conpanies on (users.state=companies.state) where users.id=%s", (user_id))
```

避免使用 select * ， 使用具体的属性进行查询，避免 select * 的二次查询

每张表都应该创建一个 主键 id

尽可能使用 NOT NULL 避免空值

固定长度的表会让查询的速度更快

垂直分割：将数据库中的一张表分为几张表，降低查询时得到的不是必须的字段，降低表的复杂度和字段的数目

- 另外：被分出去的字段形成的表，一般不会经常性的 Join 它们，不然会让性能比分割前还差

拆分大的 DELETE 或 INSERT 语句

- 使用 limit 限制每次操作的数据的条数，避免锁表

选择正确的存储引擎

- InnoDB：支持 "行锁"，在写入操作时性能更好，对事物比较支持
- MysSAM：适合大量查询时使用，但是对大量的写入操作不是很好

查看存储引擎

- `show create table lesson;`

#### 优化参数配置

back_log=500

- 默认时50，back_log指在 MySQL中暂时停止回答请求之前的短时间有多少个请求可以被存在栈中，也就时当连接数达到 max_connections时，新的请求会被存在 栈 中等待响应，即 back_log的数量，但是超过back_log的请求将会无法响应
- 查看当前的数量`show variables like 'back_log';` 

wait_timeout参数，由默认的8小时，修改为30分钟

- wait_timeout=1800
- `show variables like 'wait_timeout'`

max_connection，修改默认值，由默认的151，修改为3000

- max_connections=3000

max_user_connection,默认是 0 ， 修改为 800

- max_user_connections=800
- 针对某一账号的所有客户端并行连接在 mysql 服务的最大并行连接数，简单说是指同一个账号能够同时连接到 mysql服务的最大连接数，设置为 0表示不限制
- `show variables like 'max_user_connections'`

修改 thread_concurrency，默认是8，修改为64

- thread_concurrency=64
- 这个应该设置为CPU核数的两倍，
- `show variables like 'thread_concurrency'`

default-storage-engine=InnoDB，存储引擎设置

- `create table mytable (id int, title char(20)) ENGINE=INNODB;`
- `show variables like '%max_connections%'`



创建索引：

ix

ux

```mysql

cerate index ix_tablename_column on 

# 查询语句
explain  + sql 查看查询的详细信息, 性能分析
```

0

![1533695231027](assets/1533695231027.png)



![1533695548364](assets/1533695548364.png)