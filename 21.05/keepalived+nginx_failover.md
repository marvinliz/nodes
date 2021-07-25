# KeepAlived

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
        state MASTER #状态 MASTER|BCAKUP
        interface eth0 #虚拟IP绑定的网卡名
        virtual_router_id 51 #唯一节点ID，相同的节点ID组成集群
        priority 255 #权重 0-255 权重越大优先抢占虚拟IP
        advert_int 1 #广播频率 1秒1次
        authentication { #通信身份验证配置
              auth_type PASS
              auth_pass 12345 #密码
        }
        virtual_ipaddress { # 虚拟IP，最多20个
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

## 防火墙配置

需要放过vrrp协议

```shell
firewall-cmd --direct --permanent --add-rule ipv4 filter INPUT 0 --destination 224.0.0.18 --protocol vrrp -j ACCEPT
firewall-cmd --direct --permanent --add-rule ipv4 filter OUTPUT 0 --destination 224.0.0.18 --protocol vrrp -j ACCEPT
firewall-cmd --reload
```



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

# nginx

## 安装

```shell
yum install -y epel-release
yum install -y nginx
```

## 配置

两台机器使用相同的配置 

nginx 配置文件位置 `/etc/nginx/nginx.conf` 

```shell
    # 在http模块中添加相应热备配置
    upstream web-front {
        server {ip}:8020;
        server {ip}:8020 backup;
    }

    upstream web-backend {
        server {ip}:8090;
        server {ip}:8090 backup;
    }

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location /datasupport {
                proxy_connect_timeout 90;
                proxy_pass http://web-backend;
                proxy_read_timeout 90;
                proxy_send_timeout 90;
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_http_version 1.1;
                client_max_body_size 10m;
                proxy_buffering off;
        }

        location / {
                proxy_pass http://web-front;
        }
    }

```

## 启动

```shell
nginx
```



