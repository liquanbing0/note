# windows
第一步添加要转发端口的防火墙规则 再执行下面的

## 添加
```bash
netsh interface portproxy add v4tov4 listenaddress=127.0.0.1 listenport=9000 connectaddress=192.168.0.10 connectport=80
```
## 删除
```bash
netsh interface portproxy delete v4tov4 listenaddress=127.0.0.1 listenport=9000
```
## 查看已存在的转发规则
```bash
netsh interface portproxy show v4tov4
```

# linux

## 转发主机设置
```bash
systemctl start firewalld.service  #开启防火墙

echo "net.ipv4.ip_forward = 1"  >> /etc/sysctl.conf  #开启ipv4转发

sysctl -p

firewall-cmd --add-masquerade --permanent

firewall-cmd --permanent --add-forward-port=port=5001:proto=tcp:toport=5001:toaddr=192.168.2.33  #添加转发

firewall-cmd --zone=public --add-port=5001/tcp --permanent #放开对应端口

firewall-cmd --reload
 #重载配置 将上面三句句生效
```
#linux #windows  #主机安全 #端口转发 #跳板 