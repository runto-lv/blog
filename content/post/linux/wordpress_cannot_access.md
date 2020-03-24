+++
title = "Wordpress主题安装失败无法访问的解决办法"
description = "Wordpress主题安装失败无法访问的解决办法"
tags = [
    "wordpress"
]
date = "2019-11-14"
categories = [
        "linux"
]
+++
我们在wordpress主题theme配置的时候，会从网站上下载比较流行的theme，有不顺利的时候，你下载的theme有bug或者下载包出问题了，安装过后你的web页面不能在访问了。悲催，想通过web将theme改回去也不行。我们只能够通过后台数据库来手动修改theme了。

---
### 1.先登陆数据库
```
[root@wordpress themes]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 1982
Server version: 5.5.64-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use wordpress
MariaDB [wordpress]> SELECT *
    -> FROM wp_options
    -> WHERE option_name = 'template'
    -> OR option_name = 'stylesheet'
    -> OR option_name = 'current_theme';
+-----------+---------------+--------------+----------+
| option_id | option_name   | option_value | autoload |
+-----------+---------------+--------------+----------+
|       160 | current_theme | Jenney       | yes      |
|        41 | stylesheet    | jenney       | yes      |
|        40 | template      | jenney       | yes      |
+-----------+---------------+--------------+----------+

```
从上面看到,当前使用了一个有问题的'jenney'的主题

### 2.修改数据库
在`/usr/share/nginx/wordpress/wp-content/themes`目录,找到一个已经存在的主题,这里就选`win10explore`,然后执行下面的命令,更新数据库
```
UPDATE wp_options SET option_value = 'win10explore' WHERE option_name = 'template';
 
UPDATE wp_options SET option_value = 'win10explore' WHERE option_name = 'stylesheet';
 
UPDATE wp_options SET option_value = 'win10explore' WHERE option_name = 'current_theme';
```

具体执行结果如下
```
MariaDB [wordpress]> UPDATE wp_options SET option_value = 'win10explore' WHERE option_name = 'template';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [wordpress]>  
MariaDB [wordpress]> UPDATE wp_options SET option_value = 'win10explore' WHERE option_name = 'stylesheet';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [wordpress]>  
MariaDB [wordpress]> UPDATE wp_options SET option_value = 'win10explore' WHERE option_name = 'current_theme';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

