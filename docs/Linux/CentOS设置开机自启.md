/etc/init.d/
这个文件夹里面的都是开机自启的


写一个脚本放在里面

授予775权限
```
chkconfig --add xxx
chkconfig xxx on
```

注意脚本里面开头有特殊格式
```
#!/bin/bash
#chkconfig: 2345 80 90
#description:auto_run
```
#linux 