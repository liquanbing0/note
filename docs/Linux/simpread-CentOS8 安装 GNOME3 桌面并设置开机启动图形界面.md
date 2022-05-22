> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.linuxprobe.com](https://www.linuxprobe.com/centos8-gnome3.html)<table><tbody><tr><td>导读</td><td><strong>本篇文章介绍如何在 <a href="https://www.linuxprobe.com/" title="centos" target="_blank">CentOS</a>8 <a href="https://www.linuxprobe.com/" title="linux" target="_blank">Linux</a> 操作系统中安装 GNOME3 桌面环境和 GDM(GNOME Display Manager) 现实环境管理器。</strong></td></tr></tbody></table>

**环境**

CentOS8 Minimal

**安装 GNOME3**

首先列出网络源中可以使用的 Groups：

```
[root@localhost ~]# yum grouplist


```

![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image1.png)  
下一步我们需要 “Available Environment Groups” 下面的 “Server with GUI”，这个“Server with GUI” 环境包使用的桌面环境就是 GNOME3。下面进行安装，下载大约 1GB 的安装包：

```
[root@localhost ~]# yum groupinstall "Server with GUI"


```

![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image2.png)  
安装完成！

**设置 GDM 开机启动**

查看`gdm.service`是否启动，发现 gdm 服务没有启动。

```
[root@localhost ~]# systemctl status gdm
● gdm.service - GNOME Display Manager
   Loaded: loaded (/usr/lib/systemd/system/gdm.service; enabled; vendor preset: enabled)
   Active: inactive (dead)


```

下面设置`gdm`开机启动，并立即启动该服务：

```
[root@localhost ~]# systemctl enable gdm --now


```

![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image3.png)

**设置系统启动级别为 graphical**

默认情况，CentOS8 默认启动级别为`multi-user.target`。

```
[root@localhost ~]# systemctl get-default
multi-user.target


```

![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image4.png)  
下面设置 CentOS8 的启动级别为`graphical.target`

```
[root@localhost ~]# systemctl set-default graphical.target 
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target ¡ú /usr/lib/systemd/system/graphical.target.

[root@localhost ~]# systemctl get-default 
graphical.target


```

![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image5.png)

**进入 GNOME3 桌面**

设置完上面的配置之后，重启操作系统。进入欢迎界面：  
![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image6.png)  
![](https://www.linuxprobe.com/wp-content/uploads/2020/06/image7.png)

**总结**

本篇文章介绍如何在 CentOS8 Linux 操作系统中安装 GNOME3 桌面环境和 GDM(GNOME Display Manager) 现实环境管理器。

本文原创地址：[https://www.linuxprobe.com/centos8-gnome3.html](https://www.linuxprobe.com/centos8-gnome3.html) 
#linux 