---
title: 08-tox使用
date: 2022-04-30 16:55:47
tags: my-notes
---
tox

tox是通用的虚拟环境管理和测试命令行工具，tox能够让我们在同一个Host上自定义出多套相互独立且隔离的python环境

- 检查软件包能否在不同的python版本或解释器下正常安装
- 在不同的环境中运行测试代码
- 作为持续集成服务的前端，大大减少测试工作所需的时间

2、openstack社区tox使用：

比如openstack社区的openstack-infra/project-config工程，其gerrit配置的门禁，其门禁具体执行中使用了tox执行基本语法检测。

S1、clone该工程：

git clone https://github.com/openstack-infra/project-config.git

S2、查看project-config的工程门禁配置（project-config/zuul/layout.yaml截取一部分）：

```python

- name: openstack-infra/project-config            
  template:                                       
  - name: bindep-fallback                       
  - name: merge-check                           
    check:                                          
  - gate-project-config-gerrit                  
  - gate-project-config-grafyaml                
  - gate-project-config-layout	# check阶段的门禁                  
  - gate-project-config-linters-ubuntu-xenial   
  - gate-project-config-irc-access              
  - gate-project-config-jenkins-project         
  - gate-project-config-nodepool                
  - gate-infra-docs-index                       
  - gate-generate-specs-site                    
  - gate-project-config-dib  
```


S3、查看gate-project-config-layout的工程配置：

（位于project-config/jenkins/jobs/infra.yaml中）

```python
job:                                    
name: gate-project-config-layout      
node: ubuntu-trusty                   
                                      
builders:                             

- net-info # 显示环境信息，如构建时间、ip、网络状况等                         
- zuul-git-prep   	# zuul-clone工程                  
- install-distro-packages   # 安装相应依赖        
- revoke-sudo   	# 取消sudo权限                    
- run-tox:      	# 调用tox的相关脚本              
  envlist: 'zuul'                 
                                  

publishers:                           

- test-results                      
- console-log       
```




S4、重点是查看builder：run-tox的配置，位于project-config/jenkins/jobs/macros.yaml：

```python


- builder:                                                             
  name: run-tox                                                      
  builders:                                                          
  - shell: "/usr/local/jenkins/slave_scripts/run-tox.sh {envlist}"
```


实际上最后执行的脚本就是：/usr/local/jenkins/slave_scripts/run-tox.sh zuul

run-tox.sh的脚本位于：project-config/jenkins/scripts/run-tox.sh

S5、run-tox.sh中脚本，最重要的就是：

tox -vv -e$venv （其中venv=$1，也就是zuul）

其中执行的脚本就是zuul -vv zuul，执行对应的测试

具体的参见openstack的gerrit：

https://review.openstack.org/#/q/status:merged+project-config



3、tox -vv zuul的具体执行任务：

当clone下来project-config的工程后，里面有tox.ini文件（截取相关的部分）

```python
[testenv:zuul]           
basepython = python2.7      
deps =                      # 安装对应的依赖    
     jenkins-job-builder==1.6.1    
     zuul              
whitelist_externals =    	# 白名单，列出的命令可在virtualenv中使用 
  bash     
  find                                                                                 
  jenkins-jobs                                                                         
  mkdir                                                                                 
  rm                                                                                   
commands =          	# 具体的执行命令的过程脚本          
  rm -rf {envdir}/tmp     
  mkdir -p {envdir}/tmp/jobs  
  pip install -U jenkins/modules/jjb_afs  
  jenkins-jobs -l debug test -o {envdir}/tmp/jobs jenkins/jobs  
  bash -c 'find {envdir}/tmp/jobs -printf "%f\n" > {envdir}/tmp/job-list.txt' 
  zuul-server -c tools/zuul.conf-sample -l zuul/layout.yaml -t {envdir}/tmp/job-list.txt   
  {toxinidir}/tools/layout-checks.py {envdir}/tmp/job-list.txt                            
```



可以看到，这个tox.ini文件中有很多配置，这里只是截取了该任务对应的配置。、

{envdir}：tox.ini文件目录



4、查看tox的详细配置：

tox的https://tox.readthedocs.io/en/latest/examples.html

常见配置如下：

（1）tox -i http://pypi.my-alternative-index.org：更换pypi依赖的下载地址

（或者在tox.ini中指定indexserver地址）

（2）通常tox只能传递系统变量PATH，如果需要传递其他变量，需要赋值指定；

（3）指定基础环境，有几种方式：

使用tox -e ENV1[,ENV2...]

（4）使用distshare变量实现多个tox工程的文件共享

（5）使用basepython指定构建virtualenv的编译器

（6）usedevelop：使用开发模式安装

（7）skipsdist：不在virtualenv中安装本次软件，使用时需谨慎



5、tox集成pytest、unitest、nose等：

比如一个简单的例子如下，在工程的tox.ini中写入：

```python

[tox]
envlist = py26,py31
[testenv]
deps=pytest       # PYPI package providing pytestcommands=
  pytest \
​        {posargs} # substitute with tox' positional arguments
```


在tox.ini目录中执行tox命令的时候，会构建py26、py31两个环境，并且安装pytest，并执行对应的pytest测试



6、使用tox类：

可以在python脚本中使用tox的类调用对应的tox方法。

```python

import tox
os.chdir(os.getenv('WORKSPACE'))
tox.cmdline()	# 执行对应的tox命令，可根据情况入参，具体参见tox类的函数
```




7、构建一个开发的virtual环境：

我们可以使用tox构建一个简单的virtualenv环境辅助我们进行开发

详情参见：https://tox.readthedocs.io/en/latest/example/devenv.html 

然后用virtualenv + 路径切入进行测试开发