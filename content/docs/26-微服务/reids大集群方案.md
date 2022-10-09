---
title: reids大集群方案
keywords:
- reids大集群方案
- mlzhang
description : "reids大集群方案"
---
由于redis核心是单线程，想要最大化使用redis的性能，最好使用 1核2G 的实例组成redis集群使用redis



实例数小于500

- 推荐使用redis官方的redis-cluster方案

大于500

- 在redis实例集群上做一层proxy， 通过porxy路由到对应的redis实例中



redis大集群持久化问题

- 首先应该遵循redis的协议
- 有一块超大的缓存作为内存
- 内存和rocksDB进行数据交换

![image-20220911204723221](/assets/image-20220911204723221.png)