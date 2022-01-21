---
title: UEditor使用
author: HiuTingYin
date: 2021-06-07 16:55:00 +0800
categories: [日常问题合集, HTML]
tags: HTML
excerpt: UEditor富文本编辑器使用过程中踩过的坑
---


### 问题描述及解决方案
前端时间在做玩家投稿活动，因为涉及到文章投稿，所以用了UEditor富文本编辑器。
在使用过程中，遇到了以下几个问题，基本可以通过修改UEDITOR_CONFIG的配置即可实现。

1、关闭字体颜色、背景色设定，玩家如果从外部复制文案的话，也需要去除对应的颜色设定
```$xslt
    window.UEDITOR_CONFIG.retainOnlyLabelPasted = true;//粘贴只保留标签，去除标签所有属性
```

2、关闭图片粘贴入口
```$xslt
    window.UEDITOR_CONFIG.pasteplain = true;//默认为纯文本粘贴
```

3、关闭富文本框大小的调整入口
```$xslt
    window.UEDITOR_CONFIG.scaleEnabled = false;
```

4、上传图片太大，导致整个富文本框充满图片，需要对图片大小进行处理
```$xslt
    window.UEDITOR_CONFIG.initialStyle = 'img{max-height:200px;max-width:100%}';
```

5、关闭图片的大小调整入口
```$xslt
<style>
    #edui1_imagescale {
        display: none !important;
    }
</style>
```

6、UEditor提交的文档前端展示处理
UEditor提交的文档，展示的时候基本是通过iframe直接展示对应的html内容。
但是由于活动的弹窗样式等，可能会出现玩家自定义的内容与弹窗样式相违和，所以需要对玩家提交的文档样式进行处理。
```$xslt
    //1.填充内容至iframe
     $('#articleContent').contents().find('body').html(data.content);
    //2.修改文档样式
    var x = document.getElementById("articleContent");
    var y = (x.contentWindow || x.contentDocument);
    if (y.document) y = y.document;
    
    y.body.style.background = "transparent";//去除背景色
    y.body.style.color = "white";//设置默认字体颜色
    y.body.style.fontSize = '14px';//设置默认字体大小
    y.body.style.whiteSpace = 'pre-line';//自动换行
    y.body.style.wordBreak = 'break-all';
    y.body.style.lineHeight = '20px';
    //判断是否有白背景文字
    var deptObjs = y.getElementsByTagName("p");
    for (var i = 0; i < deptObjs.length; i++) {
        var tmp = deptObjs[i];
        var bg = window.getComputedStyle(tmp, null);
        if (bg['background-color'] == 'rgb(255, 255, 255)') {
            tmp.style.background = "transparent";
            tmp.style.color = "#c2cde8";
        }
    }
    //设置图片大小
    var imgArr = y.getElementsByTagName('img');
    for (var j = 0; j < imgArr.length; j++) {
        var img = imgArr[j];
        img.style.maxHeight = "200px";
        img.style.maxWidth = "500px";
    }
```