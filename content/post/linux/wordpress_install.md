+++
title = "Centos7上安装Wordpress"
description = "Wordpress的安装步骤"
tags = [
    "wordpress",
    "linux",
    "nginx",
    "php",
    "mysql",
]
date = "2019-11-13"
categories = [
        "linux"
]
+++
### 1.安装mysql
yum -y install mariadb-server
修改配置文件my.cnf,mysqld段添加下面的内容
```
character-set-server=utf8
```
启动mysql 
```
systemctl enable mariadb && systemctl start mariadb
```

设置mysql,并设置root密码
```
mysql_secure_installation
```

创建wordpress数据库

```
mysql -u root -p

SQL> create database wordpress;
SQL> grant all privileges on wordpress.* to wp@'localhost' identified by 'R*****F';
```

### 2.安装php php-fpm

```
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum -y install php74 php74-php-fpm php74-php-pecl-mysql
systemctl enable php74-php-fpm && systemctl start php74-php-fpm

```
修改下面这个配置文件的内容,否则无法下载插件,会弹出FTP的窗口

```
vim /etc/opt/remi/php74/php-fpm.d/www.conf
```
把`user = apache`修改成`user = nginx`

> **参考这个网址**:https://blog.csdn.net/c332030/article/details/89117206

### 3.安装wordpress

```
cd /usr/share/nginx
wget https://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
```

创建wordpressp配置文件，从 simple文件复制

```
cd wordpress
cp  wp-config-sample.php wp-config.php
--------------------------------------
define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'wp' );

/** MySQL database password */
define( 'DB_PASSWORD', 'R*****F' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
```


### 4.安装nginx

```
yum -y install nginx
```


nginx需要修改下面的字段

```
root         /usr/share/nginx/wordpress;
```


```
location / {
            index  index.php;
        }
```

新增下面这段
```
        location ~ .php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
```
配置nginx站点目录权限
```
chown nginx.nginx /usr/share/nginx/wordpress -R
```

重启相关服务
```
systemctl restart nginx && systemctl restart php74-php-fpm
```

### 5.登陆图形界面配置
访问下面的网址可以访问 
```
http://IP地址
```
创建用户*****,密码*****



