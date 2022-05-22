[toc]
# 通过访问sysctl的内核ipv4.ip_forward来判断转发是否开启。
说明：现在有些网络已经普及ipv6的，所以下面的命令可以从ipv4改为ipv6即可

使用sysctl：

sysctl net.ipv4.ip_forward net.ipv4.ip_forward = 0

或者检查/proc下的文件：

cat /proc/sys/net/ipv4/ip_forward 0

ipv4转发没有开启 (值为 0)

# 启动IP转发
通过sysctl可以开启ipv4的转发功能 (无需重启)：

sysctl -w net.ipv4.ip_forward=1

或者

echo 1 > /proc/sys/net/ipv4/ip_forward

这种设置只是暂时的，它的效果会随着计算机的重启而失效。

通过在/etc/sysctl.conf设置参数：

如果想使IP转发永久生效，就请修改/etc/sysctl.conf ，在这里可以增加一条net.ipv4.ip_forward = 1

/etc/sysctl.conf: net.ipv4.ip_forward = 1

如果ipv4转发项已被设为0那么你只需要将它改为1。

 

# 要想是更改生效，你需要执行以下指令：
sysctl -p /etc/sysctl.conf

在红帽系列的发行版上可以通过重启网络服务使之生效：

service network restart

而在Debian/Ubuntu系列的发行版则用这样的命令：

早期版本
```
/etc/init.d/procps.sh restart
```
最新版本
```
/etc/init.d/procps restart
```
#linux #端口转发 