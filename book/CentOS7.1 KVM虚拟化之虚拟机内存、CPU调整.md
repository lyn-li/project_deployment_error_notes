#CentOS7.1 KVM虚拟化之虚拟机内存、CPU调整

##一、调小虚拟机内存
调小虚拟机内存可以动态实现，不用关机

###1.查看当前内存大小
```
[root@kvm01 ~]# virsh dominfo vm1-clone | grep memory
Max memory:     786432 KiB
Used memory:    786432 KiB
```
###2.设置虚拟机内存大小为512MB
```
[root@kvm01 ~]# virsh setmem vm1-clone 524288
```
###3.再次查看当前内存大小
```
[root@kvm01 ~]# virsh dominfo vm1-clone | grep memory
Max memory:     786432 KiB
Used memory:    524288 KiB
```

##二、增大虚拟机内存、增加虚拟机CPU个数
增大虚拟机内存、增加虚拟机CPU个数需要首先关机虚拟机

###1.关闭虚拟机
```
virsh shutdown vm1-clone
```
###2.编辑虚拟机配置文件
修改内存大小、vcpu个数
```
virsh edit vm1-clone
......
  <memory unit='KiB'>1048432</memory>
  <currentMemory unit='KiB'>1048432</currentMemory>
  <vcpu placement='static'>2</vcpu>
......
```
###3.从配置文件启动虚拟机
```
virsh create /etc/libvirt/qemu/vm1-clone.xml
```
###4.查看当前内存大小
```
[root@kvm01 ~]# virsh dominfo vm1-clone | grep memory
Max memory:     1048432 KiB
Used memory:    524288 KiB
```
###5.设置虚拟机内存大小为1G
```
[root@kvm01 ~]# virsh setmem vm1-clone 1048432
```
###6.验证
查看当前内存大小
```
[root@kvm01 ~]# virsh dominfo vm1-clone | grep memory
Max memory:     1048432 KiB
Used memory:    1048432 KiB
```
