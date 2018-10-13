# mj-bj-deployment

## 遇到的问题

### 1.自检一直在刷LLDP

解决办法：交换机开启LLDP

### 2.西安poc项目step3报错
![](/assets/step3_error.png)
原因：交换机没有配置巨型帧（mtu 9000），导致超时。

## 学习

### 1.controller节点的网关起在external上，其他节点的网关都起在pxe上

### 2.awk

```
nova list | awk '{print $12}'
虚线也算一列
```

### 3.vim批量操作技巧

```
ctrl+v进入可视块模式
批量插入：按G到文件最后一行，I-行首插入，A-行尾插入
批量删除：选中需要删除的字段，按d删除选中的字段，按D删除选中字段至行尾
```

### 4.Linux下如何查看版本信息，包括位数、版本信息以及CPU内核信息、CPU具体型号等等，整个CPU信息一目了然

```
#### 1.# uname －a   （Linux查看版本当前操作系统内核信息）

#### 2.# cat /proc/version （Linux查看当前操作系统版本信息）

#### 3.# cat /etc/issue  或cat /etc/redhat-release（Linux查看版本当前操作系统发行版信息）

#### 4.# cat /proc/cpuinfo （Linux查看cpu相关信息，包括型号、主频、内核信息等）

#### 5.# getconf LONG_BIT  （Linux查看版本说明当前CPU运行在32bit模式下， 但不代表CPU不支持64bit）

#### 6.# lsb_release -a  （查询系统版本）
```

### 5.VNC登录映射公网地址而非内网地址

登录计算节点

```
[root@overcloud-novacompute-1 ~]# vim /etc/nova/nova.conf
[vnc]
novncproxy_base_url=http://218.25.208.4:6080/vnc_auto.html
```

重启openstack-nova-compute服务

### 6.查看baremetal中的各种信息

```
查看网卡信息
[stack@undercloud baremetal_info]$ cat compute-0.json | jq '.all_interfaces' 

查看哪些网卡是已经插线的网卡
[stack@undercloud baremetal_info]$ cat compute-0.json | jq '.extra.network|.[] | select(.link=="yes").serial'

查看system-product-name
[stack@undercloud baremetal_info]$ cat compute-0.json | jq .inventory.system_vendor.product_name

查看服务器主机板号
[stack@undercloud baremetal_info]$ cat compute-0.json | jq .extra.system.product.uuid

查看磁盘信息
[stack@undercloud baremetal_info]$ cat compute-0.json | jq .inventory.disks
```

### 7.mtu的配置

交换机上pxe和ipmi对应的网卡不需要配置mtu 9000，其他的网卡需要配置

### 8.admin密码的修改
有两种方法：
1.直接修改kunkka和overcloudrc的密码（此方法不能用于生产环境）
* 1.修改三台控制节点/opt/kunkka/configs/server.json:  "admin_password":
* 2.重启kunkka服务：systemctl restart kunkka
* 3.修改overcloudrc文件中的密码

2.直接在模版文件中添加（此方法适用于生产环境）
* 1.vim /home/stack/templates/environments/cloudname.yaml
添加：
```
parameter_defaults:
  AdminPassword: "uMHNCDeNa8eezmwY3kJZwQuyr.2"
```
* 2.重跑deploy脚本

## 注意事项

### 1.设置undercloud的软件源的时候是在stack用户下操作

### 2.opsserver虚机用1611的镜像

### 3.跑监控脚本一直卡在zabbix_agent/install zabbix_plugin这个task
$ sudo vim roles/zabbix-agent/tasks/main.yaml
在- name: Install zabbix-plugin
  yum: name=zabbix_plugin state=latest下面加上一行：
tags: install_zabbix_plugin

然后重新运行ansible脚本
$ ansible-playbook playbook-opsserver.yml -s --skip-tags=install_zabbix_plugin



