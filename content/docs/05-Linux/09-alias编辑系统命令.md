---
title: 09-alias编辑系统命令
date: 2022-04-30 16:55:47
tags: my-notes
---
```shell

vi ~/.bashrc

添加别名
```





在profile中设置PATH

`vi /etc/profile`

找到export行，在下面新增加一行，内容为：


```shell
export PATH=$PATH:/usr/local/apache/bin
export PATH=$PATH:/home/lanms/Desktop/software/navicat121_premium_cs_x64
```

`export PATH=$PATH:/usr/local/apache/bin`  后面为想要添加的服务所在的文件目录
注：＝ 等号两边不能有任何空格。这种方法最好,除非手动强制修改PATH的值,否则将不会被改变。
编辑/etc/profile后PATH的修改不会立马生效，如果需要立即生效的话，可以执行

source profile命令苏