+++
title = "Hyper-v下安装minikube"
description = "Hyper-v下安装minikube"
tags = [
    "Hyper-v",
    "Windows",
    "Kubernetes",
    "Minikube"
]
date = "2019-10-01"
categories = [
        "kubernetes"
]

+++
> 本文参考网址：https://yq.aliyun.com/articles/221687

### 1. 打开powershell安装hyper-v的所有组件
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
### 2.打开hyper-v管理控制台，新建一个外部的网络，映射到一个物理网卡，虚拟交换机的名称，这里使用`Bridge`

### 3.下载 minikube-windows-amd64.exe 文件，并重命名为 `minikube.exe`
下载地址
```
https://github.com/kubernetes/minikube/releases
```


### 4.执行创建hyper-v

以管理员身份打开powershell过后执行下面的命令
```
 minikube.exe start --image-mirror-country cn --iso-url=https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/iso/minikube-v1.6.0.iso    --registry-mirror=https://ho0aa4tw.mirror.aliyuncs.com     --vm-driver="hyperv"    --hyperv-virtual-switch="Bridge"     --memory=4096 --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers
```
> 上面这个minikube的最新版本是我写这个文档 时的最新版本，如果要更新的，需要去下载

上面这个文档里面，是创建一个虚拟机，使用上面的镜像的信息安装一台k8s


安装的过程可能会有文件下载不下来的情况，需要重新下载，已经下载完成的不要删除，再次下载的时候，已经下载的不会再下载，但虚拟机要删除历史文件，删除此文件夹`C:\Users\runto.lv\.minikube`


下载文件的保存路径在下面
```
C:\Users\runto.lv\.minikube\cache\images\registry.cn-hangzhou.aliyuncs.com\google_containers
```

下载kubuctl
```
https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/windows/amd64/kubectl.exe
```

配置环境变量，不然minicube dashboard这个命令不能运行,过程省略


### 5.本机文件复制复制到虚拟机
先在hyper-v里面这台虚拟机的网卡上面，开启来宾服务，然后就可以把文件复制到虚拟机里面
```
Copy-VMFile -Name minikube -SourcePath 'C:\Users\runto.lv\.minikube\cache\v1.17.2\kubeadm' -DestinationPath '/bin' -FileSource Host

Copy-VMFile -Name minikube -SourcePath 'C:\Users\runto.lv\.minikube\cache\v1.17.2\kubelet' -DestinationPath '/bin' -FileSource Host
```
给执行权限
```
minikube -p minikube ssh "sudo chmod 755 /bin/kubelet"

minikube -p minikube ssh "sudo chmod 755 /bin/kubeadm"
```