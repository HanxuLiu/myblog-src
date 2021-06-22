﻿---
title: markdown介绍和使用说明
date: 2021-05-21 22:53:01
category: Markdown
tags: Markdown
comment: 'valine'
---


[![陈树义](https://pic1.zhimg.com/v2-9d019104896d4a8c9b94ba1ea53eadc9_xs.jpg?source=172ae18b)](https://www.zhihu.com/people/chen-shu-yi-21)

[陈树义](https://www.zhihu.com/people/chen-shu-yi-21)[](https://www.zhihu.com/question/48510028)

广州唯品会信息科技有限公司 Java开发工程师

854 人赞同了该文章

写博客、写文章比较多的人都知道 Markdown 是什么。

> Markdown 是一种轻量级标记语言，创始人为 John Gruber。它允许人们「使用易读易写的纯文本格式编写文档，然后转换成有效的 XHTML（或者 HTML）文档」。——维基百科

简单地说，Markdown 与 HTML 语言一样，使用一些符号就代替样式。但是它比 HTML 语言更加简单。例如我想要实现标题样式，那么我可以这么做：

![img](https://pic1.zhimg.com/80/v2-169597b288329de5b969b341c5fed180_720w.jpg)

## **Markdown的本质**

有些朋友会觉得，我用 Word 也可以实现同样的效果，为什么要使用 Markdown 呢？这个问题提得非常好，直戳问题本质。

Word 等其他文本编辑工具虽然也能实现同样的效果，但是其功能太强大了。Word 还能改变字体颜色，还可以调节字体大小。这么强大的功能可以说是 Word 的优点，但也可以说是它的缺点。

当功能多了，我们的注意力就会分散，我们就会去纠结：这个字体大小到底是 14 还是 16 好？我想强调这一句话，是要加粗还是标红？

而 Markdown 的出现让我们不需要去纠结这些问题。**Markdown 的本质是让我们回归到内容本身，注重文章本身的结构，而不是样式。**

例如我们想要强调一句话，那么我们直接使用两个星号为其加粗，而不需要考虑是加粗还是标红（Markdown中只有黑色字体，没有其他颜色字体）。

![img](https://pic2.zhimg.com/80/v2-51ce4ea5fbdf997b5137476c14c18135_720w.jpg)

你也不需要关心字体大小问题，因为 Markdown 中字体大小都与结构有关，无法手动设置。一级标题字体大一些，二级标题小一些。我们只需要关注哪些是一级标题，哪些是二级标题。而不需要去纠结标题字体到底要设置多大。

**Markdown 的本质是让我们回归内容本身。** 当你明白这个道理的时候，你会明白为什么许多功能 Markdown 没有。

## **Markdown语法**

原生的 Markdown 语法只包含了最基础的一些特性，例如：标题、加粗、斜体、段落、引用、超链接、图片、列表、代码块、水平分割线等。而一些自定义实现的编辑器，则有一些自定义的实现，例如：数学公式、TODO列表等。所以当你看到有一些语法在一个编辑可以用，但到另一个编辑器又用不了的时候，那么这个语法可能就不是通用的 Markdown 原生语法了。

Markdown 语法非常简单，基本上只要多用几次就会了。下面简单介绍一些常用的 Markdown 语法。

### **段落**

Markdown 使用 `#` 号来表示段落。

![img](https://pic4.zhimg.com/80/v2-ddbecf1f0fa5a9a5e92ab996d8863d23_720w.jpg)

### **加粗、斜体、加粗斜体**

Markdown 使用一个星号表示斜体，两个星号表示加粗，三个星号表示加粗斜体。

![img](https://pic4.zhimg.com/80/v2-4b35d8ad9479b746fa9ea0f45c06a6c3_720w.jpg)

### **无序列表**

使用星号或者中横线来表示无序列表，注意后面需要加个空格。

![img](https://pic4.zhimg.com/80/v2-dcc81e176f9d783776cc6adcd5098773_720w.jpg)

### **有序列表**

使用`数字+.`来表示有序列表，后面同样需要价格空格。

![img](https://pic4.zhimg.com/80/v2-de41880f5807e6f9fdde99c2742038b7_720w.jpg)

### **引用**

Markdown 使用 `>` 符号来表示引用。

![img](https://pic1.zhimg.com/80/v2-ade3a47093a0899a08074c7fc2c167c0_720w.jpg)

### **超链接**

Markdown 使用 `[]()` 来表示超链接，中括号表示链接文字，小括号表示链接地址。

![img](https://pic4.zhimg.com/80/v2-63cc3a2b007a0a449ac2d549d62c6277_720w.jpg)

### **图片**

图片的样式与超链接非常相似，只需在前面加个感叹号就可以了，即用`![]()`表示图片。其中中括号表示图片未加载时的提示文字，小括号表示图片地址。

![img](https://pic1.zhimg.com/80/v2-a059af56aadb832034c4057c0f136824_720w.jpg)

### **代码块**

我们使用 ` 号来表示代码块引用。

![img](https://pic2.zhimg.com/80/v2-736e127b8d833dc85e0499f7f817c3a1_720w.jpg)

### **水平分割线**

使用 `---` 来表示分割线

![img](https://pic1.zhimg.com/80/v2-dc4df419d95a4bd2c05d8b7ab920fed8_720w.jpg)

### **表格**

使用一根竖线来分隔各个单元格，使用冒号来决定单元格的对齐方向。

![img](https://pic4.zhimg.com/80/v2-3222f96f11294e78f28bc1f949965557_720w.jpg)

## **Markdown使用场景**

目前越来越多的站点都支持了 Markdown 语法，例如：Github 等代码托管平台，简书、博客园等写作平台。因此，掌握好 Markdown 语法可以非常方便地在各个主流站点使用。

## **编辑工具**

因为 Markdown 本质上是一个纯文本，所以任何能打开纯文本的东西都可以来编辑 Markdown。但如果你想要有预览功能，那么一款顺手的编辑功能还是有必要的。

在 Windows 上比较不错的编辑器有：MarkdownPad、Typora。在 OSX 上比较不错的编辑器有：Mou、MacDown、MWeb。

而我之前使用 MouDown 和 sublime 做简单的编辑，但是笔记等的就使用印象笔记写，而要发表的文章则使用 MWeb 编写。

## **兼容神器**

使用了 Markdown 可以写出条理非常清晰的文章，文章结构特别清晰。但是对于一些老编辑器而言，例如：ATLASSIAN 的 WIKI 对于 Markdown 的支持就很弱。

那么怎么办呢？

其实很简单，因为老的编辑器基本都支持 HTML，所以我们可以将 Markdown 渲染后的 HTML 复制到老编辑器。

我一般是在本地写好 Markdown 文章，之后直接复制到一个 Markdown 编辑器，然后复制对应的 HTML 文本到老编辑器中。

![img](https://pic4.zhimg.com/80/v2-394d2ecadb292765ac1d810cbe5672b3_720w.jpg)

之后点击一下蓝色的复制按钮，就可以复制渲染后的 HTML 文本，最后直接粘贴到老编辑器中就可以了。我用的 Markdown 编辑器是：**[https://mdnice.github.io/](https://link.zhihu.com/?target=https%3A//mdnice.github.io/)**，样式非常漂亮。

## **进阶应用**

当你学会使用 Markdown 之后，你会发现 Markdown 还有更多好玩的地方。例如：开源的 gitbook 就可以教你把一个个 Markdown 文件组织起来，弄成一本电子书。

![img](https://pic2.zhimg.com/80/v2-cdc7ada790a4473145b3c1207a3f8495_720w.jpg)

具体如何操作，可以参考这里：**[https://github.com/chenyurong/g](https://link.zhihu.com/?target=https%3A//github.com/chenyurong/gitbook-notebook)**
