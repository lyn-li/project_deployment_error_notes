# gw-hlj-deployment

##1.访问agent.ramdisk

https://docs.openstack.org/tripleo-docs/latest/install/troubleshooting/troubleshooting-nodes.html#node-registration-problems

https://docs.openstack.org/ironic-python-agent/latest/admin/troubleshooting.html#where-can-i-find-the-ipa-logs

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

##2.ipa-api-url报错

先给ramdisk注入密码，默认的ramdisk没有密码，不然不能登录系统查看相关信息
采用openssl passwd -1 123456 生成密码HASH值
```
[root@docker ~]# openssl passwd -1 123456  # -1 是数字1，不是字母l
$1$EjlltJoH$kn6mNHbE7rZJFoXOnb4H40

编辑inspector.ipxe文件，同时添加ipa-appi-url

cd /httpboot/
vi inspector.ipxe 
```

##3.step2报错

重新跑一遍deploy.sh脚本
可能是因为mon服务还没起来就开始执行import keyring

##4.step3报错

![](/assets/step3.jpeg)

/dev/sdo硬盘不存在
/dev/sdf是系统盘，不能对其进行操作

修改uos-storage.yaml

##5.扩容失败
原因：4台服务器万兆网卡不稳定

```
ironic node-list
ironic node-set-maintenance <UUID> <false|true>
ironic node-set-provision-state <UUID> manage
ironic node-set-power-state <UUID> <on|off>
```
##6.
想重新部署机器，用RemovalPolicy：会先移除指定的nova，然后在heat里删掉nova instance，最后把baremetal里设置为available
```
在cloudname.yaml中添加一行：
ComputeRemovalPolicies: [{'resource_list': ['0', '1', '2']}]


```
