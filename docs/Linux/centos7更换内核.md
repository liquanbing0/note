```
yum install -y kernel-ml-5.8.8-1.el7.elrepo.x86_64.rpm #安装内核
grub2-set-default 'CentOS Linux (5.8.8-1.el7.elrepo.x86_64) 7 (Core)' #设置默认内核
grub2-editenv list #查询默认启动内核
cat /boot/grub2/grub.cfg|grep menuentry #查询当前系统中的全部内核
```

基于boot安装的  所有内核列表在  /etc/grub2.cfg
基于efi安装的  所有内核列表在 /boot/efi/EFI/centos/grub.cfg

>centos7内核下载地址
http://elrepo.org/linux/kernel/el7/x86_64/RPMS/
#linux 