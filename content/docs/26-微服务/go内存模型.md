---
title: go内存模型
keywords:
- go内存模型
- mlzhang
description : "go内存模型"
---
share memory by communicating

通过通信共享内存



使用go channel 通信





## channels

- channels是一种类型安全的消息队列，充当两个goroutine直接的通道

### 无缓冲

- 不设置 chan的缓冲，接受者和发送者必须是同步的
- 用于同步通讯
- `make(chan struct{}{})`

### buffered channels

- `make(chan string, 2)`
- Send先于Receive发生
- 好处: 延迟更小
- 代价: 不保证数据到达，越大的buffer, 越小的保障到达, buffer = 1 时，给你延迟一个小时的保障
- 并不是buffer越大性能越好