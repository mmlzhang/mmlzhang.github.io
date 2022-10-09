---
title: go异常处理
keywords:
- go异常处理
- mlzhang
description : "go异常处理"
---
使用 `github.com/pkg/erros`, 可以说向错误添加上下文，这个方式可以由人也可以由机器检查



```go
// 包含堆栈信息
errors.Wrap(err, "xxx异常")
errors.Wrapf(err, "xxx异常 %s", "xxxx")
// 只包含异常信息
errors.WithMessage(err, "xxx异常")


// 打印异常信息
fmt.Printf("original error: %T %v\n", err.Cause(err), errors.Cause(err))
fmt.Printf("stack trace: \n%+v\n", err)
```