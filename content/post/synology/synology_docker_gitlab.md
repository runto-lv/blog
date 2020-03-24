+++
title = "群晖NAS上面使用Docker运行gitlab"
description = "群晖NAS上面使用Docker运行gitlab"
tags = [
    "synology",
    "docker",
    "gitlab",
    "linux",
    "NAS"
]
date = "2019-05-04"
categories = [
        "synology"
]

+++
在群晖NAS上面可以通过下面的命令快速创建一个gitlab

```
 docker run -d  \
 -p 8443:443 \
 -p 8080:80 \
 -p 222:22 \
 --name gitlab \
 --restart always \
 -v /volume1/docker/Gitlab/config:/etc/gitlab \
 -v /volume1/docker/Gitlab/logs:/var/log/gitlab \
 -v /volume1/docker/Gitlab/data:/var/opt/gitlab \
 gitlab/gitlab-ce
```

 打开登陆界面 ，会要求修改密码，

 修改密码过后就可以登陆了，用户名为root,密码为修改过的密码
