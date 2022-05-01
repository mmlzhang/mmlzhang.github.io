---
title: 安装mariaDB
date: 2022-04-30 16:55:47
tags: my-notes
---
### 安装MariaDB

安装命令

```
yum -y install mariadb mariadb-server
```

安装完成MariaDB，首先启动MariaDB

```
systemctl start mariadb
```

设置开机启动

```
systemctl enable mariadb
```

### 设置密码

```python
 mysql_secure_installation
```

```
Enter current password for root:<–初次运行直接回车

设置密码

Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车

New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码

其他配置

Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车

Disallow root login remotely? [Y/n] <–是否禁止root远程登录,回车,

Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车

Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车

初始化MariaDB完成，接下来测试登录

mysql -u root -p password
```

### 开启远程连接

在mysql数据库中的user表中可以看到默认是只能本地连接的，所有可以添加一个新的用户，该用户可以远程访问

#### 1. 创建用户

```
# 先使用数据库
use mysql;

# 针对ip
create user 'root'@'192.168.10.10' identified by 'password';

#全部
 create user 'root'@'%' identified by 'password';
```

#### 2. 授权

```
# 给用户最大权限
grant all privileges on *.* to 'root'@'%' identified by 'password';

# 给部分权限(test 数据库)

grant all privileges on test.* to 'root'@'%' identified by 'password' with grant option;

# 刷新权限表
```

 	`flush privileges;`

```
# 查看
show grants for 'root'@'localhost';
```

接下来就可以在远程的数据库可视化工具中直接访问该服务器中的mysql了。

```
# 访问数据库
mysql -u root -p
```

###