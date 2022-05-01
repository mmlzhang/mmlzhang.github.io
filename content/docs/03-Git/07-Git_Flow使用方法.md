---
title: 07-Git_Flow使用方法
date: 2022-04-30 16:55:47
tags: my-notes
---
- http://www.berlinix.com/it/gitflow.php



  http://www.berlinix.com/it/gitflow.php





  http://www.berlinix.com/it/git.php

  http://www.berlinix.com/it/git.php





  # gitflow分支管理模型



  gitflow的分支类型：

  - master分支（1个）
  - develop分支（1个）
  - feature分支。同时存在多个。
  - release分支。同一时间只有1个，生命周期很短，只是为了发布。
  - hotfix分支。同一时间只有1个。生命周期较短，用了修复bug或小粒度修改发布。

  在这个模型中，master和develop都具有象征意义。master分支上的代码总是稳定的（stable build），随时可以发布出去。develop上的代码总是从feature上合并过来的，可以进行Nightly Builds，但不直接在develop上进行开发。当develop上的feature足够多以至于可以进行新版本的发布时，可以创建release分支。

  release分支基于develop，进行很简单的修改后就被合并到master，并打上tag，表示可以发布了。紧接着release将被合并到develop；此时develop可能往前跑了一段，出现合并冲突，需要手工解决冲突后再次合并。这步完成后就删除release分支。

  当从已发布版本中发现bug要修复时，就应用到hotfix分支了。hotfix基于master分支，完成bug修复或紧急修改后，要merge回master，打上一个新的tag，并merge回develop，删除hotfix分支。

  由此可见release和hotfix的生命周期都较短，master/develop虽然总是存在但却不常使用。

  以上就是gitflow的基本概念了。下面是nvie（gitflow的提出者，一个荷兰人！） [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)（发布于2010年月5日）一文的笔记。







  从右看起：

  - 时间轴。
  - feature（玫红）。主要是自己玩了，差不多的时候要合并回develop去。从不与master交互。
  - develop（黄色）。主要是和feature以及release交互。
  - release（绿色）。总是基于develop，最后又合并回develop。当然对应的tag跑到master这边去了。
  - hotfix（红色）。总是基于master，并最后合并到master和develop。
  - master（蓝色）。没有什么东西，仅是一些关联的tag，因从不在master上开发。

  接下来nvie说道自己喜爱git，因git改变了人们对合并/分支（merge/branches）的看法。从集中式的代码管理工具过来的人感到释放了（beware of merge conflicts, they bite you，注意合并冲突，它们会跳出来咬你！）。

  # gitflow实例

  安装gitflow：

  ```
  $ git clone --recursive git://github.com/nvie/gitflow.git
  $ cd gitflow/
  $ sudo make install
  $ ls /usr/local/bin/git-flow
  /usr/local/bin/git-flow
  ```

  到项目根目录下执行gitflow，因为之前修改没有commit，所以gitflow初始化失败：

  ```
  $ git flow init
  fatal: Working tree contains unstaged changes. Aborting.
  ```

  commit后再次进行gitflow初始化：

  ```
  $ git commit -a -m "update Bash"
  [master 8f5b874] update Bash
   4 files changed, 71 insertions(+), 5 deletions(-)
  
  [bailing@zhuji zhuji]$ git flow init
  
  Which branch should be used for bringing forth production releases?
     - master
  Branch name for production releases: [master] 
  Branch name for "next release" development: [develop] 
  
  How to name your supporting branch prefixes?
  Feature branches? [feature/] 
  Release branches? [release/] 
  Hotfix branches? [hotfix/] 
  Support branches? [support/] 
  Version tag prefix? [] 
  ```

  一路回车下来，各个分支名都按默认的设置。最后，当前分支已经被切换到了develop：

  ```
  $ git branch
  * develop
    master
  ```

  建立一个新的feature。git flow新建了功能分支feature/blog_builder，并在develop的基础上checkout了新分支：

  ```
  $ git flow feature start blog_builder
  $ git branch
    develop
  * feature/blog_builder
    master
  ```

  开发完成后执行如下命令：

  ```
  $ git flow feature finish blog_builder
  Summary of actions:
  - The feature branch 'feature/blog_builder' was merged into 'develop'
  - Feature branch 'feature/blog_builder' has been removed
  - You are now on branch 'develop'
  ```

  正如这条命令的总结所言，git flow为我们做了3件事：

  - 把feature/blog_builder合并到了develop。
  - 删除了feature/blog_builder分支。
  - 切换回develop分支。

  接下来发布一个正常的版本：

  ```
  $ git flow release start v0.5
  ```

  一旦需要发布的版本确认无误可以发布后，执行命令：

  ```
  $ git flow release finish v0.5
  summary of actions:
  - Latest objects have been fetched from 'origin'
  - Release branch has been merged into 'master'
  - The release was tagged 'v0.5'
  - Release branch has been back-merged into 'develop'
  - Release branch 'release/v0.5' has been deleted
  ```

  注意release/v0.5被合并到了master和develop分支，并打了个v0.5的tag，然后被删除，最后切换回了develop分支：

  ```
  $ git branch
  * develop
    master
  ```

  发布时只需将tag为v0.5的版本checkout出来部署即可：

  ```
  $ git tag
  v0.5
  ```

  当上线后发现v0.5的bug，可以进行hotfix：

  ```
  $ git flow hotfix start v0.5.1
  ```

  此时gitflow从master分支上拉出一个hotfix/v0.5.1的分支，接下来在新分支上修改bug。最后执行命令：

  ```
  $ git flow hotfix finish v0.5.1
  ```

  这样hotfix/v0.5.1被merge到master/develop分支，打好v0.5.1这个tag，删除这个分支，切换回develop分支。

  之后又是新一次的轮回，启动正常的feature开发。















  \-----------------------------









  Git（the stupid content tracker）是一个源自[Linux](http://www.berlinix.com/linux/Linux.php)内核项目的源码管理工具。和传统的CVS、[SVN](http://www.berlinix.com/it/SVN.php)不同，git是一个分布式源码管理工具。

  | Git命令      | 简单说明                               |
  | ------------ | -------------------------------------- |
  | git init     | 初始化一个本地的代码仓库。             |
  | git clone    | 从远程复制一个代码仓库。               |
  | git config   | git选项设置。                          |
  | git add      | 添加文件/目录。                        |
  | git commit   | 提交修改。                             |
  | git status   | 显示工作目录的状态以及缓冲区内的快照。 |
  | git log      | 已提交快照的日志。                     |
  | git branch   | 创建分支。                             |
  | git checkout | 迁出/拉出/切换到一个分支。             |
  | git merge    | 合并分支。                             |
  | git revert   | 撤销commit快照。                       |
  | git reset    | 撤销本地工作目录的修改。               |
  | git clean    | 删除代码仓库以外的文件。               |
  | git remote   | 管理远程git。                          |
  | git fetch    | 从远程获取分支。                       |
  | git pull     | 从远程获取分支。                       |
  | git push     | 把代码推到远程分支。                   |

  ## 基本概念

  ### 文件状态

  Git仓库中的文件有几种状态：

  - untracked - 还没添加到仓库中。
  - unmodified - 自上次提交以来，文件未曾修改过。
  - modified - 文件修改了还没提交。
  - staged - 文件提交到了暂存区中。一旦执行git commit就会转换为unmodified状态。







  Git暂存区（Staged Area）的意思是：你把一个文件托付给Git跟踪（git add），然后又修改了它，此时这个文件就位于暂存区了。暂存区内的文件几乎只做一件事：等待你执行git commit，把它提交。

  ### 快照（snapshot）

  Git与其他版本控制系统的区别在于：Git只关心文件是否变化，而不关心文件内容的变化。大多数版本控制系统都会忠实地记录版本间的文件差异（diff），但Git不关心这些具体差异（哪一行有什么变动），Git只关心哪些文件修改了哪些没有修改，修改了的文件直接复制形成新的blob（这就是所谓的快照snapshot）。当你需要切换到或拉出一个分支时，Git就直接加载当时的文件快照即可，这就是Git快的原因。说起来，这也是用空间换取时间的经典案例。

  从这个角度看，Git更像是一个小型文件系统，并在这个系统上提供一系列的工具来辅助开发。

  ### Git的地理观

  Git是一个分布式的版本控制系统，因此没有所谓的中心。粗略来看Git可分为本地库（local repository）和远程库（remote repository），细致地看可分为以下几个部分：

  - Working Directory - 工作目录。Git仓库位于工作目录之下，工作目录下的文件有加入Git仓库（tracked）和没加入Git仓库（untracked）的区别。
  - Stage Area - 暂存区。如上所述，已加入Git仓库并被修改（尚未提交）的文件。
  - Local Repository - 本地仓库。
  - Remote Repository - 远程仓库。

  文件通常是：加入Git仓库（git add）-> 修改后即位于暂存区 -> 提交到本地库（git commit） -> 推送到远程库（git push）。

  ### origin/master

  这里主要笔记一些在Git上下文中经常遇见的术语。origin/master指远程仓库origin的master分支。

  ```
  远程仓库/分支
  ```

  这样的形式。虽然Git是分布式的系统，但通常把git clone的源头叫做origin，origin也被视为中心仓库（Central Repository）。

  # git入门

  创建目录，并用`git init`初始化：

  ```
  $ mkdir learn-git && cd learn-git
  $ git init
  Initialized empty Git repository in /tmp/learn-git/.git/
  ```

  从`git init`输出可知，`git`创建了一个名为`.git`的隐藏目录。

  创建一个文件，并用`git add`添加到仓库，用`git commit`提交：

  ```
  $ echo "hello git" > README.txt
  $ git add .
  $ git commit -m "readme file"
  [master (root-commit) cd27ac1] readme file
   1 file changed, 1 insertion(+)
   create mode 100644 README.txt
  ```

  接下来对已提交文件做一些修改，并新添加一个文件：

  ```
  $ echo "learn files here" >> README.txt
  $ cp ~/.vimrc .
  ```

  用`git diff`查看[文件差异](http://www.berlinix.com/it/diff.php)（每次commit前应该先diff对比差异详情）：

  ```
  $ git diff
  diff --git a/README.txt b/README.txt
  index 8d0e412..0219596 100644
  --- a/README.txt
  +++ b/README.txt
  @@ -1 +1,2 @@
   hello git
  +learn files here
  ```

  差异对比可以用`git diff --cached`保存下来（如此差异则不输出到屏幕）。

  用`git status`查看git仓库状态：

  ```
  $ git status
  # On branch master
  # Changes not staged for commit:
  #   (use "git add <file>..." to update what will be committed)
  #   (use "git checkout -- <file>..." to discard changes in working directory)
  #
  #       modified:   README.txt
  #
  # Untracked files:
  #   (use "git add <file>..." to include in what will be committed)
  #
  #       .vimrc
  no changes added to commit (use "git add" and/or "git commit -a")
  ```

  这里显示`README.txt`被修改了，而`.vimrc`则等待添加。接下来，我们将`.vimrc`添加到git仓库中，且将所有修改一并提交（`git commit -a`）：

  ```
  $ git commit -a -m "update readme && add vimrc"
  [master f6162f0] update readme && add vimrc
   2 files changed, 123 insertions(+)
   create mode 100755 .vimrc
  ```

  `git log`输出git日志，包括提交编号（如"f6162f04170e3665bc03744e43f764c903e4e38d"这样的字串）、提交者、提交日期和提交日志。

  `git log`的其他输出：

  ```
  $ git log -p                    # 详细日志，并输出到分页程序
  $ git log --stat --summary
  ```

  美化git log输出

  ```
  $ git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --
  ```

  修改全局配置：

  ```
  $ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --"
  $ git lg
  ```

  ## 管理分支

  创建一个分支：

  ```
  $ git branch exp
  ```

  查看当前git仓库的所有分支：

  ```
  $ git branch
    exp
  * master
  ```

  切换到`exp`分支：

  ```
  $ git checkout exp
  Switched to branch 'exp'
  ```

  修改文件，并提交：

  ```
  $ echo "start branch: exp" >> README.txt 
  $ git commit -a -m "modified readme in exp branch"
  [exp 2e825a4] modified readme in exp branch
   1 file changed, 1 insertion(+)
  ```

  切换回`master`分支：

  ```
  $ git checkout master
  Switched to branch 'master'
  ```

  在`master`分支检查文件，可见`exp`分支的修改并没影响到`master`分支（注意，在exp分支的修改都已提交；如果没有提交，则切换回master分支会看到文件已变）。接下来我们制造一个冲突：

  ```
  $ echo "return branch: master" >> README.txt                    
  $ git commit -a -m "modified readme in master branch"
  [master 8dd9fb2] modified readme in master branch
   1 file changed, 1 insertion(+)
  ```

  用`git merge exp`合并分支：

  ```
  $ git merge exp
  Auto-merging README.txt
  CONFLICT (content): Merge conflict in README.txt
  Automatic merge failed; fix conflicts and then commit the result.
  ```

  从git输出可见，git尝试自动合并但失败了，因此提示需要解决冲突再提交

  用`git diff`查看差异，且差异文件被修改：

  ```
  $ git diff
  ...
  $ cat README.txt 
  hello git
  learn files here
  <<<<<<< HEAD
  return branch: master
  =======
  start branch: exp
  >>>>>>> exp
  ```

  手工解决冲突并再次提交：

  ```
  (edit file)
  $ git commit -a -m "do merge"
  ```

  接下来，可以删除`exp`分支：

  ```
  $ git branch -d exp
  Deleted branch exp (was 2e825a4).
  ```

  `git branch -d`删除分支时会检查分支是否完全合并到主干，如果不是，则会删除失败，并提示需要合并：

  ```
  $ git branch exp                        # 建立exp分支
  $ git checkout exp                      # 切换到exp分支
  $ echo "exp again" >> README.txt        # 修改并提交
  $ git commit -a -m "exp again"
  [exp 868e68c] exp again
   1 file changed, 1 insertion(+)
  $ git checkout master                   # 切换回master
  Switched to branch 'master'
  $ git branch -d exp                     # 删除失败
  error: The branch 'exp' is not fully merged.
  If you are sure you want to delete it, run 'git branch -D exp'.
  ```

  可以用`git branch -D exp`忽略修改，完全删除分支：

  ```
  $ git branch -D exp
  Deleted branch exp (was 868e68c).
  ```

  # 查看远端git

  基础命令是：git remote show, git remote show X。

  ```
  $ git remote show
  origin
  web
  ```

  查看GitHub默认设置的origin

  ```
  $ git remote show origin
  * remote origin
    Fetch URL: git@github.com:berlinix/blog.git
    Push  URL: git@github.com:berlinix/blog.git
    HEAD branch: master
    Remote branch:
      master tracked
    Local branch configured for 'git pull':
      master merges with remote master
    Local ref configured for 'git push':
      master pushes to master (fast-forwardable)
  ```

  # git命令快查

  以下列出一些常用的git命令

  | 命令                                                       | 说明                                   |
  | ---------------------------------------------------------- | -------------------------------------- |
  | [基础操作](http://www.berlinix.com/it/git.php#basic)       |                                        |
  | git init                                                   | 初始化git仓库                          |
  | git add X                                                  | 添加X文件/路径到git仓库                |
  | git commit -m "COMMENTS"                                   | 提交更新                               |
  | [分支管理](http://www.berlinix.com/it/git.php#branch)      |                                        |
  | git branch X                                               | 创建一个名为X的分支                    |
  | git checkout X                                             | 切换到X分支                            |
  | git merge X                                                | 自动合并X分支                          |
  | git branch -d X                                            | 删除X分支，需要先merge                 |
  | git branch -D X                                            | 强制删除X分支，忽略其修改，无须先merge |
  | [与远程git交互](http://www.berlinix.com/it/git.php#remote) |                                        |
  | git remote show                                            | 显示远程git仓库                        |
  | git remote show X                                          | 显示远程git一个名为X的仓库             |
  | git push origin master                                     | 更新提交到GitHub                       |

  # Git日常问题

  ## 撤销commit

  刚与master合并并提交后就后悔了现在要做的是撤销commit（revoke/undo merge/commit）。

  查看当前所在分支：

  ```
  $ git branch
    bs3
  * coin
    dev
    master
  ```

  查看日志：

  ```
  $ git log --oneline
  9b7ba39 merged with master
  73a66e8 update FAQ
  ```

  用以下2个命令来撤销提交（把COMMIT_SHA替换为实际的SHA值；把HEAD~N中的N替换为一个数字，表示回退几步）：

  ```
  $ git reset --hard COMMIT_SHA
  $ git reset --hard HEAD~N
  ```

  例如回退到合并前：

  ```
  $ git reset --hard 73a66e8
  HEAD is now at 73a66e8 update FAQ
  ```

  回退后发现不对，因为现在这个commit还是在master中的（在merge之前master已经走的太远），赶紧再次reset到merge时的状态：

  ```
  $ git reset --hard 9b7ba39
  HEAD is now at 9b7ba39 merged with master
  ```

  如此一来就是就是merge后commit之前的状态。接下来就是要完成undo merge（已经undo commit了）：

  ```
  $ git revert -m 1 9b7ba39
  ```

  这下就彻底回到merge前了，以防万一再次检查：

  ```
  $ git diff --name-status master..coin
  ```

  看起来没什么问题了，检查下日志：

  ```
  $ git log --oneline
  2691516 Revert "merged with master"
  9b7ba39 merged with master
  73a66e8 update FAQ
  ```

  ## 用git找回已删除文件

  首先找到与目标文件相关的最后一次commit。如果目标文件没有出现在HEAD commit中，那么在这次commit时，文件就被删除了：

  ```
  $ git rev-list -n 1 HEAD -- htdocs/myfile.php
  1e8182f58dc038c8e6bc2025e8430f463d372030
  ```

  接下来就是恢复工作了：

  ```
  $ git checkout 1e8182f58dc038c8e6bc2025e8430f463d372030^ -- htdocs/myfile.php
  ```

  ## 合并分支的部分文件

  有时候只想合并分支里的部分文件，而不是整个分支，可以用这个命令：

  ```
  git checkout BRANCH FILE ...
  ```

  例如，从test_branch分支中合并file_modified文件：

  ```
  $ git checkout test_branch file_modified
  ```

  参考[Git Tip: How to "Merge" Specific Files from Another Branch](http://jasonrudolph.com/blog/2009/02/25/git-tip-how-to-merge-specific-files-from-another-branch/)。