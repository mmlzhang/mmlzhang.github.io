---
title: 02-jenkins配置指南01
date: 2022-04-30 16:55:47
tags: my-notes
---
## 一、Windows环境中安装Jenkins

在最简单的情况下，Jenkins 只需要两个步骤：

1、下载最新的版本（一个 WAR 文件）。Jenkins官方网址: [http://Jenkins-ci.org/](http://jenkins-ci.org/)

2、命运行运行 java -jar jenkins.war　（默认情况下端口是8080，如果要使用其他端口启动，可以通过命令行”java –jar Jenkins.war --httpPort=80”的方式修改）

注意：Jenkins 需要运行 Java 5以及以上的版本。

还有一种安装方式就是将下载的war包文件部署到 servlet 容器，然后启动容器，在浏览器的URL地址栏中输入类似[http://localhost:8080/jenkins/](http://localhost:8088/hudson/)这样的地址即可。下图是安装成功后的界面（使用的是Linux+Tomcat6+Java6环境）：

 ![img](assets/201743335014772.jpg)

 

## 二、Jenkins配置

在配置前的一些话：Jenkins的配置不可能全部都说到的，大部分配置是有英文说明的，点击输入框后面的问号就可以看见了。英文不会用翻译工具，多测试几次，你就懂了。

## 2.1 系统管理

在已运行的Jenkins主页中，点击左侧的系统管理进入如下界面：

 ![img](assets/12163618-fbcb44edaba24137a7161a0d9e73ab03.png)

 

### 2.1.1 提示信息

Ps：版本不同提示的消息有可能不同

### 2.1.1.1 Utf-8编码

Your container doesn't use UTF-8 to decode URLs. If you use non-ASCII characters as a job name etc, this will cause problems. See [Containers](http://wiki.jenkins-ci.org/display/JENKINS/Containers) and [Tomcat i18n](http://wiki.jenkins-ci.org/display/JENKINS/Tomcat#Tomcat-i18n) for more details.

Jenkins建议在tomcat中使用utf-8编码，配置tomcat下conf目录的server.xml文件

 ![img](assets/12163628-5e18c80f1c44409fb584a8a618299e12.png)

Ps：如果Job的控制台中文输出乱码，请将URIEncoding=”utf-8”更改为useBodyEncodingForURI="true"

### 2.1.1.2 新的版本

New version of Jenkins (1.518.JENKINS-14362-jzlib) is available for download (changelog).

提示有新的版本可以下载了,喜欢更新的点击download去下载吧！

### 2.1.1.3 安全设置

 ![img](assets/12163634-a71e6cae19ce4f059fb19d845b7f5db7.png)

詹金斯允许网络上的任何人代表您启动进程。考虑至少启用身份验证来阻止滥用。点击Dismiss忽略该消息,点击Setup Security进入设置界面.详细设置请参考 Configure Global Security(安全设置) 章节

### 2.1.2 系统设置

在已运行的Jenkins主页中，点击左侧的系统管理—>系统设置进入如下界面：

 ![img](assets/201744405631093.jpg)     

 ps：jenkins的根目录，默认地在C:\Documents and Settings\AAA\.hudson。

 

### 2.1.2.1 JDK、Maven、Ant配置(图为Windows环境)

配置一个JDK、Ant、Maven实例，请在每一节下面单击Add(新增) 按钮，这里将添加实例的名称和绝对地址。下图描述了这两个部分。

![img](assets/201747150633693.jpg)

点击“安装”，添加相应的设置，如下图：

![img](assets/201748138603322.jpg)

JDK别名：给你看的，随便你自己

JAVA_HOME：这个是本机JDK的安装路径（错误的路径会有红字提示你的）

自动安装：不推荐这个选项

注：Ant、Maven的配置是一样的（JDK去oracle官网下载，Ant与Maven去apache官网下载）

Ps：每个文本框后面都有个问号，点击问号就会出现帮助信息

 

### 2.1.2.2 邮件通知配置（默认）

#### 2.1.2.2.1 配置发件人地址

 ![img](assets/201752167514361.jpg)

系统管理员邮件地址（System Admin e-mail address）：Jenkins邮件发送地址，切记，必须设置。

#### 2.1.2.2.2 配置邮件通知

 ![img](assets/201754532201063.jpg)

设置：SMTP服务器，勾选"使用SMTP认证"，输入用户名与密码

Ps：小技巧：用户默认邮件后缀配置了后，以后你填写邮件地址只需要@之前的就行了

 

### 2.1.2.3 Subversion配置

 ![img](assets/251338417708391.jpg)

Subversion Workspace Version：Subversion 的版本号，选择你对应的版本号就行了

 

### 2.1.3 Configure Global Security(安全设置)

在已运行的Jenkins主页中，点击左侧的系统管理—>Configure Global Security进入如下界面：

 ![img](assets/251340108648742.jpg)

 

设置如上图，保存后系统管理中就出现管理用户的选项。页面右上角也会出现登录/注册的选项。

此设置：只有登录用户可以做任何事

 

### 2.1.4 管理用户设置

在右上角点击注册

 ![img](assets/12163750-a102721d99c647939c917aded73a20e6.png)

点击sign up按钮，提示你现在已经登录.返回首页.

登录后和匿名账号看到的首页有几点不同，如下图红框所示：

 ![img](assets/201800501737514.jpg)

 

### 2.1.5 管理插件设置

建议先阅读[Jenkins插件](http://www.cnblogs.com/zz0412/p/jenkins02.html#_Jenkins%E6%8F%92%E4%BB%B6)章节，在回来安装如下所示的插件。

需求：这个插件将生成的构件（war或者ear）部署到主流的服务器上。

插件名称：[Deploy Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Deploy+Plugin)

插件介绍：This plugin takes a war/ear file and deploys that to a running remote application server at the end of a build

 

## 2.2 项目构建设置

### 2.2.1 构建自由风格的Job

#### 2.2.1.1 新建自由风格构建任务

在已运行的Jenkins主页中，点击左侧的新建Job进入如下界面：

 ![img](assets/12163818-f9eb7b95424f4c3488bdcdf4bbedd295.png)

这时，需要为新的构建任务指定一个名称。（这里输入的任务名称为：Ant_test）这里有几种的任务类型可供选择，鉴于初步介绍，先选择构建一个自由风格的软件项目。对于其他的类型,经常使用的是拷贝已存在任务;这主要为了能在现有的任务基础上新建任务。点击OK按钮.

**需要注意的是**：
1.Job名称千万不要用中文名称（不作死就不会死）。
2.创建Job名称时最好有个规划，因为我们最后会通过正则匹配自动将Job归类，比如我喜欢 “项目前缀_一些说明-Job类型”这种方式。

 

### 2.2.1.2 构建任务配置

### 2.2.1.2.1 源码管理配置

演示是使用Subversion的链接，在Repository URL中输入你的项目链接，如果没有权限则会提示如下图：

 ![img](assets/251355220209106.jpg)

设置成功后，就直接从SVN此目录获取文件到本地。Ps:要先添加Credentials。添加的方法如下操作：

点击Jenkins首页左侧Credentials，进入页面

![img](assets/251349498029943.jpg)

![img](assets/251349585527950.jpg)

下一步：一般都是使用的用户名和密码登陆的

![img](assets/251350103174946.jpg)

 

Ps：svn的用户名和密码设置了是没有办法在web界面修改的。如果要修改则先去Jenkins目录删除hudson.scm.SubversionSCM.xml文件

 

### 2.2.1.2.2 构建触发器

在其他项目构建完成后才执行构建：指定的项目完成构建后，触发此项目的构建。

Poll SCM ：这是CI 系统中常见的选项。当您选择此选项，您可以指定一个定时作业表达式来定义Jenkins每隔多久检查一下您源代码仓库的变化。如果发现变化，就执行一次构建。例如，表达式中填写0,15,30,45 * * * *将使Jenkins每隔15分钟就检查一次您源码仓库的变化。

Build periodically ：此选项仅仅通知Jenkins按指定的频率对项目进行构建，而不管SCM是否有变化。如果想在这个Job中运行一些测试用例的话，它就很有帮助。

![img](assets/251358244113027.jpg)

### 2.2.1.2.3 Ant构建配置

因为我的项目是用ant脚本实现的编译和打包，所以我选择的是Invoke Ant，Ant Version选择你Ant配置的那个名字，注意不要选择default喔，那个选择了没有用。

 ![img](assets/251359225055041.jpg)

Ps：如果你的构建脚本build.xml不在workspace根目录、或者说你的构建脚本不叫build.xml。那么需要在高级里设置Build File选项的路径，指明你的脚本。注意：是相对路径

 

## 2.2 监控

当任务一旦运行，您将会看到这个任务正在队列中的仪表板和当前工作主页上运行。这两种显示如下。

 ![img](assets/251401327081953.jpg)![img](assets/251401409899116.jpg)

一旦构建完成后，完成后的任务将会有三个地方进行显示。

你可以在Jenkins的控制面板上看到它，如下图。

 ![img](assets/251402171309356.jpg)

 

在上面展示的截图中，您将注意到有两个图标描述当前作业的状态。S栏目代表着“最新构建状态”，W栏目代表着“构建稳定性”。Jenkins使用这两个概念来介绍一个作业的总体状况：

构建状态:下图中分级符号概述了一个Job新近一次构建会产生的四种可能的状态： 

Successful:完成构建，且被认为是稳定的。

Unstable:完成构建，但被认为不稳定。

Failed:构建失败。

Disabled:构建已禁用。

 ![img](assets/12164329-a5349dc8bbce4f3d8a280d3de7eed1c4.jpg)

构建稳定性: 当一个Job中构建已完成并生成了一个未发布的目标构件，如果您准备评估此次构建的稳定性，Jenkins会基于一些后处理器任务为构建发布一个稳健指数 (从0-100 )，这些任务一般以插件的方式实现。它们可能包括单元测试(JUnit)、覆盖率(Cobertura )和静态代码分析(FindBugs)。分数越高，表明构建越稳定。下图中分级符号概述了稳定性的评分范围。任何构建作业的状态(总分100)低于80分就是不稳定的。

 ![img](assets/12164338-0b8dc036c3ae4af28a3f1f90ae11ea54.jpg)

你也可以在当前Job主界面上看到它，如下图左下部分

 ![img](assets/251403023802648.jpg)

当前作业主页上还包含了一些有趣的条目。左侧栏的链接主要控制Job的配置、删除作业、构建作业。右边部分的链接指向最新的项目报告和构件。

通过点击构建历史（Build History）中某个具体的构建链接，您就能跳转到Jenkins为这个构建实例而创建的构建主页上。如下图

 ![img](assets/251403422555263.jpg)

如果你想通过视图输出界面来监控当前任务的进展情况。你可以单击Console Output（控制台输出）。如果工作已完成，这将显示构建脚本产生的静态输出；如果作业仍然在运行中，Jenkins将不断刷新网页的内容，以便您可以看到它运行时的输出。如下图：

 ![img](assets/251405032394438.jpg)

##  

## 三、Jenkins插件

从Jenkins现有的功能扩展或开发者们为Jenkins提供的新功能都可以称之为Jenkins插件。有些插件可以无缝添加到您的构建过程，而其它，诸如除CVS和Subversion的SCM插件则需要源代码控制系统的支持。

### 3.1 Jenkins插件安装

Jenkins 插件管理器允许您安装新的插件，和更新您Jenkins服务器上的插件。管理者将连接到联机资料库，检索可用的和已更新的插件。如果您的Jenkins服务器 无法直接连接到外部资源，您可以从Jenkins网站上下载。

在已运行的Jenkins主页中，点击左侧的系统管理—>管理插件进入如下界面：

 ![img](assets/251405485678986.jpg)

它包含四个标签：

更新:清单中列示了Jenkins为某些插件搜索到了可用的更新。列出的每个插件可以被选择并应用更新。

可选安装:清单中列示了可用于安装（而不是目前已安装的）的所有插件。列出的每个插件都可以被选择并安装。

已安装:清单中列示了已经安装的插件。

高级:允许您通过设定HTTP代理的方式使Jenkins与在线插件库建立连接。此外，还提供了一个上传设备，可以安装你在Jenkins以外已下载的那些插件。

由上图可知，Jenkins缺省集成了maven2插件，并且一旦插件有新版本，会提示更新新版本插件。

如果想安装新的插件，可以点击tab分页中的可选插件。如下图：

 ![img](assets/251406367704780.jpg)

从图可知，各种Jenkins插件根据之前所记述的类型进行分门别类。可勾选任意想安装的Jenkins插件，点击Install without restart按钮进行安装。安装后，所有插件以hpi作为后缀名放置在plugins文件夹下。如果是高级用户还可以自行开发插件方便具体项目使用。

注意：安装完成后需要重启Jenkins部署的容器。这样才能使用新装的插件。

### 3.2 Jenkins插件安装示例

Jenkins运行自动部署war包到servlet容器内，要实现这个功能必须安装一个插件。

 ![img](assets/12164434-a7aae58656094811847ffef3c49a703b.png)

 ![img](assets/12164440-f417735d752a4fba80f009ef5bafc057.png)

好了，到此[Deploy Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Deploy+Plugin)插件安装完成！