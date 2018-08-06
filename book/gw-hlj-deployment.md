~~~~~~~~\# gw-hlj-deployment

## 1.访问agent.ramdisk

[https://docs.openstack.org/tripleo-docs/latest/install/troubleshooting/troubleshooting-nodes.html\#node-registration-problems](https://docs.openstack.org/tripleo-docs/latest/install/troubleshooting/troubleshooting-nodes.html#node-registration-problems)

[https://docs.openstack.org/ironic-python-agent/latest/admin/troubleshooting.html\#where-can-i-find-the-ipa-logs](https://docs.openstack.org/ironic-python-agent/latest/admin/troubleshooting.html#where-can-i-find-the-ipa-logs)

采用openssl passwd -1 123456 生成密码HASH值

```
[root@docker ~]# openssl passwd -1 123456
$1$EjlltJoH$kn6mNHbE7rZJFoXOnb4H40
```

编辑：

```
cd /httpboot/
vi inspector.ipxe
```

![](/assets/inspector.png)

## 2.ipa-api-url报错

先给ramdisk注入密码，默认的ramdisk没有密码，不然不能登录系统查看相关信息  
采用openssl passwd -1 123456 生成密码HASH值

```
[root@docker ~]# openssl passwd -1 123456  # -1 是数字1，不是字母l
$1$EjlltJoH$kn6mNHbE7rZJFoXOnb4H40

编辑inspector.ipxe文件，同时添加ipa-appi-url

cd /httpboot/
vi inspector.ipxe
```

## 3.step2报错

重新跑一遍deploy.sh脚本  
可能是因为mon服务还没起来就开始执行import keyring

## 4.step3报错

![](/assets/step3.jpeg)

/dev/sdo硬盘不存在  
/dev/sdf是系统盘，不能对其进行操作

修改uos-storage.yaml

## 5.扩容失败

原因：4台服务器万兆网卡不稳定

```
ironic node-list
ironic node-set-maintenance <UUID> <false|true>
ironic node-set-provision-state <UUID> manage
ironic node-set-power-state <UUID> <on|off>
```

## 6.重新部署机器方法

想重新部署机器，用**RemovalPolicy**：会先移除指定的nova，然后在heat里删掉nova instance，最后把baremetal里设置为available。

```
在cloudname.yaml中添加一行：
ComputeRemovalPolicies: [{'resource_list': ['0', '1', '2']}]
resource list写上要删除的计算节点的index；如果是其他类型的节点，需要根据role name对应改下
```

_**Note**_

> _1.这种做法的不好之处在于index就乱了，不是按顺序增长的，是跳着涨的；_
> _2.需要跑2遍deploy脚本。第一遍先删除对应的nova，把相应的baremetal设置为available，相当于缩容；第二遍再扩容_

## 7.集群有计算高可用时遇到的问题及解决办法

```
pacemaker服务无法启动：
pcs resource cleanup --force          强制修复pacemaker
pcs property                          有计算高可用时需注意在跑deploy前要先停止计算高可用：
pcs property set                  
pcs resource debug-start rabbitmq     使用pcs重启集群服务
pcs resource restart hqproxy          使用pcs重启集群服务
```

## 8.

```
journalctl -u os-collect-config
```

## 9.

```
tar -zcvf <压缩后文件名> <需要压缩的文件或目录>
```

## 10.

hdd-volumes存储在hdd池中，其余的都存储在ssd池中

## 11.osd起不来

服务器未识别到journal盘  
进不了系统，无法识别raid盘

## 12.手动移除osd

osd正常运行是up 且 in状态

```
#out之后，ceph开始重新平衡，拷贝此osd上数据到其它osd，此osd状态变为up且out
**ceph osd out 10**

#stop osd进程之后，状态变为down 且 out
**systemctl stop ceph-osd@10**

#删除 CRUSH 图的对应 OSD 条目，它就不再接收数据了
**ceph osd crush remove osd.10**

#移除osd认证key
**ceph auth del osd.10**

#从osd中删除osd 10，ceph osd tree中移除
**ceph osd rm 10**
```



