---
title: 08-Centos7虚拟机扩展磁盘
date: 2022-04-30 16:55:47
tags: my-notes
---
Centos虚拟机扩展磁盘



## 简介

CentOS7虚拟机原硬盘空间只分配了10GB，需要扩容到20GB。 
环境：VMware 10

## VMware分配空间

选中虚拟机->虚拟机设置->硬盘->实用工具->扩展->设置最大磁盘大小->点击扩展 
![这里写图片描述](https://img-blog.csdn.net/20170906213815653?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvV2FuZ19YaW5fU0g=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## CentOS7内部分配

可以参考：<http://jingyan.baidu.com/article/54b6b9c0fc8b0b2d583b47c6.html>

- 查看当前磁盘空间，/dev/mapper/cl-root硬盘空间只有8GB，打算扩容：

```shell
# df -h
Filesystem           Size  Used Avail Use% Mounted on
/dev/mapper/cl-root  8.0G  3.8G  4.3G  47% /
devtmpfs             482M     0  482M   0% /dev
tmpfs                493M     0  493M   0% /dev/shm
tmpfs                493M  6.7M  486M   2% /run
tmpfs                493M     0  493M   0% /sys/fs/cgroup
/dev/sda1           1014M  184M  831M  19% /boot
tmpfs                 99M     0   99M   0% /run/user/0
```

- 对新增的硬盘空间做新增分区（硬盘数没有增加，增加的是空间）

```shell
#  fdisk /dev/sda
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n
Partition type:
   p   primary (2 primary, 0 extended, 2 free)
   e   extended
Select (default p): p
Partition number (3,4, default 3): 3
First sector (20971520-41943039, default 20971520): 
Using default value 20971520
Last sector, +sectors or +size{K,M,G} (20971520-41943039, default 41943039): 
Using default value 41943039
Partition 3 of type Linux and of size 10 GiB is set

Command (m for help): t
Partition number (1-3, default 3): 3
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): p

Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000bc924

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    20971519     9436160   8e  Linux LVM
/dev/sda3        20971520    41943039    10485760   8e  Linux LVM

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

- 重启系统 reboot
- 查看当前分区类型，本例类型为xfs

```shell
# df -T /dev/sda1
Filesystem     Type 1K-blocks   Used Available Use% Mounted on
/dev/sda1      xfs    1038336 188240    850096  19% /boot
```

- 在新磁盘上创建xfs文件系统

```shell
# mkfs.xfs /dev/sda3
meta-data=/dev/sda3              isize=512    agcount=4, agsize=655360 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=2621440, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

- 创建PV

```shell
# pvcreate /dev/sda3
WARNING: xfs signature detected on /dev/sda3 at offset 0. Wipe it? [y/n]: y
  Wiping xfs signature on /dev/sda3.
  Physical volume "/dev/sda3" successfully created.
# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               cl
  PV Size               9.00 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              2303
  Free PE               0
  Allocated PE          2303
  PV UUID               MlRwjY-TmVF-H8PV-heSz-ALGL-Q7sp-jFU6Al

  "/dev/sda3" is a new physical volume of "10.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sda3
  VG Name               
  PV Size               10.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               0hmgH0-0wVg-jWUW-65WX-1TYb-sUGH-6jF1qm
```

- PV加入VG，vgextend后接VG Name，本例中为cl

```shell
vgextend cl /dev/sda3
```

```shell
# vgdisplay
  --- Volume group ---
  VG Name               cl
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               9.00 GiB
  PE Size               4.00 MiB
  Total PE              2303
  Alloc PE / Size       2303 / 9.00 GiB
  Free  PE / Size       0 / 0   
  VG UUID               dYdb4l-wMUh-e2xv-WiaJ-Oa52-NvdF-s5ICJC

# vgextend cl /dev/sda3
```

- VG加入LV

```shell
# lvextend -l +2559 /dev/cl/root
  Size of logical volume cl/root changed from 8.00 GiB (2047 extents) to 17.99 GiB (4606 extents).
  Logical volume cl/root successfully resized.
```

后两个参数“+2559”和“/dev/cl/root”来源详解： 
“+2559”来自于vgdisplay命令的Free PE/Size字段

```shell
# vgdisplay
  --- Volume group ---
  VG Name               cl
  ...
  VG Size               18.99 GiB
  PE Size               4.00 MiB
  Total PE              4862
  Alloc PE / Size       2303 / 9.00 GiB
  Free  PE / Size       2559 / 10.00 GiB
  VG UUID               dYdb4l-wMUh-e2xv-WiaJ-Oa52-NvdF-s5ICJC
```

“/dev/cl/root”来自于lvdisplay命令的LV Path字段。

```shell
# lvdisplay
  ...
  --- Logical volume ---
  LV Path                /dev/cl/root
  ...
```

- 调整文件系统大小，本例中是xfs文件系统使用xfs_growfs命令调整，若其他文件系统，如ext4使用resize2fs命令，注意区分。

```shell
# xfs_growfs /dev/cl/root
# xfs_growfs   /dev/mapper/centos-root  # centos7命令
meta-data=/dev/mapper/cl-root    isize=512    agcount=4, agsize=524032 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=2096128, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096128 to 4716544
```

## 结果

`/dev/mapper/cl-root`从8G增加到了18G

```shell
# df -h
Filesystem           Size  Used Avail Use% Mounted on
/dev/mapper/cl-root   18G  3.8G   15G  21% /
devtmpfs             482M     0  482M   0% /dev
tmpfs                493M     0  493M   0% /dev/shm
tmpfs                493M  6.7M  486M   2% /run
tmpfs                493M     0  493M   0% /sys/fs/cgroup
/dev/sda1           1014M  184M  831M  19% /boot
tmpfs                 99M     0   99M   0% /run/user/0https://www.cnblogs.com/x_wukong/tag/Linux/)
```