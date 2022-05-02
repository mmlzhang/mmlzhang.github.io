---
title: 08-tox集成使用指南
keywords:
- 08-tox集成使用指南
- mlzhang
description : "08-tox集成使用指南"
---
以keystone工程为例，其他工程类似

内容包括：打源码包（sdist）、单元测试（UT）、测试覆盖率（coverage）、代码格式检查（pep8，flake）

`pip install tox`  可以将tox安装在外部全局环境中，方便每次使用tox命令，而不用激活虚拟环境，tox会在当前工程的文件目录下创建`.tox`文件目录来下载需要的虚拟环境和存放生成的文件

#### 1、引子

接触了一段时间openstack社区，并提交了几个bug之后，就发现社区中，从bug提交、问题确认、到bug修复，代码review，自动构建、单元测试、静态检查、再到代码合入，也就是我们经常说的持续集成（CI），是一个非常简单和高效的过程。

开发人员都是懒人，这点我从来都没有怀疑过，怎么让一群懒人（还是一大群）将CI的这么多步骤做规范，并且不觉得是一个麻烦的过程，需要很高的技巧和聪明才智。

我认为持续集成（CI）有两个重点需要把握，首先要好上手，简单易学，开发都是懒人，不好用的东西，很难养成习惯使用；其次做且仅做应该做的事，也就是CI检查的范围要确定，保持CI的高速，写完代码10分钟之内，要出ut和coverage的结果。

来让我们看看openstack社区的持续集成都包括哪些内容，使用了哪些工具。

#### 2、tox具体使用方法

对openstack几个核心工程代码比较熟悉的朋友，可能都会注意到代码根目录下都有个tox.ini文件，tox其实就是openstack持续集成中非常重要的一个工具，tox.ini就是tox的配置文件。

tox的官方对于tox的定义是这样的：

Tox as is a generic virtualenv management and test command line tool

http://tox.readthedocs.org/en/latest/

也就是一个通用的虚拟环境管理和测试命令行工具。

所谓的虚拟环境，就是可以在一个主机上，自定义出多套的python环境，多套环境中使用不同的python拦截器，环境变量设置，第三方依赖包，执行不同的测试命令，最重要的是各个环境之间互不影响，相互隔离。

最典型的应用就测试在不同python版本下代码的兼容性，我们可以为py2.4，py2.5，py2.6，py2.7创建不同的虚拟环境，都可以用tox统一管理；也可以在tox.ini中自定义虚拟环境，例如：testevn:pep8，代码格式检查；testenv:cover，测试覆盖率。

我们以最新的H版的keystone的tox.ini为例：

首先定义tox的全局配置，列出了需要执行的虚拟环境列表，在命令行中直接执行tox，就会依次执行py26，py27，pep8

```python
[tox] 
envlist = py26,py27,pep8 
```

然后定义了虚拟环境的配置

setenv列出了虚拟机环境中生效的环境变量，一些配色方案和单元测试标志；

deps列出了虚拟环境需要的第三方依赖包，也就是keystone根目录下的requirements.txt和test-requirements.txt其中包括了keystone运行和单元测试时，需要用到的依赖包，每个虚拟环境创建的时候，会通过pip install -r requirements.txt和pip install -r test-requirements.txt安装依赖包到虚拟环境；

commands就是在当前虚拟环境中需要执行的命令，python tools/patch_tox_venv.py就是安装了redhat-eventlet.patch补丁；nosetests {posargs}就是执行nose进行单元测试，{posargs}参数就是可以将tox的参数传递给nosetests，例如：tox -- --with-coverage执行的时候就是nosetests --with-coverage



```python
[testenv] 
setenv = VIRTUAL_ENV={envdir} 
         NOSE_WITH_OPENSTACK=1 
         NOSE_OPENSTACK_COLOR=1 
         NOSE_OPENSTACK_RED=0.05 
         NOSE_OPENSTACK_YELLOW=0.025 
         NOSE_OPENSTACK_SHOW_ELAPSED=1 
         NOSE_OPENSTACK_STDOUT=1 
deps = -r{toxinidir}/requirements.txt 
       -r{toxinidir}/test-requirements.txt 
commands = python tools/patch_tox_venv.py 
           nosetests {posargs}
```

自定义了一个pep8的代码静态检查的虚拟环境，执行flake8 --filename=keystone* bin

```python
[testenv:pep8] 
commands = 
  flake8 
  flake8 --filename=keystone* bin 
<span style="font-family: Arial, Helvetica, sans-serif; background-color: rgb(255, 255, 255);">定义了和CI server jenkins的集成配置，指定了pip的下载cache目录，提高构建虚拟环境的速度</span>
[tox:jenkins] 
downloadcache = ~/cache/pip
```

定义一个cover的虚拟环境，就是指定了一些环境变量，使单元测试的时候，自动应用coverage，并定义了coverage生成的html报告目录

```python
[testenv:cover] 
setenv = VIRTUAL_ENV={envdir} 
         NOSE_WITH_COVERAGE=1 
         NOSE_COVER_HTML=1 
         NOSE_COVER_HTML_DIR={toxinidir}/cover
```

这个不太明白，也许就是创建一个虚拟机环境，执行一个自定义的命令行，以备扩展

```python
[testenv:venv] 
commands = {posargs}
```

定义了flake8静态检查的一些细节配置

```python
[flake8] 
show-source = true 
# H304: no relative imports. 
ignore = H304 
builtins = _ 
exclude=.venv,.git,.tox,dist,doc,*openstack/common*,*lib/python*,*egg,tools,vendor,.update-venv
```

#### 3、使用过程中的一些改进

直接使用keystone自带的tox.ini执行单元测试和静态检查时，也遇到了一些问题：

每次执行tox命令的时候，所有的虚拟环境都会重建，重新用pip下载依赖包，时间都浪费在了下包上，recreate=False也不能解决，后来想了个招儿，先手动用pip将requirements.txt和test-requirements.txt都安装在系统python库下，然后将sitepackages=True，继承系统的依赖包。这样似乎打破了虚拟环境相互隔离的好处，但是能节省非常多的时间，大概70%。大家自己权衡是否需要使用这种方法。

执行单元测试的时候，顺便生成单元测试报告，并检查测试覆盖率，并生成覆盖率报告。直接执行tox是不行的，只能进行单元测试，需要给tox增加扩展参数，如下：tox -- --cover-erase -- --with-coverage -- --cover-html

一开始执行tox的时候，生成的coverage覆盖率报告都是0%，百思不得其解，后来发现keystone根目录下有个.coveragerc文件，这个文件是coverage的配置文件，会影响coverage的行为，将文件中的source = keystone注释掉之后，正常生成覆盖率报告。