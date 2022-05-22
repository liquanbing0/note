> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/ysuwangqiang/p/11487520.html)

mdadm：为软 RAID 提供管理界面，RAID 设备可命名为 / dev/md0、/dev/md1、/dev/md2、/dev/md3 等

命令的语法格式：mdadm[mode] <raiddevice> [options] <component-devices>

支持的 RAID 级别：LINEAR, RAID0, RAID1, RAID4, RAID5, RAID6, RAID10
```
**mdadm 选项：**

       -C: 创建模式

              -n #: 使用 #个块设备来创建此 RAID

              -l #：指明要创建的 RAID 的级别

              -a {yes|no}：自动创建目标 RAID 设备的设备文件

              -c CHUNK_SIZE: 指明块大小, 单位 k

              -x #: 指明空闲盘的个数

       -D：显示 raid 的详细信息

              mdadm-D /dev/md#

       管理模式：

              -f: 标记指定磁盘为损坏

              -a: 添加磁盘

              -r: 移除磁盘

       观察 md 的状态：cat /proc/mdstat

       使用 mdadm 创建并定义 RAID 设备

              # mdadm-C /dev/md0 -a yes -l 5 -n 3 -x 1 /dev/sd{b,c,d,e}1

       用文件系统对每个 RAID 设备进行格式化

              # mkfs.xfs/dev/md0

　　使用 mdadm 检查 RAID 设备的状况

              # mdadm-D/dev/md0

       增加新的成员

              # mdadm–G /dev/md0 –n4 -a /dev/sdf1

       生成配置文件：mdadm–D –s >> /etc/mdadm.conf

       停止设备：mdadm–S /dev/md0

       激活设备：mdadm–A –s /dev/md0 激活

       强制启动：mdadm–R /dev/md0

       删除 raid 信息：mdadm--zero-superblock /dev/sdb1
```
举例：创建一个可用空间为 1G 的 RAID1 设备，文件系统为 ext4，有一个空闲盘，开机可自动挂载至 / backup 目录

```
因raid1需要2个块设备，空闲盘也需要1个块设备，共需要3个块设备
1、在3个硬盘创建3个1G的分区，并修改分区格式为fd Linux raid auto
# fdisk /dev/sda
# fdisk /dev/sdb
# fdisk /dev/sdc
...分区步骤省略...

2、创建raid1
# mdadm -C /dev/md0 -a yes -l 1 -n 2 -x 1 /dev/sd{a,b,c}1

3、创建文件系统
# mkfs.xfs /dev/md0

4、查看/dev/md0的UUID
# blkid | grep md0
/dev/md0: UUID="228bb98d-a14c-4746-b48c-75c1595e9637" TYPE="xfs"

5、将md0挂载在/backup目录，并开机自动挂载，修改/etc/fstab文件
# vim /etc/fstab
UUID=228bb98d-a14c-4746-b48c-75c1595e9637 /backup xfs defaults 0 0

6、重启设备测试挂载

```
#linux 