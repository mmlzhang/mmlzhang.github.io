---
title: 06-Linux操作命令
date: 2022-04-30 16:55:47
tags: my-notes
---
## Linux常用命令





chmod a+x file.txt  赋予文件权限

echo  回声命令

printf 打印  可以格式化输出

cat 查看文档

`rpm -q package_name`  确认安装包已成功安装

`sysctl -p` 验证Linux 核心参数



\$\$   当前的PID

\$? 检测命令是否运行成功   -0 标准输入 -1 标准输出 -2 标准错误  -其他 错误

\$*  运行的参数的个数

\$@ 当前文件

$0 当前运行的文件

$1  第一个参数



`read  var`  读取输入，赋值给 var

-d  path  判断是否为目录   `[ -d /home ] && echo yes`



pgrep xxx  获得xxx的进程ID

ps -ef |grep xxx 查看完成的进程信息

`cat/proc/$PID/environ | tr '\0' '\n'`  查看PID 进程的环境变量，替换 空 为换行输出



{}  分隔变量和普通字符   `${fruit}(s)`



\>  输出到文件，每次输出会覆盖原内容

\>>  追加的方式输出到文件



别名

`alias  new_command="command sequence"`    

- eg: `alias rm='cp $@ ~/backup && rm $@'` 备份当前文件并删除



`tput` 获取终端相关的信息

`date `显示读取的时间

`date +%s `  时间戳显示时间，纪元时间

`data  --data 'Jan 20 2011' +%A`     ===>>  Satarday    对时间进行计算，格式化输出需要的内容

%a  星期简写   %A  全拼

%b %B  月份

%d %D  日期

%y %Y  年



`bash -x script.sh` shell 跟踪调试

`{1..8}`   1-8 的数组

```shell

#! /bin/bash

function DEBUG()
{
    [ "$_DEBUG"=="on" ] && $@ ||:
}
for i in {1..10}
do
	DEBUG echo "I am $i"
done

# 执行
$ _DEBUG=on ./secript.sh

# 在每一条需要打印调试信息的语句前加上DEBUG，如果没有 DEBUG=on 传递给脚本，那么调试信息就不会被打印出来
```



 Frok **炸弹**

`:(){ :|:& }; :`