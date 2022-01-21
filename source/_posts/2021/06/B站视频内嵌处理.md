---
title: B站视频内嵌处理
author: HiuTingYin
date: 2021-06-18 16:55:00 +0800
categories: [日常问题合集, HTML]
tags: HTML
excerpt: 内嵌B站视频处理
---


### 问题描述
在投稿类型活动过程中，会需要将玩家投稿的B站视频，在活动页中展示出来并且进行播放。
基本操作是，将视频地址通过iframe标签内嵌在活动页中。但是内嵌的视频会出现
1、B站分辨率太小，视频不高清
2、视频弹幕太多，并且游戏平台不希望主动将弹幕展示给游戏内玩家

### 解决方案
1、将默认分辨率设为 1080P
因为如果用户未登录B站的话，默认会展示360P。所以需要我们手动调整。
方法很简单，在iframe src网址那加上high_quality=1参数即可。
high_quality：是否高清。1：高清，0：最低视频质量。
修改前代码：
```$xslt
<iframe src="https://player.bilibili.com/player.html?bvid=BV1qv411V7Kc" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
```
修改后代码：
```$xslt
<iframe src="https://player.bilibili.com/player.html?bvid=BV1qv411V7Kc&high_quality=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
```

2、默认弹幕关闭
查了一下B站的相关文档，暂时还未发现可以直接通过修改iframe地址达到隐藏弹幕按钮的效果。
所以目前是视频默认关闭弹幕，让玩家手动触发。
也是通过在iframe src网址那加上danmaku=0参数即可。
修改前代码：
```$xslt
<iframe src="https://player.bilibili.com/player.html?bvid=BV1qv411V7Kc" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
```
修改后代码：
```$xslt
<iframe src="https://player.bilibili.com/player.html?bvid=BV1qv411V7Kc&high_quality=1&danmaku=0" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
```