---
title: 安装python3
date: 2022-04-30 16:55:47
tags: my-notes
---
### 安装python3.6

在centos中，系统默认只提供python2.7的版本，但是项目我们使用的python3.6的版本。所有我们自己安装python3

```shell
# 安装python3全部命令 root权限
yum -y groupinstall "Development tools" && yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel && wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tar.xz && tar -xvJf  Python-3.6.2.tar.xz && cd Python-3.6.2 && ./configure --prefix=/usr/local/python3 && make && make install && ln -s /usr/local/python3/bin/python3 /usr/bin/python3 && ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3 && yum install python-virtualenv
```