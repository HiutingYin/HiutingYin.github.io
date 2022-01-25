---
title: hexo博客主题优化-Next
date: 2022-01-21 17:40:27
tags: hexo
categories: hexo
---

hexo自带的landscape主题有点不是很好看。然后搜了一下，发现[Next](https://github.com/theme-next/hexo-theme-next)还不错，很简单，极简美。
功能比较齐全，也有很多外部插件，最重要的是一直都有人在维护更新。

### Next主题的安装配置
1.在博客对应的路径下的theme文件夹下clone对应的主题
```sass
git clone git@github.com:theme-next/hexo-theme-next.git themes/next
```
然后在整个站点的配置文件_config.yml中修改主题
```sass
theme: next
```

其中Next提供了四中主题风格scheme，可以在主题配置文件blog/themes/next/_config.yml文件中进行选择，分别是Muse、Mist、Pisces、Gemini。我选择的是Gemini。
```sass
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
#scheme: Muse # 默认 Scheme，这是 NexT 最初的版本，黑白主调，大量留白
#scheme: Mist # Muse 的紧凑版本，整洁有序的单栏外观
scheme: Pisces # 双栏 Scheme，小家碧玉似的清新
#scheme: Gemini # 类似 Pisces

```
2.修改博客的自定义图标
对应的图标文件在 blog/themes/next/sources/images 目录下，可以在主题配置文件中进行如下配置，只需要设置small和medium两个就可以。
>favicon:
   small: /images/favicon-16x16-next.png
   medium: /images/favicon-32x32-next.png
   apple_touch_icon: /images/apple-touch-icon-next.png
   safari_pinned_tab: /images/logo.svg

### 添加代码块一键复制功能
1.在themes/next/layout/_third-party/下新建文件copy-code.swig，写入下面的内容：
```sass
{% if theme.codeblock.copy_button.enable %}
  <style>
    .copy-btn {
      display: inline-block;
      padding: 6px 12px;
      font-size: 13px;
      font-weight: 700;
      line-height: 20px;
      color: #333;
      white-space: nowrap;
      vertical-align: middle;
      cursor: pointer;
      background-color: #eee;
      background-image: linear-gradient(#fcfcfc, #eee);
      border: 1px solid #d5d5d5;
      border-radius: 3px;
      user-select: none;
      outline: 0;
    }

    .highlight-wrap .copy-btn {
      transition: opacity .3s ease-in-out;
      opacity: 0;
      padding: 2px 6px;
      position: absolute;
      right: 4px;
      top: 8px;
    }

    .highlight-wrap:hover .copy-btn,
    .highlight-wrap .copy-btn:focus {
      opacity: 1
    }

    .highlight-wrap {
      position: relative;
    }
  </style>
  
  <script>
    $('.highlight').each(function (i, e) {
      var $wrap = $('<div>').addClass('highlight-wrap')
      $(e).after($wrap)
      $wrap.append($('<button>').addClass('copy-btn').append('{{__("post.copy_button")}}').on('click', function (e) {
        var code = $(this).parent().find('.code').find('.line').map(function (i, e) {
          return $(e).text()
        }).toArray().join('\n')
        var ta = document.createElement('textarea')
        document.body.appendChild(ta)
        ta.style.position = 'absolute'
        ta.style.top = '0px'
        ta.style.left = '0px'
        ta.value = code
        ta.select()
        ta.focus()
        var result = document.execCommand('copy')
        document.body.removeChild(ta)
        {% if theme.codeblock.copy_button.show_result %}
          if(result)$(this).text('{{__("post.copy_success")}}')
          else $(this).text('{{__("post.copy_failure")}}')
        {% endif %}
        $(this).blur()
      })).on('mouseleave', function (e) {
        var $b = $(this).find('.copy-btn')
        setTimeout(function () {
          $b.text('{{__("post.copy_button")}}')
        }, 300)
      }).append(e)
    })
  </script>
{% endif %}
```

2.编辑themes/next/layout/_layout.swig文件，在文件末尾的如下位置添加一行代码：
```sass
{% include '_third-party/copy-code.swig' %}
```

3.在主题配置文件themes/next/_config.yml中开启开关
```sass
codeblock:
  # Code Highlight theme
  # Available values: normal | night | night eighties | night blue | night bright | solarized | solarized dark | galactic
  # See: https://github.com/chriskempson/tomorrow-theme
  highlight_theme: normal
  # Add copy button on codeblock
  copy_button:
    enable: true
    # Show text copy result.
    show_result: true
    # Available values: default | flat | mac
    style:
```

```
参考：
1.https://www.jianshu.com/p/2b09156ee5b1
2.https://blog.csdn.net/nightmare_dimple/article/details/86661502
3.https://afuya.blog.csdn.net/article/details/107466848
```