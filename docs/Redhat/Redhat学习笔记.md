# 配置网络
## 主机名： node1.domain250.example.com IP 地址: 172.25.250.100/24 网关： 172.25.250.254 DNS： 172.25.250.254
```
nmcli com mod "eth0" ipv4.addr 192.168.2.2/24 ipv4.gateway 192.168.2.1 ipv4.dns 192.168.2.1 ipv4.method manual connection.autoconnect yes

hostnamectl set-hostname xxx
```

## 配置YUM
##  配置您的系统以使⽤默认存储库
配置您 的系统以使⽤默认存储库
YUM 存储库已可以从 http://foundation0.ilt.example.com/dvd/BaseOS 和
http://foundation0.ilt.example.com/dvd/AppStream 使⽤配置您的系统，以
将这些位置⽤作默认存储库
```
vim /etc/yum.repos.d/BaseOS.repo
[BaseOS]
name = BaseOS
baseurl = http://foundation0.ilt.example.com/dvd/BaseOS
gpgcheck = 0
enabled = 1

vim /etc/yum.repos.d/AppStream
[AppStream]
name = AppStream
baseurl = http://foundation0.ilt.example.com/dvd/AppStream
gpgcheck = 0
enabled = 1
```

# 调试 SELinux
## 调试 SELinux
⾮标准端口 82 上运⾏的 Web 服务器在提供内容时遇到问题。根据需要调试并解决问
题，使其满⾜以下条件：
系统上的 Web 服务器能够提供 /var/www/html 中所有现有的 HTML ⽂件（注：不
要删除或以其他⽅式改动现有的⽂件内容）
Web 服务器在端口 82 上提供此内容
Web 服务器在系统启动时 ⾃动启动



# 可参考文档
[[考试原题 （不带答案）]]
[[张忆伟-2021年最新战报]]
[[【2022版】红帽8.2-RHCSA+RHCE（带答案）]]
[[2022年新增考试题目变化(仅供参考)]]
#Redhat