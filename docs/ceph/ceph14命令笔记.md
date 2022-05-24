# osd相关

## 查看osd分配磁盘情况：
>ceph-disk list

## 清除osd分区gpt信息
>dd if=/dev/zero of=/dev/sd<i> bs=1M count=100  oflag=direct

## 格式化磁盘
>sgdisk -Z /dev/sdk

## 停止服务
>systemctl stop ceph-osd@<id>

## 查看主机的osd
>ceph osd status <host>

## 删除osd
>ceph osd out osd.<id>

## 删除auth
>ceph auth del osd.<id>

## 清除osd crush
>ceph osd crush remove osd.<id>

## 删除
>ceph osd rm osd.<id>

## 删除Host
>ceph osd crush remove <host>

## 手动部署osd
>ceph-disk prepare --osd-id 990 --block.db /dev/sd<缓存> --block.wal /dev/sd<缓存> /dev/sd<数据>

## pg incomplete 状态修复
>ceph health detail --查到osdid/pgid
- 两副本就去对应的两个osd 三个副本就去对应的三个osd
- 对应osd节点执行
>systemctl stop ceph-osd@<id>	
- 对应osd节点执行
>ceph-objectstore-tool --pgid <pgid> --op mark-complete --data-path /var/lib/ceph/osd/ceph-<osd-id>/ --type bluestore	
- 对应osd节点执行
>systemctl start ceph-osd@<id>

## osd权重更改
>ceph osd crush reweight osd.osd号 权重值

## osd移动到host
>ceph osd crush remove  osd.<osdid>
>ceph osd crush add osd.<osdid> <权重值> host=<hostname>

# pool相关

## 创建pool
>ceph osd pool create <poolname> <pgnum> <pgpnum>

## 删除pool
>ceph osd pool delete <poolname> <poolname> --yes-i-really-really-mean-it

## 对应pool调整pg数
```bash
# 查询pg和pgp
ceph osd pool get <poolname> <pg_num>
ceph osd pool get <poolname> <pgp_num>
# 设置pg和pgp
ceph osd pool set <poolname> <pg_num>
ceph osd pool set <poolname> <pgp_num>
```
## pool重命名
>osd pool rename <poolname> <poolname>

## pool pg均衡
>>>get_pg.sh
```shell
ceph pg dump | awk '/^PG_STAT/ { col=1; while($col!="UP") {col++}; col++ }  
 /^[0-9a-f]+\.[0-9a-f]+/ { match($0,/^[0-9a-f]+/); pool=substr($0, RSTART, RLENGTH); poollist[pool]=0;  
 up=$col; i=0; RSTART=0; RLENGTH=0; delete osds; while(match(up,/[0-9]+/)>0) { osds[++i]=substr(up,RSTART,RLENGTH); up = substr(up, RSTART+RLENGTH) }  
 for(i in osds) {array[osds[i],pool]++; osdlist[osds[i]];}  
}  
END {  
 printf("\n");  
 printf("pool :\t"); for (i in poollist) printf("%s\t",i); printf("| SUM \n");  
 for (i in poollist) printf("--------"); printf("----------------\n");  
 for (i in osdlist) { printf("osd.%i\t", i); sum=0;  
 for (j in poollist) { printf("%i\t", array[i,j]); sum+=array[i,j]; poollist[j]+=array[i,j] }; printf("| %i\n",sum) }  
 for (i in poollist) printf("--------"); printf("----------------\n");  
 printf("SUM :\t"); for (i in poollist) printf("%s\t",poollist[i]); printf("|\n");  
}'
```
>>>ruilin.sh
```shell
sh /root/get_pg.sh
ceph osd getmap -o /tmp/osdmap.bin
osdmaptool --upmap-pool chengdu2-zone1-dataset1-pool-1 --upmap-max 100 --upmap-deviation 1 /tmp/osdmap.bin --upmap /tmp/upmapfile
source /tmp/upmapfile
sleep 2
sh /root/get_pg.sh
ceph osd getmap -o /tmp/osdmap.bin
osdmaptool --upmap-pool chengdu2-zone1-dataset1-pool-2 --upmap-max 100 --upmap-deviation 1 /tmp/osdmap.bin --upmap /tmp/upmapfile
source /tmp/upmapfile
sleep 2
sh /root/get_pg.sh
ceph osd getmap -o /tmp/osdmap.bin
osdmaptool --upmap-pool chengdu2-zone1-dataset1-pool-3 --upmap-max 100 --upmap-deviation 1 /tmp/osdmap.bin --upmap /tmp/upmapfile
source /tmp/upmapfile
sleep 2
sh /root/get_pg.sh
ceph osd getmap -o /tmp/osdmap.bin
osdmaptool --upmap-pool chengdu2-zone1-dataset1-pool-4 --upmap-max 100 --upmap-deviation 1 /tmp/osdmap.bin --upmap /tmp/upmapfile
source /tmp/upmapfile
```
>!上面两个脚本要同时使用

