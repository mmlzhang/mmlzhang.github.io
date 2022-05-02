---
title: 07-Linux命令cat-find
keywords:
- 07-Linux命令cat-find
- mlzhang
description : "07-Linux命令cat-find"
---
常用命令   cat find

[TOC]

### cat

- `-s  ` 去除文件的多余空白行显示，不会对原文件进行修改
- `-n` 显示行号
- `-t` 挂起文件，始终显示最后更行内容

录制终端会话并进行回放

```shell

# 开始录制
script -t 2> timing.log -a output.seeeion

# 回放
scriptreplay timing.log output.session
```



### find

- `-print` 打印查找内容， `\n` 分隔
- `find . -type f -print|xargs ls -l`
- `find . -iname 'filename' -print` 模糊查找
- 逻辑运算关联查找 `find . \( -name '*e*' -and -name 's*'\)`
- -a  -and  关联查找   -o -or 逻辑或
- -regex   正则表达式查找    -iregex
- `!`  否定参数   `find . ！ -name '*.txt' -print`
- `-maxdepth` 指定查找的深度   -mindepth
- `-type` 文件类型     f 普通文件   d 目录
- `-atime`  访问时间     默认是 天 day       -tmin  分钟
- `-mtime` 修改时间 -mmin
- `-ctime` 变化时间   -cmin

- `find . -type f -atime -7 -print`  7天内访问过
- `find . -type f  -amin +7 -print`  5分钟之前访问过的文件

- `-newer` 参考其他文件的访问时间新的访问过的文件
- `-perm`  基于文件的权限  `find . -type f -perm 644 -print`
- `-delete` 删除
- `-exec` 执行其他命令，将查找到的内容作为参数