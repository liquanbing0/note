[toc]
rhel8上，停止network.service，因此只能通过NM进行网络配置，包括动态ip和静态ip。在rhel8上，必须开启NM，否则无法使用网络。

系统启用：
```bash
systemctl start NetworkManager
```
# 查看网卡名称nmcli device (查看网卡名称）。
```
nmcli connection add con-name new1 ifname ens32 type ethernet 
```

new1为新名字 ens32为旧名字 这句命令适用于 nmtui未读到配置的情况

# 读到了就用下面的方法

这样会在/etc/sysconfig/network-scripts/自动生成文件并激活网卡。
或者只要在nmtui配置下信息 就会自动生成配置文件 nmtui 也可以直接更改网卡名 也就是配置文件的NAME参数

# 重启网路

只能单张网卡重启
```sh
nmcli c reload xxx  #c 是connect xxx是网卡名 可能不会生效 
nmcli c up ethX
nmcli d reapply ethX
nmcli d connect ethX #d 是device
ifdown xxx && ifup xxx
```
重启所有网卡
```sh
sudo nmcli c reload
```
#linux 