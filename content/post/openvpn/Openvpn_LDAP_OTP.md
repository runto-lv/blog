+++
title = "企业中Docker+Openvpn+LDAP+OTP快速搭建VPN"
description = "企业中Docker+Openvpn+LDAP+OTP快速搭建VPN"
tags = [
    "docker",
    "openvpn",
    "otp",
    "ldap"
]
date = "2019-05-01"
categories = [
        "openvpn"
]

+++

只需要修改下面几个参数即可

- `OVPN_SERVER_CN`是外部的地址,比如公司对外开放的公网IP或是域名
- `LDAP_URI`是写AD服务器的地址
- `LDAP_BASE_DN`是自己的搜索用户的范围
- `LDAP_BIND_USER_DN`这个是一个普通用户的Base dn路径,可以在域控用户属性编辑器中查到,这个普通 用户就可以了,不要使用管理员
- `LDAP_BIND_USER_PASS`上面用户的密码

```
docker run \
--name openvpn \
--volume /home/openvpn:/etc/openvpn \
-v /etc/localtime:/etc/localtime:ro \
--detach=true \
--restart=always  \
-p 1194:1194/udp \
-e "OVPN_PROTOCOL=udp" \
-e "OVPN_SERVER_CN=vpn.runto.lv" \
-e "LDAP_URI=ldap://10.0.10.20" \
-e "LDAP_BASE_DN=dc=runto,dc=lv" \
-e "LDAP_BIND_USER_DN=cn=ad,cn=Users,dc=runto,dc=lv" \
-e "LDAP_BIND_USER_PASS=P@ssw0rd" \
-e "ACTIVE_DIRECTORY_COMPAT_MODE=true" \
-e "ENABLE_OTP=true" \
-e "DEBUG=true" \
--cap-add=NET_ADMIN \
wheelybird/openvpn-ldap-otp
```





