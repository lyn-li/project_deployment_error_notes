# NCEPU-deployment

## 遇到的问题

### 1.1个服务器自检失败，该服务器启动顺序不对，网卡启动应设为第一启动顺序

原因：交换机bond模式没有配置动态链路聚合，华为交换机默认模式是mode manual


## 学习

### 1.vim /etc/clustershell/groups
ctl: overcloud-controller-[0-2]
comp: overcloud-novacompute-[0-11]
bigdata: overcloud-bigdata-[0-5]
all: @ctl,@comp,@bigdata

## 注意事项

### 1.ceontos-release的包不是最新，在openstack undercloud install的时候/etc/yum.repos.d/中刷出了原始源，导致undercloud安装失败
```
解决办法：
$sudo rm -rf /etc/yum.repos.d/*
$sudo scp root@10.3.106.19:/etc/yum.repos.d/ustack.repo /etc/yum.repos.d/ustack.repo
$sudo yum clean all && sudo yum makecache
$sudo yum update -y
```

### 2.种子节点关闭NetworkManager，因为有可能ovs起不来

## 信息

container-1-1  -->  overcloud-novacompute-6
container-2-1  -->  overcloud-novacompute-3
container-3-1  -->  overcloud-novacompute-9
container-1-2  -->  overcloud-novacompute-2
container-2-2  -->  overcloud-novacompute-8
container-3-2  -->  overcloud-novacompute-7
