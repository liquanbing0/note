```toc
```
## 前言

这两年阿里云盘从内测到现在慢慢已经普及, 相比百度和天翼云盘, 目前免费阿里云盘没有限速, 也支持文件分享转存, 所以很多小伙伴就开始相互分享一些影视文件. 也有大神开发出了开源服务, 将阿里云网盘部署成为一个 WebDAV 服务, 让用户可以使用`infuse`,`nPlayer`等等客户端连接在线观看.

本项目实现了阿里云盘的 webdav 协议，只需要简单的配置一下，就可以让阿里云盘变身为 webdav 协议的文件服务器。 基于此，你可以把阿里云盘挂载为 Windows、Linux、Mac 系统的磁盘，可以通过 NAS 系统做文件管理或文件同步，更多玩法等你挖掘

本文为 [Stille](https://www.ioiox.com/stille.html) 原创文章. 经实践, 测试, 整理发布. 如需转载请联系作者获得授权, 并注明转载地址.

## 原理

首先需要说明的是,`webdav-aliyundriver`的原理是, 当用户通过此程序在线观看或者下载时, 是由`webdav-aliyundriver`所在的服务器代理去从阿里云盘中下载, **并不像** OneDrive 之类的云盘是获取直连地址.

所以部署的服务器需要足够大的带宽, 国内外的 VPS 都有带宽, 线路, 流量的限制, 体验并不一定好, 而国内家用宽带普遍都是 100-1000M, 连接阿里云的速度也非常给力. 所以部署到家里群晖 NAS 中是非常好的选择.

## 部署

### 获取阿里云盘 Token

登陆阿里云盘 [https://www.aliyundrive.com/drive](https://www.aliyundrive.com/drive)  
按`F12`打开开发者模式  
点击 Application - Local Storage  
选中右侧的 token  
复制下方`refresh_token`, 注意无需复制双引号.
![[中间件/WebDAV/附件/Pasted image 20220522020050.png]]
### 群晖 docker 部署

#### 创建挂载文件夹

在任意目录创建文件夹用于挂载`refresh_token`更新数据. 本文以`docker/aliyunpan`为例.
![[中间件/WebDAV/附件/Pasted image 20220522020058.png]]
#### 下载镜像

docker - 注册表 - 搜索`webdav-aliyundriver`  
下载`zx5253/webdav-aliyundriver`
![[中间件/WebDAV/附件/Pasted image 20220522020108.png]]## 启动容器
docker - 映像  
选择`zx5253/webdav-aliyundriver`启动
![[中间件/WebDAV/附件/Pasted image 20220522020119.png]]
#### 配置高级设置

勾选`使用高级权限执行容器`
![[中间件/WebDAV/附件/Pasted image 20220522020128.png]]
高级设置 - `启用自动重新启动`
![[中间件/WebDAV/附件/Pasted image 20220522020134.png]]
高级设置 - 存储空间  
添加文件夹选择上文创建的`docker/aliyunpan`  
装载路径填写`/etc/aliyun-driver`
![[中间件/WebDAV/附件/Pasted image 20220522020141.png]]
高级设置 - 端口设置  
默认容器端口为`8080`, 为避免于其他可能存在的服务端口冲突, 映射本地端口, 本文以`12345`为例.
![[中间件/WebDAV/附件/Pasted image 20220522020149.png]]
高级设置 - 环境 - 添加以下变量

-   `ALIYUNDRIVE_REFRESH_TOKEN` 为上文获取的`refresh_token`
-   `ALIYUNDRIVE_AUTH_USER-NAME` 为 WebDAV 设置用户名
-   `ALIYUNDRIVE_AUTH_PASSWORD` 为 WebDAV 设置密码
![[中间件/WebDAV/附件/Pasted image 20220522020157.png]]
继续下一步完成启动容器
![[中间件/WebDAV/附件/Pasted image 20220522020204.png]]
## 使用

WebDAV 为标准通用协议, 支持 HTTP 和 HTTPS , 本文并未部署在公网, 所以 HTTP 即可, 下文将使用`infuse`和`群晖 NAS`来作为客户端连接测试. 其他客户端也同样使用`IP`,`端口`,`账号`,`密码`来登陆.

### infuse 测试

打开 infuse - 文件 - 新增文件来源
![[中间件/WebDAV/附件/Pasted image 20220522020214.png]]
添加 WebDAV
![[中间件/WebDAV/附件/Pasted image 20220522020222.png]]
填写群晖 NAS 内网 IP , 端口和账号密码.
![[中间件/WebDAV/附件/Pasted image 20220522020228.png]]
成功连接
![[中间件/WebDAV/附件/Pasted image 20220522020234.png]]
### 群晖 NAS 挂载 WebDAV 测试

直接在群晖内挂载此 WebDAV 就可以在群晖的文件管理里随时访问, 下载阿里云盘.  
打开 File Station - 工具 - 远程连接 - 连接设置
![[中间件/WebDAV/附件/Pasted image 20220522020241.png]]
选择 WebDAV
![[中间件/WebDAV/附件/Pasted image 20220522020247.png]]
主机 IP 填写群晖内网 IP, 端口填写上文映射的端口, 在填写账号密码完成.
![[中间件/WebDAV/附件/Pasted image 20220522020253.png]]
成功挂载至群晖 NAS 本地, 将需要下载的文件从网盘中直接拖到本地其他目录即可开始下载.
![[中间件/WebDAV/附件/Pasted image 20220522020302.png]]
