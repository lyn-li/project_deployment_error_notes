# mj-bj-deployment

## 遇到的问题

### 1.自检一直在刷LLDP
解决办法：交换机开启LLDP

## 学习

### 1.controller节点的网关起在external上，其他节点的网关都起在pxe上

### 2.awk
nova list | awk '{print $12}'
虚线也算一列

### 3.vim批量操作技巧
ctrl+v进入可视块模式
批量插入：按G到文件最后一行，I-行首插入，A-行尾插入
批量删除：选中需要删除的字段，按D

### 4.Linux下如何查看版本信息， 包括位数、版本信息以及CPU内核信息、CPU具体型号等等，整个CPU信息一目了然
#### 1.# uname －a   （Linux查看版本当前操作系统内核信息）

#### 2.# cat /proc/version （Linux查看当前操作系统版本信息）

#### 3.# cat /etc/issue  或cat /etc/redhat-release（Linux查看版本当前操作系统发行版信息）

#### 4.# cat /proc/cpuinfo （Linux查看cpu相关信息，包括型号、主频、内核信息等）

#### 5.# getconf LONG_BIT  （Linux查看版本说明当前CPU运行在32bit模式下， 但不代表CPU不支持64bit）

#### 6.# lsb_release -a

## 注意事项
### 1.设置undercloud的软件源的时候是在stack用户下操作

### 2.opsserver虚机用1611的镜像