> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/kxzhaohuan/article/details/81713933)
[toc]
环境
==

os:centos 7.3 1611

应用：yum-utils

互联网源：阿里云

步骤
==

删除 / etc/yum.repos.d 下所有源文件

下载源 repo 到本地

```
wgetw  -O /etc/yum.repos.d/aliyun.repo https://mirrors.aliyun.com/repo/Centos-7.repo

```

安装 yum-utils 提供 reporsync 服务

```
yum install yum-utils -y

```

查看 yum 源仓库标识

```
[root@localhost yum.repos.d]# yum repolist
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
源标识 源名称 状态
base/7/x86_64 CentOS-7 - Base - mirrors.aliyun.com 9,591
extras/7/x86_64 CentOS-7 - Extras - mirrors.aliyun.com 196
updates/7/x86_64 CentOS-7 - Updates - mirrors.aliyun.com 657
repolist: 10,444

```

根据源标识同步源到本地目录

```
[root@localhost ~]# reposync -r base -p /var/www/html/     #这里同步base目录到本地

```

注意：
===

部门互联网 yum 源不支持同步

参考资料
====

[http://www.cnblogs.com/chengd/articles/6912938.html](http://www.cnblogs.com/chengd/articles/6912938.html)

[https://www.2cto.com/net/201512/455901.html](https://www.2cto.com/net/201512/455901.html)
#linux 