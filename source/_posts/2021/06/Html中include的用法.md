---
title: Html中include的用法
author: HiuTingYin
date: 2021-06-26 17:38:00 +0800
categories:  [日常问题合集, HTML]
tags: HTML
excerpt: 因为在日常活动开发过程中，页面的底部基本都需要 版权或者相关的页脚 信息，这时候就可以将这部分代码单独抽成一个html文件，每次引用即可。
---


### 问题描述
因为在日常活动开发过程中，页面的底部基本都需要 版权或者相关的页脚 信息，这时候就可以将这部分代码单独抽成一个html文件，每次引用即可。
所以在html中使用了<!--#include file=footer.html">，结果发现页面上并没有引入到footer.html页面,F12看是以注释的形式展示出来了。


### 解决方法
最后发现是因为我nginx没有开启ssi，include是依赖于ssi的。

在nginx.conf中开启ssi就可以了，配置修改后重启nginx。

```$xslt
server {
        listen        80;
        server_name  localhost;
        root   "xxx";

        # 开启ssi 
        ssi on;  
        ssi_silent_errors on; 
 
        location / {
            index index.php index.html error/index.html;
        }
        if (!-f $request_filename) {
            rewrite ^/(.*)  /index.php/$1 last;
        }
}
```

### ssi扩展

SSI 是 Server Side Include 的首字母缩略词，是一种基于服务端的网页制作技术。通过一个非常简单的语句即可调用包含文件，页面传送给浏览器之前，服务器会对页面所包含的文件放回页面中去。

SSI 默认情况下是不开启的，需要用户自己开启。开启所需要的三个参数：ssi，ssi_silent_errors和ssi_types

1、nginx 配置
```$xslt
ssi on  #开启ssi支持，默认是off
ssi_silent_errors on #默认值是off，开启后在处理SSI文件出错时不输出错误提示:"[an error occurred while processing the directive] "
ssi_types text/html #默认是ssi_types text/html，所以如果需要htm和html支持，则不需要设置这句，如果需要shtml支持，则需要设置：ssi_types text/shtml
```

2、引用格式
```$xslt
<!--#include file="foot.html"-->
```
