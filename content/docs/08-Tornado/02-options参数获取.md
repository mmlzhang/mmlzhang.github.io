---
title: 02-options参数获取
date: 2022-04-30 16:55:47
tags: my-notes
---
02-



### options 参数获取

`tornado.options` 经行全局的参数定义，转换，存储



```python
tornado.options.define()
# 参数
name  变量名,唯一性
default  设置选项变量的默认值
type  设置选项变量的类型,进行输入的值的转换， str int float 等
multiple   设置选项变量是否可以为多个值，默认为False
help  选项变量的帮助体视信息


# 例如
tornado.options.define(name="port",default=8000,type=int)

```

`tornado.options.options`   全局的options 对象，多有定义的选项变量都会成为其的属性
如 ： `tornado.options.options.port`



#### 命令行获取参数

`tornado.options.parse_command_line()`  获取命令行参数，转化为 tornado 的参数

```python
# -*-coding: utf-8 -*-


import tornado.web
import tornado.ioloop

# 引入 httpserver 模块
import tornado.httpserver

import tornado.options


# 定义 options 的变量的方法，定义参数
tornado.options.define('port', default=8000)


class IndexHandler(tornado.web.RequestHandler):

    def get(self, *args, **kwargs):
        self.write("Hello Tornado!")


if __name__ == '__main__':
    # 将命令行参数转换为 tornado 的参数并保存到 tornado.options.options 中
    app = tornado.web.Application([
        (r'/', IndexHandler),
    ])
    # 实例化一个 http 服务器对象, 匹配 app 中的路由
    httpServer = tornado.httpserver.HTTPServer(app)
    # 绑定端口，使用变量的值
    httpServer.listen(tornado.options.options.port)
    print(tornado.options.options)
    # 开始监听, 监听 epoel 中的请求
    tornado.ioloop.IOLoop.current().start()

```



命令行启动

![1537073892823](assets/1537073892823.png)



#### 从配置文件导入参数

`tornado.options.parse_config_file(path)`

config 文件中指定参数



### 日志



使用parse_command_line() 或者 parse_config_file()  时，会默认开启日志，在终端输出日志信息

#### 关闭日志

在程序的第一行加入如下代码

```python
tornado.options.options.logging = None
```