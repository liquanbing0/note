今天上午有个同事问，自己的进程被莫名其妙的杀死了，想知道是被那个进程误杀了。
第一个想法是用kernel hack系统调用的方式，刚打算翻出hook的代码，想起了貌似audit不就是干这个的吗
```bash
#auditctl -a exit,always -S kill
#auitctl -l
#tail -f /var/log/audit/audit.log 
```

加上这个规则后，再用kill杀死一个进程就可以看到是谁干的了。
```bash
type=SYSCALL msg=audit(1446528422.179:18502): arch=c000003e syscall=62 success=yes exit=0 a0=53f a1=8 a2=0 a3=53f items=0 ppid=1427 pid=2613 auid=0 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=tty1 ses=1 comm="bash" exe="/usr/bin/bash" subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 key=(null)
type=OBJ_PID msg=audit(1446528422.179:18502): opid=1343 oauid=0 ouid=0 oses=5 obj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 ocomm="top"
```
pid=2613  表示这个进程发的kill信号；ppid=1427 表示这个进程的父进程；

OBJ_PID的opid=1343 表示这个进程被kill了。

如果只想检测某一个进程是否被kill了，可以利用系统调用的参数，以kill系统调用为例，第一个参数就是被kill的进程的pid，所以可以通过如下命令只检测某一个进程是被谁kill了。
```bash
#auditctl -D //清空规则
#auditctl -a exit,always -S kill -F a0=0x6e9

-F参数表示额外匹配，这里选择匹配系统调用的第一个参数，参数内容是要监控进程的PID（这里要用16进制）。
```
#linux #开发 