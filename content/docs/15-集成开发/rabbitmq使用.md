---
title: rabbitmq使用
keywords:
- rabbitmq使用
- mlzhang
description : "rabbitmq使用"
---
参考链接

https://tests4geeks.com/python-celery-rabbitmq-tutorial/

```shell

# 安装
sudo apt-get install rabbitmq-server

# 设置用户和权限

# add user 'jimmy' with password 'jimmy123'
$ rabbitmqctl add_user jimmy jimmy123
# add virtual host 'jimmy_vhost'
$ rabbitmqctl add_vhost jimmy_vhost
# add user tag 'jimmy_tag' for user 'jimmy'
$ rabbitmqctl set_user_tags jimmy jimmy_tag
# set permission for user 'jimmy' on virtual host 'jimmy_vhost'
$ rabbitmqctl set_permissions -p jimmy_vhost jimmy ".*" ".*" ".*"
```