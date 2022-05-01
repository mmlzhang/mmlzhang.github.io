---
weight: 20
---

## 托管到Github Pages

参考文档：
  - https://zhuanlan.zhihu.com/p/350977057

对于托管到Github Pages，官方[文档](https://link.zhihu.com/?target=https%3A//gohugo.io/hosting-and-deployment/hosting-on-github)写的不是那么特别清楚，实际上是非常简单的。

因为是博客，所以遵循Github Pages的规则，项目名称就是`username.github.io`，建一个这样名字的库，权限设置为`public`，默认选项一个都不要勾选，建完了以后，就会看到这样一个界面。

![img](https://pic4.zhimg.com/80/v2-0c1afe34739044fd1b94f16cbbcec83f_1440w.jpg)

我们前文已经初始化过`git`库了，所以这里我们只要把远程库的地址加进来就好了。

在这之前，先随便创建一个`README.md`文件，一会儿用得到它。

```text
studyhugo $ echo "# Blog Contructing......" >> README.md
```

然后创建`master`分支

```text
studyhugo $ git add .
studyhugo $ git branch -M master
studyhugo $ git commit -m "Initial Commit"
studyhugo $ git remote add origin git@github.com:username/username.github.io.git
```

然后`push`上去。

```text
studyhugo $ git push
```

到Github上看到我们Hugo站点源文件已经`push`上来了。

![img](https://pic4.zhimg.com/80/v2-3b6b8d0291ba5cd0e04aa32cd51e02bf_1440w.jpg)

对于`username.github.io`这样的项目名，Github会直接认定为Github Pages并发布。所以现在已经可以访问`https://username.github.io`。

![img](https://pic1.zhimg.com/80/v2-d8e0b8c45e4f34843b32e03b1d59d914_1440w.jpg)

当然，因为Github Pages并不直接支持Hugo站点的发布，所以它发布了我们添加进去的README.md文件。

接下来我们就要添加Github Action，Hugo官方已经给我们准备好了，我们自己要做的事很少，参考官方文档的[这一节](https://link.zhihu.com/?target=https%3A//gohugo.io/hosting-and-deployment/hosting-on-github/%23build-hugo-with-github-action)。

官方提供了一个`yml`文件，文件应该存在`.github/workflows/gh-pages.yml`里。

在项目的选项里选择Actions——

![img](https://pic1.zhimg.com/80/v2-e9b63c17081bb06e98e791d030ba1aa4_1440w.jpg)

点进去以后是这样子的——

![img](https://pic1.zhimg.com/80/v2-03c0a4a827cf18721caa0d6a5c92c1c4_1440w.jpg)

把这里的文件名改成`gh-pages.yml`，删掉当前的内容，然后把官方提供的代码拷贝进来，注意这里官方的代码部署用的分支是`main`，而我们的是`master`，要对应的改掉。

![img](https://pic1.zhimg.com/80/v2-32360ca179a2fc32dd77317c8612b444_1440w.jpg)

```yaml
name: github pages

on:
  push:
    branches:
      - master  # Set a branch to deploy

jobs:
  deploy:
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

然后Commit。

![img](https://pic1.zhimg.com/80/v2-4c5a27fea570f5756bca826491392114_1440w.jpg)

再次回到Actions界面，由于Commit了一个新文件，所以会立刻触发一次构建发布的任务。

![img](https://pic4.zhimg.com/80/v2-9eb18238050fd6b700ae60f950dd7907_1440w.jpg)

如上所示，所有任务完成，回到Code选项里，现在会在分支中看到一个`gh-pages`的分支。

![img](https://pic3.zhimg.com/80/v2-0e9d310c2e34e2683b5820dbc139d242_1440w.jpg)

在`master`分支里是看不到`/public`目录的，也看不到任何发布后的文件，但是切到`gh-pages`分支，就会看到发布后的文件——

![img](https://pic1.zhimg.com/80/v2-f455cdcc0c9a97bb74bf4dc22a405c64_1440w.jpg)

接下来还需要到Setting选项里设置Github Pages设置发布源为`gh-pages`。

![img](https://pic3.zhimg.com/80/v2-807ea8e235bde861715afacdc78e827e_1440w.jpg)

等一小会儿，再次访问 `https://username.github.io`

![img](https://pic2.zhimg.com/80/v2-cc39de0c9298ae589a27d1763d9bffc5_1440w.jpg)

Yeah～

等等，为什么没有任何post？

因为我们的第一篇`post`的`draft`属性是`true`，而发布的配置里并没有带上`-D`这样的参数去执行。

因为`github`上已经发生了变化，所以先用`git pull`命令把远端的库拉下来。

```text
$ git pull
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 23 (delta 6), reused 18 (delta 5), pack-reused 0
Unpacking objects: 100% (23/23), done.
From github.com:nightan42643/nightan42643.github.io
   f21abd4..e85fb85  master     -> origin/master
 * [new branch]      gh-pages   -> origin/gh-pages
Updating f21abd4..e85fb85
Fast-forward
 .github/workflows/gh-pages.yml | 30 ++++++++++++++++++++++++++++++
 1 file changed, 30 insertions(+)
 create mode 100644 .github/workflows/gh-pages.yml
studyhugo $ 
```

再查看所有的分支，远端现在多了一个分支`gh-pages`。

```text
$ git branch -a
* master
  remotes/origin/gh-pages
  remotes/origin/master
```

不切换分支，因为`gh-pages`是由Action去发布操作的，我们不去动。我们修改本地的文件`my-first-post.md`的属性然后重新`push`一次。

```text
$ cat content/posts/my-first-post.md 
---
title: "My First Post"
date: 2021-02-16T21:53:17+08:00
draft: false
---
# My First Post
Testing.....
```

Commit并查看log

```text
studyhugo $ git add content/posts/my-first-post.md 
studyhugo $ git commit -m "Published my-first-post.md"
[master 52450d9] Published my-first-post.md
 1 file changed, 3 insertions(+), 2 deletions(-)
studyhugo $ git log
commit 52450d9576e468addb15f5fa719fbbcae590662a (HEAD -> master)
Author: nightan <nightan-pub@outlook.com>
Date:   Tue Feb 16 23:11:42 2021 +0800

    Published my-first-post.md

commit e85fb8539e47784ec9bef21c4f3085e67ddf712f (origin/master)
Author: nightan <38649296+nightan42643@users.noreply.github.com>
Date:   Tue Feb 16 22:33:30 2021 +0800

    Create gh-pages.yml

commit f21abd4cabb6479c10c64f5a96f78c8febbb1809
Author: nightan <nightan-pub@outlook.com>
```

然后`push`上去。

在Actions里能看到一个新的workflow，这个workflow结束后，再次访问页面就可以看到发布后的blog了。

![img](https://pic3.zhimg.com/80/v2-bb73b3c8949688a8af99d44d929cd8aa_1440w.jpg)

![img](https://pic3.zhimg.com/80/v2-f316bf8aa1093dde98eca88b5fab1bbe_1440w.jpg)

但是当我访问第一篇文档的时候，遇到了问题

![img](https://pic4.zhimg.com/80/v2-0da2996b64d3680bb1464e22a4b423a7_1440w.jpg)

可以很容易地看出，URL错了。

需要修改`config.toml`里的`baseURL`的参数，确保改成`username.github.io`

```yaml
$ cat config.toml 
baseURL = "http://nightan42643.github.io"
```

再`push`一次。

![img](https://pic4.zhimg.com/80/v2-63b62296cbc50fb0fba62c5a5c9b7dab_1440w.jpg)

![img](https://pic2.zhimg.com/80/v2-960d09bc4d41729c91b8f14da4968019_1440w.jpg)

现在可以正常访问了。