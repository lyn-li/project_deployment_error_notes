#CentOS7.1 KVM虚拟化之虚拟机内存、CPU调整

##一、调小虚拟机内存
调小虚拟机内存可以动态实现，不用关机

###1.查看当前内存大小

[root@kvm01 ~]# virsh dominfo vm1-clone | grep memory
Max memory:     786432 KiB
Used memory:    786432 KiB
###2.设置虚拟机内存大小为512MB
[root@kvm01 ~]# virsh setmem vm1-clone 524288
###3.再次查看当前内存大小
[root@kvm01 ~]# virsh dominfo vm1-clone | grep memory
Max memory:     786432 KiB
Used memory:    524288 KiB
