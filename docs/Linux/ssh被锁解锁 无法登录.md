查看root用户的登录失败的次数
```sh
pam_tally2   --user root
```

重置计数器,即手动解锁,在真终端使用root用户登陆后执行：
```sh
pam_tally2 --user=root --reset
```
#linux 