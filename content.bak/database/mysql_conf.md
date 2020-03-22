---
title: mysql双主模式两台Server配置文件
describe: mysql双主模式配置文件
weight: 901
---

mysql master
```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd
server-id=2
relay_log = mysql-relay-bin
log_bin =mysql-bin
log_slave_updates =1
auto_increment_increment=2
auto_increment_offset=2
character-set-server=utf8
lower_case_table_names=1

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d


```
mysql slave
```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd
server-id = 1
log-bin = binlog
relay_log = mysql-relay-bin
log_slave_updates =1
auto_increment_increment=2
auto_increment_offset=1
character-set-server=utf8
lower_case_table_names=1

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d

```
