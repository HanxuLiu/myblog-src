---
title: Ubuntu18.04/16.04下Shadowsocks服务器端安装及优化
comment: valine
date: 2021-05-23 22:16:26
category: 科学上网
tags: Shadowsocks
---


## 前言

本教程旨在提供简明的Ubuntu 16.04下安装服务器端Shadowsocks。不同于Ubuntu 16.04之前的教程，本文抛弃initd，转而使用Ubuntu 16.04支持的Systemd管理Shadowsocks的启动与停止，显得更为便捷。优化部分包括BBR、TCP Fast Open以及吞吐量优化。

本教程仅适用于Ubuntu 16.04及之后的版本，基于Python 3，支持IPv6。


## 安装`pip`

本教程使用Python 3为载体，因Python 3对应的包管理器`pip3`并未预装，首先安装`pip3`：

```bash
sudo apt install python3-pip
```


## 安装Shadowsocks

因Shadowsocks作者不再维护`pip`中的Shadowsocks（定格在了2.8.2），我们使用下面的命令来安装最新版的Shadowsocks：

```bash
pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip
```

安装完成后可以使用下面这个命令查看Shadowsocks版本：

```bash
sudo ssserver --version
```

目前会显示“Shadowsocks 3.0.0”。


## 创建配置文件

创建Shadowsocks配置文件所在文件夹：

```bash
sudo mkdir /etc/shadowsocks
```

然后创建配置文件：

```bash
sudo nano /etc/shadowsocks/config.json
```

复制粘贴如下内容（注意修改密码“password”）：

```json
{
    "server":"::",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"chacha20-ietf-poly1305",
    "fast_open": false
}
```


**添加配置信息多用户**

```javascript
// 多用户
{
    "server":"::",
    "local_address":"127.0.0.1",
    "local_port":1080,
  "port_password":{
         "8888":"123456789",
         "9999":"123456789"
  },
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```

**添加配置信息单用户**

```javascript
// 单用户
{
    "server":"::",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"123456789",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

**参数说明：**

```javascript
server：Shadowsocks服务器地址 
server_port：Shadowsocks服务器端口 
local_address：   本地IP
local_port：本地端口 
password：Shadowsocks连接密码 
timeout：等待超时时间 
method：加密方式 
workers:          工作线程数 
fast_open：       true或false
```



## 测试Shadowsocks配置

首先记录下服务器的IP地址

```bash
ifconfig
```

找到IPv4地址（和IPv6地址），如我的`ifconfig`输出为

```plain
eth0      Link encap:Ethernet  HWaddr 46:91:89:4e:c1:52
          inet addr:138.68.51.55  Bcast:138.68.63.255  Mask:255.255.240.0
          inet6 addr: fe80::4491:89ff:fe4e:c152/64 Scope:Link
          inet6 addr: 2604:a880:2:d0::3727:7001/64 Scope:Global
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:102667 errors:0 dropped:0 overruns:0 frame:0
          TX packets:7869 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:151166937 (151.1 MB)  TX bytes:1151476 (1.1 MB)
```

所以我的IPv4地址是`138.68.51.55`，IPv6地址是`2604:a880:2:d0::3727:7001`。

然后来测试下Shadowsocks能不能正常工作了：

```bash
ssserver -c /etc/shadowsocks/config.json
```

在Shadowsocks客户端添加服务器，如果你使用的是我提供的那个配置文件的话，地址填写你的`IPv4地址`或`IPv6地址`，端口号为`8388`，加密方法为`chacha20-ietf-poly1305`，密码为`你设置的密码`。然后设置客户端使用全局模式，浏览器登录Google试试应该能直接打开了。



## 配置Systemd管理Shadowsocks

新建Shadowsocks管理文件

```bash
sudo nano /etc/systemd/system/shadowsocks-server.service
```

复制粘贴：

```plain
[Unit]
Description=Shadowsocks Server
After=network.target

[Service]
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks/config.json
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

`Ctrl + O`保存文件，`Ctrl + X`退出。

启动Shadowsocks：

```bash
sudo systemctl start shadowsocks-server
```

设置开机启动Shadowsocks：

```bash
sudo systemctl enable shadowsocks-server
```

至此，Shadowsock服务器端的基本配置已经全部完成了！



## 优化

这部分属于进阶操作，在你使用Shadowsocks时感觉到延迟较大，或吞吐量较低时，可以考虑对服务器端进行优化。


### 开启BBR

BBR系Google最新开发的TCP拥塞控制算法，目前有着较好的带宽提升效果，甚至不比老牌的锐速差。

#### 升级Linux内核

BBR在Linux kernel 4.9引入。首先检查服务器kernel版本：

```bash
uname -r
```

如果其显示版本在4.9.0之下，则需要升级Linux内核，否则请忽略下文。


#### 开启BBR

运行`lsmod | grep bbr`，如果结果中没有`tcp_bbr`，则先运行：

```bash
modprobe tcp_bbr
echo "tcp_bbr" >> /etc/modules-load.d/modules.conf
```

运行：

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

运行：

```bash
sysctl -p
```

保存生效。运行：

```bash
sysctl net.ipv4.tcp_available_congestion_control
sysctl net.ipv4.tcp_congestion_control
```

若均有`bbr`，则开启BBR成功。


### 优化吞吐量

新建配置文件：

```bash
sudo nano /etc/sysctl.d/local.conf
```

复制粘贴：

```plain
# max open files
fs.file-max = 51200
# max read buffer
net.core.rmem_max = 67108864
# max write buffer
net.core.wmem_max = 67108864
# default read buffer
net.core.rmem_default = 65536
# default write buffer
net.core.wmem_default = 65536
# max processor input queue
net.core.netdev_max_backlog = 4096
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

net.ipv4.tcp_congestion_control = bbr
```

运行：

```bash
sysctl --system
```

编辑之前的shadowsocks-server.service文件：

```bash
sudo nano /etc/systemd/system/shadowsocks-server.service
```

在`ExecStart`前插入一行，内容为：

```plain
ExecStartPre=/bin/sh -c 'ulimit -n 51200'
```

即修改后的shadowsocks-server.service内容为：

```plain
[Unit]
Description=Shadowsocks Server
After=network.target

[Service]
ExecStartPre=/bin/sh -c 'ulimit -n 51200'
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks/config.json
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

`Ctrl + O`保存文件，`Ctrl + X`退出。

重载shadowsocks-server.service：

```bash
sudo systemctl daemon-reload
```

重启Shadowsocks：

```bash
sudo systemctl restart shadowsocks-server
```


### 开启TCP Fast Open

TCP Fast Open可以降低Shadowsocks服务器和客户端的延迟。实际上在上一步已经开启了TCP Fast Open，现在只需要在Shadowsocks配置中启用TCP Fast Open。

编辑config.json：

```bash
sudo nano /etc/shadowsocks/config.json
```

将`fast_open`的值由`false`修改为`true`。`Ctrl + O`保存文件，`Ctrl + X`退出。

重启Shadowsocks：

```bash
sudo systemctl restart shadowsocks-server
```

注意：TCP Fast Open同时需要客户端的支持，即客户端Linux内核版本为3.7.1及以上；你可以在Shadowsocks客户端中启用TCP Fast Open。

至此，Shadowsock服务器端的优化已经全部完成了！