---
title: 10-git_rebase
date: 2022-04-30 16:55:47
tags: my-notes
---
https://www.jianshu.com/p/4a8f4af4e803



```sh
pick：保留该commit（缩写:p）
reword：保留该commit，但我需要修改该commit的注释（缩写:r）
edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
squash：将该commit和前一个commit合并（缩写:s）
fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
exec：执行shell命令（缩写:x）
drop：我要丢弃该commit（缩写:d）

```



s: 提供可编辑界面，编辑commit

f: 自动融合，放弃当前的commit内容

r: 需要继续　git rebase --continue 进行编辑commit 

e: 需要继续执行命令　git commit --admend 修改commit 

d: 删除commit ,慎用！

p: 保留commit

x: 执行cmd shell 命令