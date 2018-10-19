# NCEPU-deployment

## 遇到的问题

### 1.

### 2.

### 3.1个服务器自检失败，该服务器启动顺序不对，网卡启动应设为第一启动顺序

原因：交换机bond模式没有配置动态链路聚合，华为交换机默认模式是mode manual

### 4.手动创建osd，ceph集群数据始终无法同步

![](/assets/ceph-s.png)

### 5.一个节点的域名是help

手动修改域名：hostnamectl set-hostname --transient overcloud-controller-1.localdomain  
但是出现了另外一个问题，问题6

### 6.rabbitmq因为主机名更改导致连接不上

\[root@overcloud-controller-1 ~\]\# rabbitmqctl cluster\_status  
Cluster status of node rabbit@help ...  
Error: unable to connect to node rabbit@help: nodedown

# DIAGNOSTICS

attempted to contact: \[rabbit@help\]

rabbit@help:

* unable to connect to epmd \(port 4369\) on help: nxdomain \(non-existing domain\)

current node details:

* node name: 'rabbitmq-cli-90@overcloud-controller-1'
* home dir: /var/lib/rabbitmq
* cookie hash: yq6dFC+bFxNqFzer4fN+GA==

**应该直接重启服务器就可以了。（具体可不可以没有尝试过）**

## 学习

### 1.ceph相关

ceph health detail

### 2.手动创建osd

```
ceph-disk prepare /dev/sdb
ceph-disk activate /dev/sdb1
ceph-disk list
```

### 3.抓包

tcpdump -envi vnet0 icmp or arp

### 4.断点续传

rsync --delete -aPL ${RSYNC\_URL} ${REPO\_CACHE\_DIR}

rsync --delete -aPL /opt/repos/production /mnt/sync\_repo\_dir1/repos

rsync -aP ${REPO\_CACHE\_DIR} ${mount\_point}


### 5.vim /etc/clustershell/groups
ctl: overcloud-controller-[0-2]
comp: overcloud-novacompute-[0-11]
bigdata: overcloud-bigdata-[0-5]
all: @ctl,@comp,@bigdata

## 注意事项

### 1.role文件controller.yaml中竟然没有配置：- OS::TripleO::Services::CephOSD，所以ceph集群中一个osd都没有

### 2.1804镜像太慢

### 3.ceontos-release的包不是最新，在openstack undercloud install的时候/etc/yum.repos.d/中刷出了原始源，导致undercloud安装失败

### 4.undercloud虚机重启之后网络被cloud-init刷回去了，需要卸掉cloud-init

### 5.种子节点关闭NetworkManager，因为有可能ovs起不来



