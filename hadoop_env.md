# 1.vmware搭建3个虚拟机群
node01
node01
node01

## 1.1配置mac地址
使用vmware适配器生成

## 1.2配置ipv4
1. 网关 192.168.181.2
2. 三台机器处于同一网段

## 1.3更改hostname
ubuntu系统文件位置 /etc/hostname 

## 1.4host与域名映射
* ubuntu系统文件位置 /etc/hosts 
	* 192.168.181.100 node01 node01.hadoop.com
	* 192.168.181.110 node02 node02.hadoop.com
	* 192.168.181.120 node03 node03.hadoop.com

## 1.5 关闭防火墙和selinux内核
1.5.1 防火墙在生产环境不关闭，学习阶段关闭

**关闭防火墙**

`sudo ufw disable` 关闭防火墙

1.5.2 selinux时Linux的内核，可针对进程进行权限管理，linux是针对于文件。如果开启selinux将需要做非常复杂的配置，学习阶段，非生产环境一般不使用。

**关闭selinx**

输入以下命令打开`/etc/selinux/config`配置文件。

`sudo gedit /etc/selinux/config`

修改/etc/selinux/config文件中设置为：`SELINUX=disabled`

## 1.6 ssh免密登录



