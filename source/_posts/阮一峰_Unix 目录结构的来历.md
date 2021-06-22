---
title: Unix 目录结构的来历
comment: valine
date: 2021-06-10 18:00:00
category: Linux
tags: Linux
---

# Unix 目录结构的来历

作者： [阮一峰](http://www.ruanyifeng.com)

日期： [2012 年 2 月 6 日](http://www.ruanyifeng.com/blog/2012/02/)

Unix（包含 Linux）的初学者，常常会很困惑，不明白目录结构的含义何在。

![](/http://www.ruanyifeng.com/blogimg/asset/201202/bg2012020601.jpg)

举例来说，根目录下面有一个子目录/bin，用于存放二进制程序。但是，/usr 子目录下面还有/usr/bin，以及/usr/local/bin，也用于存放二进制程序；某些系统甚至还有/opt/bin。它们有何区别？

长久以来，我也感到很费解，不明白为什么这样设计。像大多数人一样，我只是根据[《Unix 文件系统结构标准》](http://www.pathname.com/fhs/pub/fhs-2.3.html)（Filesystem Hierarchy Standard），死记硬背不同目录的区别。

昨天，我读到了 Rob Landley 的[简短解释](http://lists.busybox.net/pipermail/busybox/2010-December/074114.html)，这才恍然大悟，原来 Unix 目录结构是历史造成的。

话说 1969 年，[Ken Thompson](http://www.ruanyifeng.com/blog/2009/06/unix_turns_40.html)和[Dennis Ritchie](http://www.ruanyifeng.com/blog/2011/10/dennis_ritchie.html)在小型机 PDP-7 上发明了 Unix。1971 年，他们将主机升级到了 PDP-11。

![](/http://www.ruanyifeng.com/blogimg/asset/201202/bg2012020602.jpg)

当时，他们使用一种叫做 RK05 的储存盘，一盘的容量大约是 1.5MB。

![](/http://www.ruanyifeng.com/blogimg/asset/201202/bg2012020603.jpg)

没过多久，操作系统（根目录）变得越来越大，一块盘已经装不下了。于是，他们加上了第二盘 RK05，并且规定第一块盘专门放系统程序，第二块盘专门放用户自己的程序，因此挂载的目录点取名为/usr。也就是说，根目录"/"挂载在第一块盘，"/usr"目录挂载在第二块盘。除此之外，两块盘的目录结构完全相同，第一块盘的目录（/bin, /sbin, /lib, /tmp...）都在/usr 目录下重新出现一次。

后来，第二块盘也满了，他们只好又加了第三盘 RK05，挂载的目录点取名为/home，并且规定/usr 用于存放用户的程序，/home 用于存放用户的数据。

从此，这种目录结构就延续了下来。随着硬盘容量越来越大，各个目录的含义进一步得到明确。

**/**：存放系统程序，也就是 At&t 开发的 Unix 程序。

**/usr**：存放 Unix 系统商（比如 IBM 和 HP）开发的程序。

**/usr/local**：存放用户自己安装的程序。

**/opt**：在某些系统，用于存放第三方厂商开发的程序，所以取名为 option，意为"选装"。

（完）

### 文档信息

- 版权声明：自由转载-非商用-非衍生-保持署名（[创意共享 3.0 许可证](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)）
- 发表日期： 2012 年 2 月 6 日
