---
title: 01-基础开始tornado
keywords:
- 01-基础开始tornado
- mlzhang
description : "01-基础开始tornado"
---
### Tornado简介

Tornado全称Tornado Web Server，是一个用Python语言写成的Web服务器兼Web应用框架，由FriendFeed公司在自己的网站FriendFeed中使用，被Facebook收购以后框架在2009年9月以开源软件形式开放给大众。

特点：

- 作为Web框架，是一个轻量级的Web框架，类似于另一个Python web框架Web.py，其拥有异步非阻塞IO的处理方式。
- 作为Web服务器，Tornado有较为出色的抗负载能力，官方用nginx反向代理的方式部署Tornado和其它Python web应用框架进行对比，结果最大浏览量超过第二名近40%。



### Tornado的特性

#### HTTP服务器

Tornado为了高效实现Comet/后端异步调用HTTP接口，是直接内嵌了HTTP服务器。

前端无需加apache / lighttpd / nginx等也可以供浏览器访问；但它并没有完整实现HTTP 1.1的协议，所以官方文档是推荐用户在生产环境下在前端使用nginx，后端反向代理到多个Tornado实例。

Tornado本身是单线程的异步网络程序，它默认启动时，会根据CPU数量运行多个实例；充分利用CPU多核的优势。



#### 单线程异步 

网站基本都会有数据库操作，而Tornado是单线程的，这意味着如果数据库查询返回过慢，整个服务器响应会被堵塞。

数据库查询，实质上也是远程的网络调用；理想情况下，是将这些操作也封装成为异步的；但Tornado对此并**没有**提供任何支持。

这是Tornado的**设计**，而不是缺陷。

一个系统，要满足高流量；是必须解决数据库查询速度问题的！

数据库若存在查询性能问题，整个系统无论如何优化，数据库都会是瓶颈，拖慢整个系统！

异步并**不能**从本质上提到系统的性能；它仅仅是避免多余的网络响应等待，以及切换线程的CPU耗费。

如果数据库查询响应太慢，需要解决的是数据库的性能问题；而不是调用数据库的前端Web应用。

对于实时返回的数据查询，理想情况下需要确保所有数据都在内存中，数据库硬盘IO应该为0；这样的查询才能足够快；而如果数据库查询足够快，那么前端web应用也就无将数据查询封装为异步的必要。

就算是使用协程，异步程序对于同步程序始终还是会提高复杂性；需要衡量的是处理这些额外复杂性是否值得。

如果后端有查询实在是太慢，无法绕过，Tornaod的建议是将这些查询在后端封装独立封装成为HTTP接口，然后使用Tornado内置的异步HTTP客户端进行调用。





#### 开始 tornado 项目

```python
# -*-coding: utf-8 -*-


import tornado.web
import tornado.ioloop

# 引入 httpserver 模块
import tornado.httpserver


class IndexHandler(tornado.web.RequestHandler):

    def get(self, *args, **kwargs):
        self.write("Hello Tornado!")


if __name__ == '__main__':
    app = tornado.web.Application([
        (r'/', IndexHandler),
    ])
    # 直接使用 app 监听端口，最简单的写法，只能在单进程模式中使用
    # app.listen(8000)

    # 实例化一个 http 服务器对象, 匹配 app 中的路由
    httpServer = tornado.httpserver.HTTPServer(app)
    # 绑定端口，默认启动一个进程
    # httpServer.listen(8000)

    # 绑定端口
    httpServer.bind(8000)
    # 启动的进程的个数，默认开启一个进程，为 None 或者负数，也会开启对应的cpu核数个进程
    httpServer.start(num_processes=5)
    # 一般不使用 tornado 提供的方法启动多个进程，使用手动方法启动进程，绑定不同的端口
    # 1.每个子进程都会复制一份 ioloop 的实列，如果创建子进程前修改了 ioloop 会影响到多有的子进程
    # 2.所有的进程都是一个命令启动的，无法做到在不停止服务的情况下修改代码
    # 3.所有进程共享一个端口，很难进行分别监控

    # 开始监听, 监听 epoll 中的请求
    tornado.ioloop.IOLoop.current().start()

```