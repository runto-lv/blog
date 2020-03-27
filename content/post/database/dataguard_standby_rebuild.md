+++
title = "ORACLE DATAGUARD重建备库"
description = "ORACLE DATAGUARD重建备库"
tags = [
    "dataguard",
    "oracle",
    "database"
]
date = "2020-03-26"
categories = [
        "Database"
]
+++
# 重建步骤
## 1、删除备库所有的数据文件、日志文件、控制文件

```
/u01/app/oracle/oradata/orcl
rm -rf *
```

进入闪回区，删除control02.ctl

```
cd /u01/app/oracle/flash_recovery_area
rm -rf control02.ctl
```


## 2、备份备库的参数文件和密码文件

```
cd $ORACLE_HOME/dbs
mv spfileorcl.ora spfileorcl.ora.bak
cp orapworcl orapworcl.bak
```


## 3、进入主库连接辅库

```
SQL> create spfile from pfile='/tmp/initorcl.ora';
SQL> startup nomount;
```

```
$ rman target sys/yH.20200316@orcldg auxiliary sys/yH.20200316@orclpr
```



## 4、复制主库数据文件、日志文件、控制文件、密码文件、参数文件到备库

```
RMAN> duplicate target database for standby from active database nofilenamecheck;
```

## 5、打开备库

```
alter database open read only;
```

## 6、应用日志

```
alter database recover managed standby database using current logfile disconnect from session;
```

## 7、检查日志应用状态

```
select sequence#,applied from v$archived_log;
```

## 8、分别查看主库和备库的归档序列号是否一致：
主库上面执行
```
SQL> alter system switch logfile;
```

然后再主备上面都执行下面这行命令
```
archive log list;
```

执行结果:

主:
```
SQL> archive log list;
Database log mode	       Archive Mode
Automatic archival	       Enabled
Archive destination	       /u01/app/oracle/oradata/orcl/archivelog
Oldest online log sequence     17
Next log sequence to archive   19
Current log sequence	       19
```

备:
```
SQL> archive log list;
Database log mode	       Archive Mode
Automatic archival	       Enabled
Archive destination	       /u01/app/oracle/oradata/orcl/archivelog
Oldest online log sequence     17
Next log sequence to archive   0
Current log sequence	       19
SQL> 
```
