---
title: Ubuntu更新SS以支持新的加密方法
date: 2021-05-23 22:16:26
category: 科学上网
tags: Shadowsocks
---


### 遇到的问题

每次用 SSH 的方式登入服务器的时候，都会提示如下信息：

```
Welcome to Ubuntu *.*.* LTS (GNU/Linux *.*.*-*-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

 ……

13 packages can be updated.
10 updates are security updates.
```

字面提示是有 13 个包可以升级，其中 10 个是安全更新。

事实上 Ubuntu 更新还是挺容易的，因为有 `apt-get`，不过更新完之后，还是会提示一些安全更新没做完，所以找了一下解决方案。

### 解决办法

```
apt-get update
apt-get upgrade
apt-get dist-upgrade
```

#### 命令说明：

- `apt-get update` 从服务器更新可用的软件包**列表**。
- `apt-get upgrade` 根据列表，更新已安装的软件包。这个命令**不会**删除在列表中已经没有的软件包，**也不会**安装有依赖需求但尚未安装的软件包。
- `apt-get dist-upgrade` 根据列表，更新已安装的软件包。这个命令可能会为了解决软件包冲突而删除一些已安装的软件包，也可能会为了解决软件包依赖问题安装新的软件包。

所以使用上面三个命令完成更新之后，用 `reboot` 命令重启系统就行。**需要注意的是，以上所有命令都需要在 root 权限下执行。**

更新完成之后，去启动 SS，然后就报错了，从没见过的错误：

```
load libsodium failed with path None
```

不清楚是更新的系统造成的，还是其他原因，反正 SS 不能用了。搜索了一下，发现用这个 lib 的话，SS 可以支持新的高效率一点的加密方式 `chacha20` 等等这些。

于是乎，装一下 `libsodium` 吧~

```
apt-get install build-essential
wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
tar xf LATEST.tar.gz && cd libsodium-*.*.*
./configure && make -j4 && make install
```

安装完毕之后，修改 SS 配置文件，把加密换成 `chacha20-ietf-poly1305`，启动 SS，没有任何报错，一切正常。

客户端也把加密方式修改成相应的，完成~~

#### 不用自己编译的安装方式（未验证）

```
add-apt-repository ppa:chris-lea/libsodium
echo "deb http://ppa.launchpad.net/chris-lea/libsodium/ubuntu trusty main" >> /etc/apt/sources.list
echo "deb-src http://ppa.launchpad.net/chris-lea/libsodium/ubuntu trusty main" >> /etc/apt/sources.list
apt-get update && apt-get install libsodium-dev
```