---
title: 10-ubantu安装工具命令
keywords:
- 10-ubantu安装工具命令
- mlzhang
description : "10-ubantu安装工具命令"
---
将vim设置为默认文本编辑器

```shell
echo export EDITOR=/usr/bin/vim >> ~/.bashrc 
```



修改Host文件

```shell
sudo gedit /etc/hosts
```



chorme

```shell
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
```



fish

软件源所在文件目录

```shell
/etc/apt/sources.list.d

apt update
apt install fish

```



sublime

http://www.sublimetext.com/docs/3/linux_repositories.html

```shell
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add - 
echo -e "\n[sublime-text]\nServer = https://download.sublimetext.com/arch/dev/x86_64" | sudo tee -a /etc/pacman.conf
apt-get update 
apt-get install sublime-text
# 启动
subl
```

```
# 修改host 添加
127.0.0.1 license.sublimehq.com
127.0.0.1 45.55.255.55
127.0.0.1 45.55.41.223

# 注册码
----- BEGIN LICENSE -----
sgbteam
Single User License
EA7E-1153259
8891CBB9 F1513E4F 1A3405C1 A865D53F
115F202E 7B91AB2D 0D2A40ED 352B269B
76E84F0B CD69BFC7 59F2DFEF E267328F
215652A3 E88F9D8F 4C38E3BA 5B2DAAE4
969624E7 DC9CD4D5 717FB40C 1B9738CF
20B3C4F1 E917B5B3 87C38D9C ACCE7DD8
5F7EF854 86B9743C FADC04AA FB0DA5C0
F913BE58 42FEA319 F954EFDD AE881E0B
------ END LICENSE ------

```

mysql

```shell
apt-get install mysql-server mysql-client libmysqlclient-dev
mysql -u root -p
```



redis

```shell
apt-get install redis-server

```



navicat

https://blog.csdn.net/ouzhuangzhuang/article/details/81739933

破解方法

https://blog.csdn.net/liumengyan_ysu/article/details/44224735



定时删除注册信息可以永久使用

https://blog.csdn.net/a295277302/article/details/78143010

```shell
30 14  *  *  2 rm -f /home/lanms/.navicat64/system.reg  # 每周二，14：30执行一次
```

```
vi /etc/profile
export PATH=$PATH:/home/lanms/Desktop/software/navicat121_premium_cs_x64

vi ~/.bashrc
alias navicat="/home/lanms/Desktop/software/navicat121_premium_cs_x64/start_navicat"
```

解决乱码

选项里左边选择第一个，在右边第一个下拉框中选择Noto Sans mono CJK SC Regular

https://blog.csdn.net/sinat_26546385/article/details/80381282