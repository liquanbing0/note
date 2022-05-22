/home文件夹重新划分独立分区

1. 备份home文件夹，数据丢失的时候可以使用
```bash
  cp -av  /home/*  /data/
```
2. 磁盘分区

  2.1 虚拟机中添加磁盘并让系统识别，使用
```bash
      # echo '- - -' >> /sys/class/scsi_host/host0/scan

      # echo '- - -' >> /sys/class/scsi_host/host1/scan

      # echo '- - -' >> /sys/class/scsi_host/host2/scan
```
  2.2 磁盘分区
```bash
      # fdisk /dev/sdc 创建磁盘分区 /dev/sdc1
```
3. 创建文件系统
```bash
  # mkfs.ext4 -L "/home" /dev/sdc1     -L 指定设备的卷标名
```
4. mkdir /mnt/home; mount /dev/sdc1 /mnt/home

5. init 1 单用户执行(会断网)  防止目录拷贝期间有人修改数据导致数据备份不完整

6. 将/home文件夹中的数据拷贝至新增的分区中并且删除/home下面的文件
```bash
   # cp -av /home/*   /mnt/home

   # rm -rf /home/*
```
7. 编辑文件挂载配置文件(/etc/fstab)，让其生效
```bash
   # vim /etc/fstab      

   blkid /dev/sdc1    # 将/dev/sdc1的UUID保留

   UUID=3941545c-9489-42b6-b5b5-29399839bd7f /home  ext4    defaults        0 0
```
8. mount -a 让刚刚配置的文件生效

9. 删除/mnt/home/下的文件、释放空间，并且卸载挂载目录/mnt/home
```bash
   rm -rf /mnt/home/*

   umount /mnt/home
```
10. init 5 重新以图形化的方式启动系统
#linux 
