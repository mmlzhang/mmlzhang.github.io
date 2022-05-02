---
title: 03-jenkins配置指南02
keywords:
- 03-jenkins配置指南02
- mlzhang
description : "03-jenkins配置指南02"
---
操作环境：Windows

一、环境准备

1 安装JDK

  本文采用jdk-8u111-windows-x64.exe；

2 配置tomcat

  本文采用tomcat8，无需安装，配置JAVA_HOME及JRE_HOME环境变量即可；

3 安装maven

  本文采用maven3.3.9，无需安装；

4 安装Jenkins

  下载地址https://jenkins.io/download/，仅下载war包，如下图：

![img](/assets/20170822111227642)



将下载好的jenkins.war放进tomcat/webapps目录下。

 

二、相关配置

1 登入http://localhost:8080/jenkins，进入Jenkins初始化页面，第一次启动时间可能有点长，耐心等待。进入成功后会看到如下画面，按提示路径打开密码文件，输入密码：

![img](/assets/20170822111233744)



解锁后又是一长段时间等待，此后可能出现如下图所示界面：

![img](/assets/20170822111238448)



表示无法下载Jenkins插件，可能是因为防火墙导致，而Jenkins插件的安装非常重要，建议翻墙。如无法翻墙，则选择Skip Plugin Installations跳过插件安装。进入以下页面，设置登陆用户：

![img](/assets/20170822111243152)

 

2 设置成功后即进入Jenkins主界面：

![img](/assets/20170822111250116)



点击左上侧系统管理，进入Jenkins基本系统设置(主要是以下三块):

![img](/assets/20170822111254569)



3 先进入“管理插件”模块安装必需的插件，以下是建议安装列表：



![img](/assets/20170822111309988)

 ![img](/assets/20170822111322935)







将本文附件中的插件放入Jenkins插件存放目录即可，如本文插件存放目录为：C:\Users\Administrator\.jenkins\plugins（可点击系统管理–>系统设置,在最上方查看，如下图）；

![img](/assets/20170822111328292)

 

4 配置系统设置

 ![img](/assets/20170822111332935)

添加编码全局属性：

![img](/assets/20170822111336995)



增加系统管理员邮件地址：

![img](/assets/20170822111341508)



其他的可用默认配置，保存后退出。

5 添加全局配置[Global ToolConfiguration](http://localhost:8080/jenkins/configureTools)

![img](https://img-blog.csdn.net/20170822111332935)



配置JDK，不采用自动安装：



![img](/assets/20170822111346314)

配置maven，不采用自动安装：

![img](/assets/20170822111352718)



以上即为需要设置的系统配置。

 

三、系统部署

系统设置完成后开始添加任务，任务类型选择自由风格：

![img](/assets/20170822111357615)



创建完成后可在主页看到如下画面：

![img](/assets/20170822111402977)



在”All” tab下能看到新建的任务，点击该任务，进入该任务的配置页面：

![img](/assets/20170822111410237)



设置项目备注及构建规则：

![img](/assets/20170822111415251)



配置项目轮询的源码位置(@HEAD表示构建最新的代码)并配置代码访问密码：

![img](/assets/20170822111420787)



配置构建触发器，如下图配置为每天晚上9：30开始构建（Cron表达式）：

![img](/assets/20170822111425533)



增加Invoke top-level Maven targets构建步骤，插件目标为编译、发现编译Bug、部署，另外还可以配置构建时忽略测试用例：

![img](/assets/20170822111430524)



增加构建后操作步骤：Publish FindBugs analysis results，用于查看FindBugs插件的代码分析报告，该模块可采用默认配置：

![img](/assets/20170822111435126)



增加构建后操作步骤：Deploy war/ear to a container，用于将构建后生成的war包部署至tomcat服务器，下图中Contextpath用于配置项目访问路径，如填/RMS_Server则表示项目的根访问目录为：http://localhost:8080/RMS_Server，Deploy on failure用于配置当前构建失败时是否仍然部署至tomcat，默认不选：

![img](/assets/20170822111439496)



以上即为本项目的所有配置，完成后应用（或保存）并退出。

配置完成后即可开始构建，左侧可查看bugs分析信息及构建历史：

![img](/assets/20170822111443945)



点击某个构建记录，如上图中的#31，即可查看构建日志、SVN代码提交日志及bugs分析结果：



 ![img](/assets/20170822111448299)

 

 

四、编码问题

FindBugs分析报告中查看某些代码文件时可能出现中文乱码情况，如下图：

![img](/assets/20170822111452905)

![img](/assets/20170822111457231)





这是tomcat的编码问题导致的，可在系统管理中查看tomcat的相关编码情况：

![img](/assets/20170822111502231)

![img](/assets/20170822111506378)





主要关注的是file.encoding属性及sun.jnu.encoding属性，二者需要设置为UTF-8以兼容中文：



![img](/assets/20170822111516153)

 

这可通过在tomcat配置文件/bin/catalina.bat文件中添加set “JAVA_OPTS=-Dfile.encoding=UTF-8-Dsun.jnu.encoding=UTF-8”命令实现，如下图：

![img](/assets/20170822111520877)

![img](/assets/20170822111525536)





配置完成后重启tomcat，可看到编码已经更改：

![img](/assets/20170822111536631)