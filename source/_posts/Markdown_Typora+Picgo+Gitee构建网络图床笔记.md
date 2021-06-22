---
title: Typora+Picgo+Gitee构建网络图床笔记
date: 2021-05-21 22:39:08
category: Markdown
tags: Markdown
comment: 'valine'
---


**前言**

之前一直在Typora上记录自己的笔记，但是发现markdown格式的笔记在插入图片时，仅能采用两种方法：1）在线图床，2）本地路径，即图片并不是像Word那样集成在文档内的，因此经过一番研究，发现了最新版的Typora已经支持在线图床功能，遂写此篇教程帮助后人构建自己的在线图床markdown笔记。

## **所需软件**

typora

[https://typora.io/](https://link.zhihu.com/?target=https%3A//typora.io/)



picgo

[https://github.com/Molunerfinn/PicGo/releases](https://link.zhihu.com/?target=https%3A//github.com/Molunerfinn/PicGo/releases)

建议选择2.2.2正式版本，beta版本可能会存在不稳定的风险。

![img](https://pic3.zhimg.com/80/v2-be88c425ba401a71e9c91dca74f0d262_720w.jpg)



Gitee

由于国内连接github时网络偶尔存在不稳定的情况，于是采用了gitee作为github图床的替代，但整体教程是一样的

[https://gitee.com/](https://link.zhihu.com/?target=https%3A//gitee.com/)

![img](https://pic3.zhimg.com/80/v2-daba73811b87533f8f032169d3d11d9a_720w.png)

## **具体操作**

主要是分为两部分操作，第一部分是Typora的配置，第二部分是Picgo+Gitee的在线图床配置

### **Typora配置**

打开Typora后，点击菜单栏-文件-偏好设置

找到其中的“图像”设置，将其设置为如下内容

![img](https://pic3.zhimg.com/80/v2-f374bec8d90d87c2f7683c1133064166_720w.jpg)

1）**插入图片时...**下边的选择框内选择**上传图片**

2）勾选上“**对本地位置的图片应用上述规则**”

3）上传服务选择**PicGo(app)**

4）将**PicGo路径**项设置为本地PicGo的安装路径

这样，我们就把Typora配置好了，下面我们进行PicGo+Gitee的网络图床构建

### **网络图床配置**

首先，安装好PicGo之后，我们需要给PicGo配置插件以支持Gitee图床

**注意**：你必须安装[Node.js](https://link.zhihu.com/?target=https%3A//nodejs.org/en/)之后才能安装PicGo的插件，因为PicGo要使用`npm`来安装插件。

安装好Nodejs后，点开左边的**插件设置**一栏，在输入框内输入“github plus”，如下

![img](https://pic1.zhimg.com/80/v2-270b499aeadae15d014215b32b1204c8_720w.jpg)

点击安装此插件，之后左侧选项栏**图床设置**会多一个**githubplus**

![img](https://pic1.zhimg.com/80/v2-1fd1e0c2ef8c48f1d85a5a04b8566714_720w.png)



接下来我们配置Gitee仓库以存储图片

1）进入[https://gitee.com/](https://link.zhihu.com/?target=https%3A//gitee.com/)，没有账号的话，先注册账号，注册以后登录，新建一个**公开仓库**，名字为picgo（可以自己起其他名字）

2）点击右上角，进入**设置**，在左侧的**安全设置-私人令牌**处生成新令牌。（注意：生成的新令牌只会显示一次，一定要保存好！！！）

我们需要做的如下：

1）回到picgo，按照如下进行设置

![img](https://pic3.zhimg.com/80/v2-dceba8a3986c7df4dbc3f8cb1e8ca60e_720w.jpg)

其中的repo为 **UserName/仓库名称** 格式

branch填入master

Token为刚才在Gitee生成的私人令牌，粘贴到这里就行

path为仓库下用于存储图片的路径，这个可以自行选择

最下边的origin部分选择gitee（默认是github）

## **结束**

经过上述操作，我们就把typora+picgo+gitee成功配置好了，之后当我们将本地的图片粘贴到markdown文档内的时候，typora会自动将图片上传到刚才我们配置好的gitee仓库内，并自动把markdown文档内的本地路径转化为gitee的图片外链，便于我们以后进行多端访问文档内的图片。