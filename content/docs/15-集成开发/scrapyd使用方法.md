---
title: scrapyd使用方法
date: 2022-04-30 16:55:47
tags: my-notes
---
scrapyd使用方法



发布爬虫任务

`scrapyd-deploy 001 -p lianjiaSpider`

打包egg文件

`scrapyd-deploy --build-egg lianjia.egg`



**1 启动**

`scrapyd`

**2 发布工程到scrapyd**

`scrapyd-deploy <target> -p <project>`

- `scrapyd-deploy scrapyd1 -p Crawler`

**3 验证是否发布成功**

`scrapyd-deploy -L <target>`

`scrapyd-deploy -L scrapyd1`

也可以  `scrapyd-deploy -l`

**4 启动爬虫**

```shell
curl http://192.168.2.333:6800/schedule.json -d project=Crawler -d spider=CommonSpider
```

**5 终止爬虫**

```shell
curl http://192.168.2.333:6800/cancel.json -d project=Crawler -d job8270364f9d9811e5adbf000c29a5d5be
```



参考链接  使用scrapyd

https://www.jianshu.com/p/f0077adb74bb