## 存储池副本数
```
# 获取存储池副本数
ceph osd dump |grep pool
或者
ceph osd pool get <poolname> size
# 设置存储池副本数为3
ceph osd pool set <poolname> size 3
```
## 储存池大小容量设置
```
查看池配额设置
$ ceph osd pool get-quota <poolname>

限制池中存放object的个数
$ ceph osd pool set-quota <poolname> max_objects <number>

限制池中最大存储的数据量大小
$ ceph osd pool set-quota <poolname> max_bytes <number>

```
## pg深度清洗
```
ceph health detail | grep 'not deep-scrubbed since' | awk '{print $2}' | while read pg; do ceph pg deep-scrub $pg; done
```


# crush相关

## 创建crush rule root
>ceph osd crush move <hostname>  root=<crush-rule-root-name>
>ceph osd crush move CDJD-PSC-P11F1-SPOD3-PM-OS01-BCONEST-DATA-OSD-01  root=chengdu2-zone1-dataset1-replicated-root-2

## 创建crush rule 并关联crush rule root
>ceph osd crush rule create-simple <crush-rule-name> <crush-rule-root-name> host
>ceph osd crush rule create-simple chengdu2-zone1-dataset1-replicated-rule-1 chengdu2-zone1-dataset1-replicated-root-1 host

## pool关联crush roule
>ceph osd pool set <poolname> crush_rule <crush-rule-name>
>ceph osd pool set chengdu2-zone1-dataset1-pool-1 crush_rule chengdu2-zone1-dataset1-replicated-rule-1

## 重命名crush rule root
>ceph osd crush rename-bucket chengdu2-zone1-dataset1-replicated-root-2 chengdu2-zone1-dataset1-root-2
>ceph osd crush rename-bucket <crush-rule-root-name> <crush-rule-root-name-new>

## 重命名crush rule 
>ceph osd crush rule rename  chengdu2-zone1-dataset1-pool4-replicated-rule chengdu-zone2-dataset1-pool4-replicated-rule
>ceph osd crush rule rename  <crush-rule-name> <crush-rule-name-new>

## pool重命名
>osd pool rename <poolname> <poolname>

## 删除crush rule root
>ceph osd crush rm <crush-rule-root-name>

## 删除crush rule 
>ceph osd crush rule rm <crush-rule-name>

## pool重命名
>osd pool rename <poolname> <poolname> --yes-i-really-really-mean-it
>ceph osd pool delete chengdu-zone2-dataset1-pool-1 chengdu-zone2-dataset1-pool-1 --yes-i-really-really-mean-it

## crush rule导出导入
- 导出
>ceph osd getcrushmap -o test-crush-map
- 反编译
>crushtool -d test-crush-map -o test-crush-map.txt
- 修改
>vi test-crush-map.txt
- 编译
>crushtool -c test-crush-map.txt -o test-crush-map1
- 导入
>ceph osd setcrushmap -i test-crush-map1

## crush rack编辑
```
# 查看当前rack
ceph osd tree |grep -E "host|rack"

# 移动host到其他rack
ceph osd crush move <crush_host> rack=<crush_id>
ceph osd crush move node-25 rack=11
```



