# gw-hlj-deployment

##1.访问agent.ramdisk

https://docs.openstack.org/tripleo-docs/latest/install/troubleshooting/troubleshooting-nodes.html#node-registration-problems

https://docs.openstack.org/ironic-python-agent/latest/admin/troubleshooting.html#where-can-i-find-the-ipa-logs

采用openssl passwd -1 123456 生成密码HASH值

、、、

[root@docker ~]# openssl passwd -1 123456


$1$EjlltJoH$kn6mNHbE7rZJFoXOnb4H40
、、、
编辑：

cd /httpboot/

vi inspector.ipxe

![](/images/inspector.png)

##2.ipa-api-url报错

先给ramdisk注入密码，默认的ramdisk没有密码，不然不能登录系统查看相关信息
采用openssl passwd -1 123456 生成密码HASH值

[root@docker ~]# openssl passwd -1 123456  # -1 是数字1，不是字母l
$1$EjlltJoH$kn6mNHbE7rZJFoXOnb4H40
编辑inspector.ipxe文件，同时添加ipa-appi-url

cd /httpboot/
vi inspector.ipxe 

##3.step2报错

重新跑一遍deploy.sh脚本

##4.step3报错

