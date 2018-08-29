#本地服务方式搭建etcd集群
##IP功能
172.18.6.101 etcd node 
172.18.6.102 etcd node 
172.18.6.103 etcd node 
172.18.6.104 etcd proxy 
172.18.6.105 etcd proxy 
172.18.6.106 etcd proxy

##下载安装etcd二进制文件

以下命令将下载etcd发布版本v3.0.15，并解压后安装至宿主机的/usr/bin目录下，并验证
```
wget https://github.com/coreos/etcd/releases/download/v3.0.15/etcd-v3.0.15-linux-amd64.tar.gz && tar -vzxf etcd-v3.0.15-linux-amd64.tar.gz && cp etcd-v3.0.15-linux-amd64/etcd* /usr/bin/ && etcdctl --help
```

分别在需要部署的宿主机中执行如上命令安装etcd与etcdctl两个工具

##集群节点部署
###172.18.6.101 etcd0

在/etc/systemd/system/目录里创建etcd2.service，其内容如下：
```
[Unit]
Description=etcd2.service
[Service]
Type=notify
TimeoutStartSec=0
Restart=always
ExecStartPre=-/bin/mkdir -p /data/etcd2
ExecStart=/usr/bin/etcd \
  --data-dir /data/etcd2 \
  --name etcd0 \
  --advertise-client-urls http://172.18.6.101:2379,http://172.18.6.101:4001 \
  --listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  --initial-advertise-peer-urls http://172.18.6.101:2380 \
  --listen-peer-urls http://0.0.0.0:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster etcd0=http://172.18.6.101:2380,etcd1=http://172.18.6.102:2380,etcd2=http://172.18.6.103:2380
[Install]
WantedBy=multi-user.target
```
执行如下命令:

**设置服务自启动**
systemctl enable /etc/systemd/system/etcd2.service
**启动etcd2**
systemctl restart etcd2.service

###172.18.6.102 etcd1

在/etc/systemd/system/目录里创建etcd2.service，其内容如下：
```
[Unit]
Description=etcd2.service
[Service]
Type=notify
TimeoutStartSec=0
Restart=always
ExecStartPre=-/bin/mkdir -p /data/etcd2
ExecStart=/usr/bin/etcd \
  --data-dir /data/etcd2 \
  --name etcd1 \
  --advertise-client-urls http://172.18.6.102:2379,http://172.18.6.102:4001 \
  --listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  --initial-advertise-peer-urls http://172.18.6.102:2380 \
  --listen-peer-urls http://0.0.0.0:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster etcd0=http://172.18.6.101:2380,etcd1=http://172.18.6.102:2380,etcd2=http://172.18.6.103:2380
[Install]
WantedBy=multi-user.target
```
执行如下命令:

**设置服务自启动**
systemctl enable /etc/systemd/system/etcd2.service
**启动etcd2**
systemctl restart etcd2.service

###172.18.6.103 etcd2

在/etc/systemd/system/目录里创建etcd2.service，其内容如下：
```
[Unit]
Description=etcd2.service
[Service]
Type=notify
TimeoutStartSec=0
Restart=always
ExecStartPre=-/bin/mkdir -p /data/etcd2
ExecStart=/usr/bin/etcd \
  --data-dir /data/etcd2 \
  --name etcd2 \
  --advertise-client-urls http://172.18.6.103:2379,http://172.18.6.103:4001 \
  --listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  --initial-advertise-peer-urls http://172.18.6.103:2380 \
  --listen-peer-urls http://0.0.0.0:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-cluster etcd0=http://172.18.6.101:2380,etcd1=http://172.18.6.102:2380,etcd2=http://172.18.6.103:2380
[Install]
WantedBy=multi-user.target
```
执行如下命令:

**设置服务自启动**
systemctl enable /etc/systemd/system/etcd2.service
**启动etcd2**
systemctl restart etcd2.service

##集群检查

**在以上三个节点任意一个节点上执行如下命令，输出如下所示表示正常。**
```
$ etcdctl member list
e160d83780b9342: name=etcd1 peerURLs=http://172.18.6.102:2380 clientURLs=http://172.18.6.102:2379,http://172.18.6.102:4001 isLeader=false
2cb830c4198c7098: name=etcd2 peerURLs=http://172.18.6.103:2380 clientURLs=http://172.18.6.103:2379,http://172.18.6.103:4001 isLeader=false
717e65599f151c45: name=etcd0 peerURLs=http://172.18.6.101:2380 clientURLs=http://172.18.6.101:2379,http://172.18.6.101:4001 isLeader=true
```
##代理节点部署

###172.18.6.104/172.18.6.105/172.18.6.106 proxy

在/etc/systemd/system/目录里创建etcd2.service，其内容如下：
```
[Unit]
Description=etcd2.service
[Service]
Type=notify
ExecStartPre=-/bin/mkdir -p /data/etcd2
ExecStart=/usr/bin/etcd \
  --proxy on \
  --listen-client-urls http://0.0.0.0:2379,http://0.0.0.0:4001 \
  --initial-cluster etcd0=http://172.18.6.101:2380,etcd1=http://172.18.6.102:2380,etcd2=http://172.18.6.103:2380 \
  --data-dir /data/etcd2
[Install]
WantedBy=multi-user.target
```
执行如下命令:

**设置服务自启动**
systemctl enable /etc/systemd/system/etcd2.service
**启动etcd2**
systemctl restart etcd2.service

##测试代理

在172.18.6.103或172.18.6.104或172.18.6.105上执行如下命令

**输出如下所示表示正常。**
```
$ etcdctl member list
$ etcdctl member list
e160d83780b9342: name=etcd1 peerURLs=http://172.18.6.102:2380 clientURLs=http://172.18.6.102:2379,http://172.18.6.102:4001 isLeader=false
2cb830c4198c7098: name=etcd2 peerURLs=http://172.18.6.103:2380 clientURLs=http://172.18.6.103:2379,http://172.18.6.103:4001 isLeader=false
717e65599f151c45: name=etcd0 peerURLs=http://172.18.6.101:2380 clientURLs=http://172.18.6.101:2379,http://172.18.6.101:4001 isLeader=true
```
至此，etcd集群与代理节点搭建完成，每个节点或者代理的数据都放置在宿主机的/data/etcd目录下