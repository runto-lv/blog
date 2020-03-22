# Runto's blog  Docs
Welcome to Runto's Blog

---

这个是Runto的blog Doc,基于Hugo,内容模板基于Rainbond的Doc



### 使用方法：

在本机使用

#### 1.先安装nginx

---

```
yum -y install nginx
systemctl enable nginx && systemctl  enable nginx
```



#### 2.配置hugo

---

```shell
wget https://github.com/gohugoio/hugo/releases/download/v0.54.0/hugo_0.54.0_Linux-64bit.tar.gz
tar -zxvf hugo_0.54.0_Linux-64bit.tar.gz -C /usr/local/bin/
rm -rf hugo_0.54.0_Linux-64bit.tar.gz
```



#### 3.配置Blog

---

clone项目

```
cd /usr/local/nginx
git clone https://github.com/runto-lv/blog-doc.git
```



修改nginx配置文件，网站根目录为`/usr/local/nginx/blog-doc/public`，并重启nginx服务

```
systemctl restart nginx
```



#### 4.使用hugo生成静态站点

---

```
hugo --baseURL=https://www.runto.lv
```

