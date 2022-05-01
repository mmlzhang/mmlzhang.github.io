---
title: 01-CentOS7安装docker
date: 2022-04-30 16:55:47
tags: my-notes
---
### CentOS 7 安装docker



`yum update`

`vim /etc/yum.repos.d/docker.repo`

```shell
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg

```

`yum install docker-engine`   安装docker包

`systemctl start docker`   启动docker服务

`mkdir /etc/systemd/system/docker.service.d`

`vim /etc/sysytemd/system/docker.service.d/http-proxy.conf`   # 添加代理

```shell
[Service]                                                                       Enviroment=”HTTP_PROXY=http://用户名：密码@代理地址：端口号”

 例子   
[Service]
Environment="HTTP_PROXY=http://proxy.ip.com:80" 
```