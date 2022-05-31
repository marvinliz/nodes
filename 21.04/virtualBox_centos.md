# 如何跟主机网络互通

​	网上找了很多资料都不行，最后通过bilibili上的[视频](https://www.bilibili.com/video/BV1rT4y137vD)解决了。

​	1.需要在virtualBox设置网络中配置NAT和Only-host，分别记录对应的MAC地址。虚拟机通过连接该网卡获得访问主机和外网的能力。Only-host网卡是用来主机访问虚拟机的，具体如何实现的不清楚。

​	2.进入centos

```shell
cd /etc/sysconfig/network-scripts
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
# ONBOOT=yes 开机启动
# HWADDR=MAC地址(08:00:27:A7:66:31)
cp ifcfg-enp0s3 ifcfg-enp0s8
vi ifcfg-enp0s8
# NAME=enp0s8
# DEVICE=enp0s8
# HWADDR=MAC地址
# BOOTPROTO=static 手动配置IP地址
# IPADDR=192.168.56.101
# NETMARK=255.255.255.0

systemctl restart network
```



