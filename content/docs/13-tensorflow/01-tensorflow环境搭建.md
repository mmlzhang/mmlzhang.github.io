---
title: 01-tensorflow环境搭建
keywords:
- 01-tensorflow环境搭建
- mlzhang
description : "01-tensorflow环境搭建"
---
## 01-tensorflow环境搭建





#### tf 的主要依赖

#### 1. Protocol Buffer

- 一款谷歌开发的处理结构化数据的工具，处了Protocol Buffer 外还有 XML / JSON等结构化数据处理工具
- 其序列化后的数据是二进制流，需要先定义数据的格式（schema）
- 数据占用空间小，解析时间快
- 文件格式   .proto，每个 message 代表了一类结构化的数据

```protobuf

message user{
    optional string name = 1;
    required int32 id = 2;
    repeated string email = 3;
}
```

如上：message 里定义了每一个属性的类型和名字。



#### Bazel

- 谷歌开源的自动化构建工具，谷歌内部绝大部分的应用都是通过它来编译的



安装

`pip install tensorflow`

建议使用 anaconda

`conda install tensorflow`