---
title: spiderKeeper接口
keywords:
- spiderKeeper接口
- mlzhang
description : "spiderKeeper接口"
---
创建项目

```python

create project
url: /project/create
method: post
    form
```

上传egg文件

```python
deploy submit   upload egg
url: /project/1/spider/upload
method: post
    form
```

删除项目

```python
manage  delete project
url: /project/1/delete 
method: get
    
```



创建定时任务

```python

Periodic Jobs  add job  创建定时任务
url: /project/1/job/add/
method: post
	form
```

运行一次

```python
run once    create
url : /project/1/job/add
method: post
	form
```

停止任务

```
/project/1/jobexecs/3/stop
```

查看日志

```python

log
url: /project/1/jobexecs/1/log
method: get
```



和scrapyd通讯文件

```python
app => proxy => contrib => scrapy.py   ScrapydProxy
```