# KeepAlived高可用

## 安装

```shell
[root@server1 ~]# yum install -y keepalived

[root@server1 ~]# systemctl status keepalived
keepalived.service - LVS and VRRP High Availability Monitor
Loaded: loaded (/usr/lib/systemd/system/keepalived.service; disabled; vendor preset: disab
Active: inactive (dead)
```

## 编辑配置文件

```shell
server1# cat /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 51
        priority 255
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              192.168.122.200/24
        }
}
```

```shell
server2# cat /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {
        state BACKUP
        interface eth0
        virtual_router_id 51
        priority 254
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              192.168.122.200/24
        }
}
```

参数解释：

- vrrp_instance VRRP实例名
- state 初始化状态
- interface 定义VRRP在哪个网卡上运行
- virtual_router_id 作为这个配置的唯一ID
- priority 权重 权重大的机器回复后会拥有虚拟IP
- advert_int 定义广播发送频率（例子中代表1秒1次）
- authentication 服务器间的通信认证，案例中是通过密码认证
- virtual_ipaddress 定义VRRP负责的IP地址 可以多个

## 启动

```shell
systemctl start keepalived
```

## 设置开机启动 

```shell
systemctl enable keepalived
```

## 查看效果

```shell
server1# ip -br a
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.101/24 192.168.122.200/24 fe80::5054:ff:fe82:d66e/64

server2# ip -br a
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.102/24 fe80::5054:ff:fe04:2c5d/64
```

看到server1作为master多了一个虚拟的IP地址，server2作为backup没有变化

## 停掉master的keepalived

```shell
server1# systemctl stop keepalived

server1# ip -brief address show
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.101/24 fe80::5054:ff:fe82:d66e/64

server2# ip -brief address show
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 192.168.122.102/24 192.168.122.200/24 fe80::5054:ff:fe04:2c5d/64
```

可以看到server2拥有了虚拟IP



