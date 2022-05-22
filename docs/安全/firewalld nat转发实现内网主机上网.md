# 转发主机设置
```bash
systemctl start firewalld.service  #开启防火墙

echo "net.ipv4.ip_forward = 1"  >> /etc/sysctl.conf  #开启ipv4转发

sysctl -p

firewall-cmd --add-masquerade 
--permanent

firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o 外网网卡名 -j MASQUERADE -s $(nat网段)/24
```

 # 重载配置 将上面两句生效
```bash
#被转发主机配置
#临时生效方法
route add default gw $(转发主机ip) dev $(被转发主机网卡名)    #设置默认路由

#永久生效方法
echo "GATEWAY=$(转发主机ip)" >> /etc/sysconfig/network-scripts/ifcfg-$(被转发主机网卡名)

systemctl restart network
```
# 如果涉及chrony 一定要重启它 主节点  他不会自动识别路由变化
```bash
echo "GATEWAY=10.99.100.1" >> /etc/sysconfig/network-scripts/ifcfg-p4p2
echo "DNS1=8.8.8.8" >> /etc/sysconfig/network-scripts/ifcfg-p4p2
```
#linux #主机安全 #端口转发 #跳板 