+++
title = "通过Rsync把EAS主应用端同步到备应用端"
description = "通过Rsync把EAS主应用端同步到备应用端"
tags = [
    "rsync",
    "kingdee",
]
date = "2020-03-27"
categories = [
        "other"
]
+++
# 通过Rsync把EAS主应用端同步到备应用端
原EAS只有一台服务器,为了防止这台服务器故障,导致EAS故障时间较久,现新增一台EAS服务器做为备用,主eas服务器每天定时通过rsync命令,把文件同步到备服务器,防止一台服务故障,导致服务长时间不可用

# 环境
- 源主机,主EAS应用端:10.0.2.68
- 目标主机,备EAS应用端:10.0.2.25

# 操作步骤
在源主机上面执行rsync命令,把/kingdee82文件夹里面的数据同步到备应用端,每天一次

### 1.在目标服务器上面做如下操作

---

```
yum -y install xinetd rsync 

vim /etc/rsyncd.conf
```
输入下面的内容

```
uid=root                                    #以指定的 UID 传输文件
gid=root                                    #以指定的 GID 传输文件
hosts allow=10.0.2.68                  #允许指定主机访问
hosts deny=*                                #阻止指定主机访问
use chroot=yes
max connections=10                          #允许的最大连接数
pid file=/var/run/rsyncd.pid                #指定pid文件路径
lock file=/var/run/rsync.lock               #指定进程锁文件
log file=/var/log/rsyncd.log                #指定日志路径
timeout=600                                 #连接超时时间
port=873                                    #指定tcp端口

[eas]
path = /kingdee
# hosts you allow to access
hosts allow = 10.0.2.68
hosts deny = *
list = true
uid = root
gid = root
read only = false
```

```
chkconfig xinetd on
chkconfig rsync on
/etc/init.d/xinetd restart
```

### 2.在源服务器上执行如下操作

下面这个操作,是把本地/kingdee文件夹同步到备服务器的,但这个未启用`强一致性`,假如在源服务器上面文件已经删除,但备服务器上面的文件不会删除
```
rsync -avz  /kingdee/ 10.0.2.25::eas
```
所以应使用下面这个命令,这个是两个目录 完全保持一致,源删除,目标也删除
```
rsync -avz --delete /kingdee/ 10.0.2.25::eas
```

### 3.设置定时任务
```
vim /etc/crontab
```
加入下面的内容
```
00 4 * * * root        rsync -avz --delete /kingdee/ 10.0.2.25::eas
```

