---
title: QQ对话框中发送网站链接时，所显示预览内容的修改方法
author: HiuTingYin
date: 2020-09-30 15:55:00 +0800
categories: [日常问题合集, HTML]
tags: HTML
excerpt: 网站分享bug解决
---


### 问题描述
在PC端，QQ对话框中直接发送网站链接时，QQ会自动获取这个网页里的一张图片为摘要图片或者称之为分享icon，拉取对应的介绍内容。

有时候因为需求变更、或者因为代码的原因展示的预览内容带有代码符号，需要对这个预览内容进行修改。

### 问题解决方案
在header部分添加以下内容即可解决。

```
<meta itemprop="name" content="《英魂之刃》特约作者招募" />
<meta itemprop="image" content="http://hefx.99.com/acts/2019/10/zztgCenter/pic/video.jpg"/>
<meta itemprop="description" content="投稿即有机会获得海量积分限定皮肤，还有专属铭牌、称号、头像框等你拿。赶快来加入我们吧！"/>
```

### 踩坑注意点
- qq有缓存机制，如果你改了摘要信息没变，可能是qq缓存了。可以在地址后面加无关后缀
- 摘要图片，不能是https链接，最好用http （https 在android手机qq不能被获取，ios可以）
- 摘要图片，不能是一个重定向地址。
- 三个信息不能通过js改变（js更改摘要信息android下不生效，ios可以）服务器用php输出html是最好的