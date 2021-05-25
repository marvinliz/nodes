# 如何跟主机网络互通

​	网上找了很多资料都不行，最后通过bilibili上的[视频](https://www.bilibili.com/video/BV1rT4y137vD)解决了。

​	1.需要在virtualBox设置网络中配置NAT和Only-host，分别记录对应的MAC地址。虚拟机通过连接该网卡获得访问主机和外网的能力。Only-host网卡是用来主机访问虚拟机的，具体如何实现的不清楚。

​	2.进入centos

```shell
vi /etc/sysconfig/network-scripts/ifcfg-${NAT网卡名}
# ONBOOT=yes 开机启动
# HWADDR=MAC地址
vi /etc/sysconfig/network-scripts/ifcfg-${Only-host网卡名}
# ONBOOT=yes 开机启动
# HWADDR=MAC地址
# BOOTPROTO=static 手动配置IP地址
# IPADDR=192.168.56.101
# NETMARK=255.255.255.0

systemctl restart network
```



