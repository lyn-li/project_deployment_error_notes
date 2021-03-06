# NCEPU-deployment

## 遇到的问题

### 1.

### 2.1个服务器自检失败，该服务器启动顺序不对，网卡启动应设为第一启动顺序

## 学习

### 1.vim /etc/clustershell/groups

ctl: overcloud-controller-\[0-2\]  
comp: overcloud-novacompute-\[0-11\]  
bigdata: overcloud-bigdata-\[0-5\]  
all: @ctl,@comp,@bigdata

### 2.fping -s 10.112.93.32 10.112.93.89

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

### 3.实施关键结果确认：

```
1. 虚拟机的MTU确保是1500
2. Ceph的Crushmap确保是跨机柜三副本
3. Ceph pool 指向正确的crush ruleset
4. Cinder多后端配置正确
```



## 信息

container-1-1  --&gt;  overcloud-novacompute-6  
container-2-1  --&gt;  overcloud-novacompute-3  
container-3-1  --&gt;  overcloud-novacompute-9  
container-1-2  --&gt;  overcloud-novacompute-2  
container-2-2  --&gt;  overcloud-novacompute-8  
container-3-2  --&gt;  overcloud-novacompute-7

for node in `openstack baremetal node list -c Name -f value | grep control`; \  
do wwn=`cat $node.json | jq .inventory.disks[3].wwn_with_extension`; \  
openstack baremetal node set --property root\_device='{"wwn\_with\_extension": '"$wwn}" $node; done

