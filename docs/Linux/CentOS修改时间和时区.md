```bash
修改timezone

sudo cp -a /usr/share/zoneinfo/Etc/GMT-8  /etc/localtime #修改时区

date -R   # 展示当前的timezone

ntpdate time.nist.gov  # 与互联网时间同步
```
#linux 