---
title: MySQL主从分离
date: 2022-04-30 16:55:47
tags: my-notes
---
MySQL主从分离，读写分离

>  Orical适合做集群，没有主从



磁盘阵列，raid 

#### 数据库日志

错误日志，数据日志，慢查询日志，启动日志

数据日志

- 全量备份：全部备份，每周
- 增量备份：每天，将增加的部分进行备份，日志中会记录每个对数据库的操作记录，
- 冷备份：关掉数据库备份
- 热备份：不关数据库备份

### 主从分离配置

```python
# master配置
server-id=200   # 一般取IP的最后一组数字
innodb_flush_log_at_trx_commit=2
sync_binlog=1
log-bin=mysql-bin-200
binlog-do-db=  # 同步的数据库的名称,全部可以使用 *
重启MySQL
# 客户端执行命令,授权给 slave
grant replication slave on *.* to 'zhang'@"IP" identified by '123456';
show master status;

# slave 更改配置
server-id=201
innodb_flush_log_at_trx_commit=2
sync_binlog=1
log-bin=mysql-bin-201

# slave mysql客户端执行命令
change master to master_host='10.7.152.77',  # 连接 master的 IP
master_user='zhang',  # master授权的用户
master_password='123456',  # 密码
master_log_file='mysql-bin-200.000002',  # master 的日志位置
master_log_pos=448;  # master 的 Position

start salve;
show slave status\G; # 查看状态
```

![1533709204033](assets/1533709204033.png)







Nginx

单点故障：

webServer的优势



AppServer：

- uwsgi：网关接口，WSGI 网管协议，uwsgi时WSGI的一种实现方式，时沟通Nginx和django的桥梁