# 说明
前阵子上线，一次性上了十个服务，一直上到凌晨才完事，期间每个服务都要先输入跳板机的登录信息来登录跳板机，然后再输入线上服务器的信息来登录线上服务器，实在是太过于麻烦，而且有些服务还有好几台服务器，检查问题的时候，服务器跳来跳去的，简直苦不堪言。
于是，便萌发了用 shell 脚本的方式来一键跳转的想法，先上 github 上搜了搜现成的轮子，发现都不太好用，要不就是没说明，要不就是只能登一台服务器，不能满足从跳板机 A 跳转到线上服务器 B 的需求。

所以，还是自己动手，丰衣足食。

# Shell 脚本
Shell 脚本已经发布到了`github`上，链接在此：[https://github.com/MFrank2016/GotoSSH](https://github.com/MFrank2016/GotoSSH)
本地脚本链接
[[gotossh脚本]]

这个脚本借鉴了`AutoSSH`的脚本，并将其进行了修改，使得能够支持从跳板机直接跳转登录服务器。

# 使用说明
`GotoSSH`是一个一键登录 SSH 的脚本，可直接从跳板机登录到线上服务器，致力于帮助你提高工作效率。
# 安装依赖
CentOS :

```
$ sudo yum install -y expect
```

Ubuntu :

```
$ sudo apt-get install tcl tk expect
```

Mac :

```
$ sudo brew install expect
```

# 安装 GotoSSH
```
$ git clone https://github.com/MFrank2016/GotoSSH.git
$ cd GotoSSH
$ chmod a+x gotossh
$ sudo cp gotossh /usr/local/bin/
```

# 配置

```
$ vim ~/.gotossh_config
server_name|ip|username|password|port|rely_server_no
```
最后一列是代表该服务器依赖于哪个服务器，如果该列的值设置为 0，代表不依赖于其他服务器，否则代表需要先登录其他服务器后才能登录该服务器，目前暂时只能从 A 跳转到 B，不支持多跳转，感觉没有必要，如果有需要，把脚本稍微改改就行了。

# 示例
------------------------

```
$ vim ~/.gotossh_config
test_server|192.168.0.1|root|password|22|0
online_server|192.168.2.2|root|password|22|1
```

# 使用说明
------------------------

```
$ gotossh
######################################################################################
#                                  [GOTO SSH]                                        #
#                                                                                    #
#                                                                                    #
# [1] test_server - 192.168.0.1:root                                                 #
# [2] online_server - 192.168.2.2:root                                               #
#                                                                                    #
#                                                                                    #
######################################################################################
Server Number:(Input Server Number Here)
```

OR

```
gotossh 1
```

OR

```
gotossh 2
```

因为配置文件里设置了服务器 2 依赖于服务器 1，所以会先登录服务器 1，然后再登录服务器 2。
#主机安全 #linux #跳板 