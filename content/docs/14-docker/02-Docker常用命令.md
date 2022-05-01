---
title: 02-Docker常用命令
date: 2022-04-30 16:55:47
tags: my-notes
---
常用命令



```shell
service docker start / stop / restart    # 启动 停止  重启
docker images  #　查看多有的镜像
docker ps   # 查看已启动的容器列表
docker ps -a  # 查看docker创建的所有容器
```

docker创建一个容器

```shell
docker run -it -v /docker_test:/yufei --name docker_name
-i: 允许对容器内的（STDIN)进行交互
-t: 在新容器内指定一个伪终端或终端
-v:  挂载宿主机的目录， /docker_test是宿机目录，/yufei是当前docker容器的目录，宿机必须是绝对的
--name: 容器的名称，如果省略将会随机产生一个名字
```

docker启动、停止、重启某个容器

```shell
docker start container_name
dcoker stop container_name
dcokre restart container_name
```

查看指定容器的日志记录

```shell
docker logs -f container_name
```

删除某个容器

```shell
docker rm container_name
```

删除全部容器

```she&#39;l
docker rm $(docker ps -a -q)
```

删除镜像

```shell
docker rmi image_name
dcoker rmi -f $(docker images)
```



指定端口映射启动镜像

```shell
docker run -d -p 91:80 nginx
-d  表示后台运行
-P  随机映射端口
-p 指定端口有四种形式
	- ip:hostPort:containerPort
	-  ip::containerPort
	-  hostPort：containerPort
	-  containerPort
```

强行停止容器

```shell
docker kill 容器ID/容器名称
```

进入容器

- 某些场景下，可能需要进入运行终端容器尽享一些操作

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG ...]

docker exec -it ofe2f388c80ad /bin/bash   # 进入该容器并开启一个bash
```

暂停容器

```shell
docker pause CONTAINER
```

开启运行暂停的容器

```shell
docker unpause CONTAINER
```

查看容器或者镜像的详细信息

```shell
docker inspect [OPTIONS] NAME|ID [NAME|ID ...]
docker inspect NAME|ID
docker inspect --format "{{.State.Pid}}" Name|ID
docker inspect --format "{{ .NetworkSetting.IPAddress }}" NAME|ID
```