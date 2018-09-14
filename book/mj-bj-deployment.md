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

## 注意事项
### 1.设置undercloud的软件源的时候是在stack用户下操作