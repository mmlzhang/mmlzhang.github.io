---
title: Nginx_uwsgi
date: 2022-04-30 16:55:47
tags: my-notes
---
### Nginx+uwsgi安装配置

```python
yum install epel-release && yum install nginx && systemctl start nginx && firewall-cmd --permanent --zone=public --add-service=http && firewall-cmd --permanent --zone=public --add-service=https && firewall-cmd --reload &&systemctl enable nginx && pip3 install uwsgi && ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi
```

#### 4.1 配置nginx.conf文件

<b>首先</b>：编写自己项目的nginx.conf文件如下：

每一个项目对应有一个自己定义的nginx的配置文件，比如爱鲜蜂项目，我定义为axfnginx.conf文件

```
server {
     listen       80;
     server_name 39.104.176.9 localhost;

     access_log /home/logs/access.log;
     error_log /home/logs/error.log;

     location / {
         include uwsgi_params;
         uwsgi_pass 127.0.0.1:8890;
     }
     location /static/ {
         alias /home/src/axf/static/;
         expires 30d;
     }

 }
```

<b>其次</b>：修改总的nginx的配置文件，让总的nginx文件包含我们自定义的项目的axfnginx.conf文件

总的nginx配置文件在：/etc/nginx/nginx.conf中

![图](../06-Django/02-Django%E8%BF%9B%E9%98%B6/django/images/django_centos_nginx_peizhi.png)

以上步骤操作完成以后，需要重启nginx：

```
systemctl restart nginx
```

如果自定义的axfnginx.conf文件没有错误的话，查看nginx的运行状态会有如下的结果：

![图](../06-Django/02-Django%E8%BF%9B%E9%98%B6/django/images/django_centos_nginx_status.png)

#### 4.2 配置uwsgi文件

在conf文件夹下除了包含自定义的axfnginx.conf文件，还有我们定义的uwsgi.ini文件

```shell
[uwsgi]
projectname = axf
base = /home/src

# 守护进程
master = true

# 进程个数
processes = 4

# 虚拟环境
pythonhome = /home/env/axfenv

# 项目地址
chdir = %(base)/%(projectname)

# 指定python版本
pythonpath = /usr/local/python3/bin/python3

# 指定项目的wsgi文件
module = %(projectname).wsgi

# 和nginx通信地址:端口
socket = 127.0.0.1:8890

# 日志文件地址
logto = /home/logs/uwsgi.log
```

	
运行项目:

```shell
uwsgi --ini uwsgi.ini
```