# storage-policy相关

## 创建storage-policy
>radosgw-admin storage-policy set --storage-policy-name <storage-policy-name>  --storage-policy-pool-weight=10  --storage-policy-pool-storage-class="STANDARD"  --storage-policy-pool-work-time="00:00-24:00" --storage-policy-pool-alive=true --storage-policy-pool-id=<storage-policy-id> --storage-policy-pool-name="<poolname>"   --storage-policy-pool-cluster-conf="<mon-ceph.conf-pwd>"
>radosgw-admin storage-policy set --storage-policy-name default  --storage-policy-pool-weight=10  --storage-policy-pool-storage-class="STANDARD"  --storage-policy-pool-work-time="00:00-24:00" --storage-policy-pool-alive=true --storage-policy-pool-id=12 --storage-policy-pool-name="chengdu2-zone1-dataset4-pool-1"   --storage-policy-pool-cluster-conf="/etc/ceph/data-cluster/dataset4/ceph.conf"

## 删除storage-policy
>radosgw-admin storage-policy rm --storage-policy-name=<storage-policy-name>
>radosgw-admin storage-policy rm --storage-policy-name=default

## 创建对象存储user
- 默认storage-policy-name=default
>radosgw-admin user create --uid=admin --display-name=admin --caps="users=*;buckets=*;metadata=*;usage=*;zone=*" 
- 默认storage-policy-name=observer-policy1
>radosgw-admin user create --uid=observer1 --display-name=observer1 --caps="users=*;buckets=*;metadata=*;usage=*;zone=*" --storage-policy-name=observer-policy1

## 查看user的key
>radosgw-admin user info --uid=<用户名>

**注意：更改storage-policy后需要重启全部rgw**

# regin相关

## 查看pool空间大小
>ceph df

## 查看zone名称
>radosgw-admin zone list

## 查看zonegroup名称
>radosgw-admin zonegroup list

## 查看realm名称
>radosgw-admin realm list

## 查看zone详情
>radosgw-admin zone get

## 查看zonegroup名称
>radosgw-admin zonegroup get

## 查看realm名称
>radosgw-admin realm get


# 创建region等相关信息

## 创建cmecloud的realm
>radosgw-admin realm create --rgw-realm=cmecloud --default

>这步应该用不上.....是新建zone和zonegroup
>radosgw-admin zonegroup add --rgw-zonegroup=chengdu2 --rgw-zone=chengdu2-zone1 --default

>将zonegroup加进cmecloud的realm，rgw-zonegroup和rgw-zone根据实际情况可能是default
>radosgw-admin zonegroup modify --rgw-zonegroup=chengdu2 --rgw-realm=cmecloud
>radosgw-admin zone modify --rgw-zone=chengdu2-zone1 --rgw-realm=cmecloud

## 改名
>radosgw-admin zonegroup rename --rgw-zonegroup=default --zonegroup-new-name=chengdu2
>radosgw-admin zone rename --rgw-zone=default --zone-new-name=chengdu2-zone1

## 修改数据池名字
>ceph osd pool rename <oldname>  <newname>

## modify为master和default（master?）
>radosgw-admin zonegroup modify --rgw-zonegroup=chengdu2 --default
>radosgw-admin zone modify --rgw-zone=chengdu2-zone1 --default

>radosgw-admin zone get > infile
>vim infile(把原来default.的东西都改成类似chengdu2-zone1.)
>radosgw-admin zone set < infile

## 修改rgw节点ceph.conf，commit,然后重启rgw
>vim /etc/ceph/ceph.conf

## 在client下添加下面内容
>rgw_realm = cmecloud
>rgw_zonegroup = anhui1
>rgw_zone = anhui1-zone1

## 提交
>radosgw-admin period update --commit

## 重启rgw，需根据服务器实际实例名称修改
>for  i in `seq 0 10`;do systemctl restart ceph-radosgw@rgw.AHHN-PSC-S1X1-SPOD2-PM-OS01-BCONEST-RGW01.rgw$i |grep running;done
#ceph 