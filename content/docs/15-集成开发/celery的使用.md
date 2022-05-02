---
title: celery的使用
keywords:
- celery的使用
- mlzhang
description : "celery的使用"
---
celery的使用



https://blog.csdn.net/Shyllin/article/details/80940643

https://www.cnblogs.com/forward-wang/p/5970806.html



启动命令

celery -A task_dir_name beat

```
http://127.0.0.1:5555/user/celery/?v1=aaaa&v2=bbb
```


可使用 AWS 的 SQS 和 DynmoDB

windows

```

pip install eventlet
celery -A <mymodule> worker -l info -P eventlet
```

http://www.cnblogs.com/cwp-bg/p/8759638.html



celery_aaa

```shell
celery -A celery_aaa worker --loglevel=info

python3 -m celery_aaa.run_tasks
```

可视化 celery 和 rabbitmq

```shell
pip install flower
```



所有的任务会存在本地的 schedule文件中

当没有发布任务时 worker 会等待，直到有任务发布

当没有worker存在时，会一直发布任务，直到worker出现



启动任务发布时会返回它的pid，可以通过kill pid 来停止



celery_aaa

```python
# 运行worker
celery -A celery_aaa worker --loglevel=info

# 导入任务
from  celery_util.celery_aaa.tasks import longtime_add

longtime_add.delay(1, 2)
```