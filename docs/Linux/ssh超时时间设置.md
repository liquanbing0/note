# 临时方法
ssh -o ServerAliveInterval=60



# 永久方法

## 修改server端的/etc/ssh/sshd_config

ClientAliveInterval 60   ＃server每隔60秒发送一次请求给client，然后client响应，从而保持连接

ClientAliveCountMax 3  ＃server发出请求后，客户端没有响应得次数达到3，就自动断开连接，正常情况下，client不会不响应

 

## 修改client端的/etc/ssh/ssh_config添加以下：（在没有权限改server配置的情形下）

ServerAliveInterval 60     ＃client每隔60秒发送一次请求给server，然后server响应，从而保持连接

ServerAliveCountMax 3  ＃client发出请求后，服务器端没有响应得次数达到3，就自动断开连接，正常情况下，server不会不响应

# 设置set TIMEOUT的情况
```
echo "export TMOUT=1800" >> /etc/profile && source /etc/profile
```
临时防止超时退出
直接输入 
```
unset TIMEOUT
```
#linux 