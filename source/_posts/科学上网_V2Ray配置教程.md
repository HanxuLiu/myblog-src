---
title: V2Ray配置教程
index_img: https://v2xtls.org/wp-content/uploads/2021/03/v2rayN主界面.png
comment: valine
date: 2021-07-03 13:06:13
category: 科学上网
tags: V2Ray
---

# V2Ray 配置教程

V2Ray 是继 Shadowsocks(R)后又一个体验很棒、功能非常强大的科学上网工具，近年来受到网友的广泛关注和喜爱。敏感时期，基本上只有 [V2ray 伪装](https://tlanyan.pp.ua/v2ray-traffic-mask/) 或 [trojan](https://tlanyan.pp.ua/trojan-tutorial/) 能提供十分稳的服务，推荐使用。

本教程详细介绍 V2ray 的特点，安装和配置过程，让读者能迅速上手和使用 V2ray。在本文基础上，建议网友继续阅读 [V2ray 高级技巧：流量伪装](https://tlanyan.pp.ua/v2ray-traffic-mask/)，以及最新出炉的[VLESS 协议和 XTLS 黑科技](https://tlanyan.pp.ua/introduce-v2ray-vless-protocol/)，体验更稳的上外网方式。v2ray 客户端下载请访问：[V2ray 客户端](https://tlanyan.pp.ua/v2ray-clients-download)。

## V2Ray 简介


`V2Ray`是近几年十分流行的网络工具，其功能强大，用途不限于突破防火墙，但因其能有效翻墙而广为人知。`V2Ray`有如下大放异彩的特点：

- 开源。`V2Ray`是`Project V`的核心工具，源代码开源；
- 多协议支持。传输层支持 TCP、mKCP、WebSocket 等，上层协议支持 Socks、Shadowsocks、以及自定义的`VMess`等；
- 多入口和多出口。`V2Ray`可同时支持多个入站和出站协议，每个协议独立工作；
- 多平台支持。原生支持 Windows、Linux、MacOS 三大常用平台，安卓、iOS 两大移动平台有丰富的第三方工具；
- 隐蔽性。`V2Ray`流量可伪装成网页流量，更难被检测和干扰。

与另一个知名的翻墙工具`Shadowsocks(R)`相比，V2ray 和 SSR 区别在于：

1.  `V2Ray`是一个框架/平台，而`Shadowsocks(R)`是一个代理工具；
2.  `V2Ray`功能强大配置复杂，`Shadowsocks(R)`简单易用；
3.  `V2Ray`性能更好，协议支持更完善。

一句话总结：`V2Ray`更好更强大，但更难上手和用好。

> 因 v2ray 原开发者长期不上线，社区为了维护方便创建了 v2fly，v2fly 的官网是 [https://www.v2fly.org](https://www.v2fly.org)。大名鼎鼎的 v2ray 白话文、一键安装脚本等都出自 v2fly。目前 v2ray 项目主要由 v2fly 推动，v2fly 的代码更新也会合并到 v2ray 项目库中，一般可以认为 v2ray 和 v2fly 是同一个组织。
>
> V2ray 发布了新版 VLESS 协议，详情请参考 [VLESS 协议介绍和使用教程](https://tlanyan.pp.ua/introduce-v2ray-vless-protocol/)

## 一、服务器配置
### 前提
- 买一台位于境外的服务器。
- 在本地使用 SSH 连到服务器。

### 安装V2Ray
在 CentOS、Ubuntu 等常用 Linux 系统上，直接执行如下命令安装`V2Ray`（如果已安装则更新程序）：

    bash <(curl -sL https://raw.githubusercontent.com/hijkpw/scripts/master/goV2.sh)


安装完成后，安装成功后会自动生成的配置文件，配置文件为`/etc/v2ray/config.json`。  
cat 命令可查看内容：`cat /etc/v2ray/config.json`：

```bash
[root@paopao v2ray]# cat config.json 
{
  "inbounds": [{
    "port": 12345,
    "protocol": "vmess",
    "settings": {
      "clients": [
        {
          "id": "9e3939be-76db-4757-a95e-db5158725eaa",
          "level": 1,
          "alterId": 64
        }
      ]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      }
    ]
  }
}
```

配置文件中”inbounds”下的这几项信息需要留意：  
port（`端口`）、clients 中的 id（`用户id`）和 alterId（`额外id`），它们将在配置客户端时用到。

配置文件无需任何改动即可正常使用。接下来防火墙放行监听的端口，设置开机启动并运行`V2Ray`：

```bash
    # firewalld放行端口（适用于CentOS7/8）
    firewall-cmd --permanent --add-port=12345/tcp # 12345改成你配置文件中的端口号
    firewall-cmd --reload
    
    # ufw放行端口（适用于ubuntu）
    ufw allow 12345/tcp # 12345改成你的端口号
    
    # iptables 放行端口（适用于CentOS 6/7）
    iptables -I INPUT -p tcp --dport 12345 -j ACCEPT
    
    # 设置开机启动
    systemctl enable v2ray
    
    # 运行v2ray
    systemctl start v2ray
```

`ss -ntlp | grep v2ray` 命令可以查看 v2ray 是否正在运行。  

```bash
[root@paopao v2ray]# ss -ntlp | grep v2ray
LISTEN      0      128       *:12345   *:*     users:(("v2ray",pid=9544,fd=3))
```
如果输出如上面的内容，则成功了。   
如果输出为空，大概率是被 selinux 限制了，解决办法如下：

1\. 禁用 selinux：`setenforce 0`;

2\. 重启 v2ray：`systemctl restart v2ray`

到此，服务端应该配置好了。如果服务器商层面还有防火墙（阿里云/Google/AWS 购买的 vps），请登录网页后台，放行 v2ray 的端口。


## 二、Windows客户端：V2rayN 配置

V2rayN 是一个基于[V2ray](https://v2raytech.com/category/v2ray/)核心的 Windows 客户端，功能强大且支持多种协议。V2rayN 4.0 版本开始取消 PAC 模式，给许多用户使用上带来困难。鉴于此，本文介绍最新 V2rayN 4.12 正式版的配置教程，让你能顺利切换 PAC 模式和全局模式。

### V2rayN 4.12 配置教程

到这个页面 [V2Ray windows 客户端下载](https://v2xtls.org/v2ray-windows%e5%ae%a2%e6%88%b7%e7%ab%af%e4%b8%8b%e8%bd%bd/) 下载 V2rayN 并解压。V2rayN 是绿色软件，解压后直接能用，不需要安装。

首先. 进入解压后的 V2rayN-core 目录，双击”v2rayN”文件，程序启动后会最小化到右下角的托盘。双击蓝色的 V 字小图标，打开 V2rayN 的主界面：

[![v2rayN主界面](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN主界面.png)](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN主界面.png)

v2rayN 主界面

接下来，**如果用了 VLESS 或者 XTLS 技术，请更换为 Xray 内核**，否则可能无法正常连接到节点！操作为：点击“设置” -> “参数设置” -\> “V2rayN 设置” -> “Core 类型”，选择“Xray_core”，然后点击确定：

[![v2rayN切换Xray内核](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN切换Xray内核.png)](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN切换Xray内核.png)

v2rayN 切换 Xray 内核

接着添加新的 V2ray 节点：点击界面上方的“服务器”，按照自己节点类型选择，**注意 VMess 服务器和 VLESS 服务器是不同的**！

[![v2ray添加新服务器](https://v2xtls.org/wp-content/uploads/2021/03/v2ray添加新服务器.png)](https://v2xtls.org/wp-content/uploads/2021/03/v2ray添加新服务器.png)

v2ray 添加新服务器

这里以我们以手动 VLESS 服务器为例进行说明，根据 V2ray 节点信息填写地址、端口、用户 ID 等、流控等信息。VLESS 协议不需要额外 id（alterID），所以不需要填写。反过来说如果你看到要填写额外 ID，说明服务器类型选错了！

底层传输安全那里，建议跳过证书验证为 false，证书过期或者上不了外网再改成 true。SNI 填写伪装域名，也就是保持和伪装域名那里填的一样就可以了：

[![v2rayN添加VLESS服务器](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN添加VLESS服务器.png)](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN添加VLESS服务器.png)

v2rayN 添加 VLESS 服务器

配置好后点击确定，主界面出现了节点的简要信息。可以双击节点那一行再次出来上面的配置界面修改配置，也可以点击右键删除、移动该节点：

[![V2rayN添加了节点界面](https://v2xtls.org/wp-content/uploads/2021/03/V2rayN添加了节点界面.png)](https://v2xtls.org/wp-content/uploads/2021/03/V2rayN添加了节点界面.png)

V2rayN 添加了节点界面

配置好节点后需要设置系统代理才能让浏览器访问外网，操作为：右下角托盘找到 v2rayN 图标，在图标上点击右键，找到“系统代理” ，点击“自动配置系统代理”，此时图标会变成红色：

[![v2rayN设置系统代理](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN设置系统代理.png)](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN设置系统代理.png)

v2rayN 设置系统代理

默认 v2rayN 是全局代理，也就是不管访问什么网站，都是走节点流量。大部分网友之前用 v2rayN 都是 PAC 模式，v2rayN 4.0 版本开始取消了 PAC，因此需要下面的操作来代替 PAC：

主界面点击“设置” -> “路由设置” -> “基础功能” -\> “一键导入基础规则”，然后点击“确定”：

[![V2rayN设置PAC模式](https://v2xtls.org/wp-content/uploads/2021/03/V2rayN设置PAC模式.png)](https://v2xtls.org/wp-content/uploads/2021/03/V2rayN设置PAC模式.png)

V2rayN 设置 PAC 模式

> 域名策略一般建议选“IPIfNotMatch”或者“ASIS”，也可以按照需求选其他的

接下来访问百度、淘宝等国外网站都是直连，不会再走代理了。如果想让某个网站走代理而不是直连，那么在上图中“代理的 Domain 或 IP”的左边加一行：`domain:网站域名`，例如添加本站走代理：domain:v2raytech.com，然后点击“确定”。

想换成所有网站都走代理又该怎么操作呢？方法也很简单：上图中“直连的 Domain 或 IP”，把左边和右边输入框的行都清空，然后点击“确定”。

V2ray 节点没挂，你配置又没问题的话，接下来应该能愉快的上外网了。

> 如果不想上外网了，右下角托盘找到 v2rayN 图标，在图标上点击右键，找到“系统代理” ，点击“清除系统代理”。


### 查看 V2rayN 日志

配置好后不能上外网，请先参考 [PC 端科学上网常见问题](https://v2xtls.org/pc%e7%ab%af%e7%a7%91%e5%ad%a6%e4%b8%8a%e7%bd%91%e5%b8%b8%e8%a7%81%e9%97%ae%e9%a2%98/)，不能解决再复制 V2rayN 日志错误信息，然后在本教程下面留言，或者到 [网络跳越论坛](https://hijk.club) 或 tg 群组[https://t.me/hijkclub](https://t.me/hijkclub) 交流。

V2rayN 的日志在主界面下面的黑框框里，不能上外网的话一般会输出类似下面的日志：

[![v2rayN日志](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN日志.png)](https://v2xtls.org/wp-content/uploads/2021/03/v2rayN日志.png)

v2rayN 日志

### V2rayN 报病毒

由于一些原因，V2rayN 在 Windows 上可能会报病毒导致无法正常使用：

[![windows提示v2rayN病毒](https://v2xtls.org/wp-content/uploads/2021/03/windows提示v2rayN病毒.png)](https://v2xtls.org/wp-content/uploads/2021/03/windows提示v2rayN病毒.png)

windows 提示 v2rayN 病毒

如果不放心 V2rayN，可以换其他客户端，比如 [Clash for Windows](https://v2xtls.org/clash-for-windows%e9%85%8d%e7%bd%aev2ray%e6%95%99%e7%a8%8b/)，或者试试从官网下载的版本。但一般来说这都是 Windows 的误报，添加到例外即可。操作为：点击右下角托盘的 Windows 安全中心 -> “病毒和威胁防护” -> “保护历史记录” -> “已隔离威胁” -> “操作”，点击“还原”。如果 V2rayN 界面里还是提示病毒，找到同一个界面，操作点击“允许”：

[![还原病毒提示](https://v2xtls.org/wp-content/uploads/2021/03/还原病毒提示-1024x794.png)](https://v2xtls.org/wp-content/uploads/2021/03/还原病毒提示.png)

还原病毒提示

尽管移除了 PAC 模式，但 V2rayN 还是 Windows 上最好用的 V2ray、trojan 客户端之一，非常推荐！


## 三、安卓客户端：V2rayNG 配置

**v2rayNG** 是安卓平台上一款基于 v2ray 核心的简洁、功能强大的客户端。本文通过图文详细介绍其安装和配置过程，希望对在安卓手机上使用 v2rayNG 进行科学上网的网友有帮助。如果配置完后依然无法上外网，请参考：[科学上网常见问题](https://v2xtls.org/%e7%a7%91%e5%ad%a6%e4%b8%8a%e7%bd%91%e5%b8%b8%e8%a7%81%e9%97%ae%e9%a2%98/)。

阅读本教程的前提：

1.  已有服务端信息，可参考 [v2ray 一键脚本](https://v2xtls.org/v2ray%e4%b8%80%e9%94%ae%e8%84%9a%e6%9c%ac/) 或 [获取科学上网服务端信息](https://v2xtls.org/%e8%8e%b7%e5%8f%96%e7%a7%91%e5%ad%a6%e4%b8%8a%e7%bd%91%e6%9c%8d%e5%8a%a1%e7%ab%af%e4%bf%a1%e6%81%af/)
2.  下载 v2rayNG，未下载的请到这个页面下载：[V2Ray 安卓客户端下载](https://v2xtls.org/v2ray%e5%ae%89%e5%8d%93%e5%ae%a2%e6%88%b7%e7%ab%af%e4%b8%8b%e8%bd%bd/)

### 操作步骤

1\. 安装下载的 v2rayNG apk 文件；

2\. 打开软件，进入主界面（即配置文件界面）：

[![v2rayNG主界面](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG主界面-576x1024-1.png)](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG主界面-576x1024-1.png)

v2rayNG 主界面

3\. 点右上方的“+”，可以看到 V2rayNG 支持从二维码、剪切板导入，也可以手动配置 vmess、ss 等，还支持订阅（在自定义配置中）：

[![v2rayNG服务器菜单](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG服务器菜单-576x1024-1.png)](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG服务器菜单-576x1024-1.png)

v2rayNG 服务器菜单

4\. 本教程以手动输入为例，点击“手动输入\[vmess\]”，进入服务器填写界面。别名随意填写，例如“香港 vps”。地址、端口、用户 id、额外 id 按照服务端的信息填写（不知道这些信息填什么请参考参考 [获取科学上网服务端信息](https://v2xtls.org/%e8%8e%b7%e5%8f%96%e7%a7%91%e5%ad%a6%e4%b8%8a%e7%bd%91%e6%9c%8d%e5%8a%a1%e7%ab%af%e4%bf%a1%e6%81%af/)），这几项非常重要，不要填错！

[![v2rayNG基础配置](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG基础配置-576x1024-1.png)](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG基础配置-576x1024-1.png)

v2rayNG 基础配置

后半部分是高级选项，一般不用填写（加密方式一般选 auto）。

> Just My Socks V2ray 加密方式选 none 或者 auto 上不了网，请改成 chacha20-ietf-poly1305

**如果使用了伪装**，传输协议选 ws，伪装类型保持默认的 none，“功能设置”下面填入域名、伪装路径，同时底层传输安全选 tls**（不是伪装就不要选！）**：

[![v2rayNG高级配置](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG高级配置-576x1024-1.jpeg)](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG高级配置-576x1024-1.jpeg)

v2rayNG 高级配置

5\. 点界面右上角的勾保存配置。界面回到主界面，点右下角的 v2rayNG 图标，启动程序。如果出现网络连接请求，点击确定：

[![v2rayNG网络连接请求](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG网络连接请求-576x1024-1.jpeg)](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG网络连接请求-576x1024-1.jpeg)

v2rayNG 网络连接请求

6\. 一些高级设置在左侧菜单的“设置”中，建议将域名策略改成”Asis”，勾选“启用速度显示”打开 vpn 流量速度显示，其他不懂的就不用管：

[![v2rayNG全局设置](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG全局设置-576x1024-1.jpeg)](https://v2xtls.org/wp-content/uploads/2020/11/v2rayNG全局设置-576x1024-1.jpeg)

v2rayNG 全局设置

配置好且服务端没有问题的话，打开浏览器应该能正常访问 google、youtube 等网站了。
