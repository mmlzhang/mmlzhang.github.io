---
title: Celery和RabbitMQ
date: 2022-04-30 16:55:47
tags: my-notes
---
Celery和RabbitMQ



Celery是Distributed Task Queue，分布式任务队列，分布式决定了可以有多个 worker 的存在，队列表示其是异步操作，即存在一个产生任务提出需求的 master，和很多的等待分配的 工人

在 Python 中定义Celery，引入了 Broker 中间件，当 worker 处理完之后还会有一个 backend

![1533863030188](assets/1533863030188.png)



**任务模块 Task**

包含异步任务和定时任务，其中，异步任务通常在业务逻辑中被触发并投放在任务队列中，而定时任务也会有 Celery Beat 进程周期性地将任务发往任务队列

**消息中间件 Broker**

任务调度队列，官方推荐使用 RabbitMQ和Redis等

**任务执行单元 Worker**

监控消息队列，获取队列中调度的任务，并执行它

**任务结果 Backend**

存储任务处理的结果，存储也可以使用RabbitMQ，Redis, MongoDB



```python

# 安装 RabbitMQ
sudo apt-get install rabbitmq-server

# 启动
sudo rabbitmq-server -detached
# 停止
sudo ranbbitmqctl stop
# 设置 RabbitMQ
sudo rabbitmqctl add_user USER_NAME PASSWORD  #创建用户
sudo rabbitmqctl add_vhost VHOST_NAME  # 添加 vhost 的名称
sudo rabbitmqctl set_user_tags USER_NAME USER_TAGS_NAME  # 添加tags
sudo rabbitmqctl set_permissions VHOST_NAME USER_NAME '.*' '.*' '.*'
rabbitmqctl list_queues -p VHOST_NAME

# 安装 celery
pip3 install celery

# 返回的 broker_url
broker_url = 'amqp://USER_NAME:PASSWORD@localhost:5672/VHOST_NAME'

# 编辑 task.py 任务文件
import time
from celery import Celery

app = Celery("task", backend='amqp://USER_NAME:PASSWORD@localhost:5672/VHOST_NAME',
            broker='amqp://USER_NAME:PASSWORD@localhost:5672/VHOST_NAME')

@app.task
def add(x, y):
    time.sleep(3)
    return x + y

# 运行 celery worker
nohup celery -A task worker --loglevel=info &

# 查看执行，进入 python3 交互环境
from task import add
result = add.delay(4,4)
result.ready()
result.get(timeout=1)
result.status
result.id
```