+++
title = "DataGuard standby数据库的日常维护"
description = "DataGuard standby database的日常维护"
tags = [
    "oracle",
    "database"
]
date = "2020-03-21"
categories = [
        "Database"
]
+++
参考网址：
```
https://www.cnblogs.com/wlmq/articles/6069477.html
```
---

### 1.停止Standby

```
# 查看备库是否在应用日志进行恢复
select process, status from v$managed_standby;

alter database recover managed standby database cancel;
shutdown immediate;
```

 
### 2.切换到只读模式
#### 2.1由shutdown模式切换到只读模式
```
startup nomount;
alter database mount standby database;
alter database open read only;
```

#### 2.2由应用日志模式切换到只读模式
```
alter database recover managed standby database cancel; -- 取消日志应用
alter database open read only;
```

 
### 3.切换回管理恢复模式
```
startup nomount;
alter database mount standby database;
alter database open read only;
alter database recover managed standby database disconnect from session;
```
-- 启动日志应用
```
alter database recover managed standby database using current logfile disconnect from session;
```
 
### 4.主库和备库之间角色切换
可以先使用下面这个命令查询一下各位的状态
```
select database_role,switchover_status from v$database;
```

#### 4.1 主库切换为备库
```
alter database commit to switchover to physical standby;

# 主库有会话连接的时候,使用下面的，如果没有，可以使用上面的
alter database commit to switchover to physical standby with session shutdown;

shutdown immediate;
startup nomount;
alter database mount standby database;
alter database recover managed standby database disconnect from session;
```

#### 4.2 从库切换为主库
```
alter database commit to switchover to primary;
shutdown immediate;
startup
alter system switch logfile;
```

### 5.备库自动使用主库传过来的日志进行恢复
```
alter database recover automatic standby database;
```

### 6.更改保护模式
```
alter database set standby database to maximize protection;
alter database set standby database to maximize availability;
alter database set standby database to maximize performancen;
```
 
### 7.取消自动恢复模式
```
alter database recover managed standby database cancel;
alter database recover managed standby database finish;
alter database recover managed standby database finish force;
```

### 8.查询同步状况
其中如果apply lag对应的value大于0，那么就需要注意检查是否同步正常。如果相差时间非常多，value值等于几分钟，甚至达到1个小时，那可能实时同步有问题，需要检查 alert log 文件
```
select to_char(SYSDATE,'yyyymmdd hh24:mi:ss') CTIME,NAME,VALUE,DATUM_TIME from V$DATAGUARD_STATS  WHERE NAME LIKE '%lag';
```

