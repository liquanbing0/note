我们每次用ssh登录都会特别的慢，我们可以修改配置文件
```
vim /etc/ssh/sshd_config

UseDNS yes :把注释去掉，然后no 改成yes

GSSAPIAuthentication no 把yes改成no
```
#linux 