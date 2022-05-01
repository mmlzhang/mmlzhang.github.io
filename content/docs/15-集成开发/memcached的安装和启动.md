---
title: memcached的安装和启动
date: 2022-04-30 16:55:47
tags: my-notes
---
README



#### windows创建服务

```powershell
schtasks /create /sc onstart /tn memcached /tr "'D:\softWare\memcached-amd64\memcached.exe' -m 512 -p 10000 "

```

删除服务

```powershell
schtasks /delete /tn memcached
```

windows启动服务

```powershell
memcached  -u root -l 0.0.0.0 -p 10001 -c 1024 -P D:\softWare\memcached-amd64\Pid\memcached.pid
```



#### 启动memcached

```powershell
memcached -d -m 10 -u root -l 0.0.0.0 -p 12000 -c 256 -P /tmp/memcached.pid

参数说明:
    -d 是启动一个守护进程
    -m 是分配给Memcache使用的内存数量，单位是MB
    -u 是运行Memcache的用户
    -l 是监听的服务器IP地址
    -p 是设置Memcache监听的端口,最好是1024以上的端口
    -c 选项是最大运行的并发连接数，默认是1024，按照你服务器的负载量来设定
    -P 是设置保存Memcache的pid文件
```



常用命令

```powershell
存储命令: set/add/replace/append/prepend/cas
获取命令: get/gets
其他命令: delete/stats..
```

https://www.cnblogs.com/wang-yc/p/5693268.html