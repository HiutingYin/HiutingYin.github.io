---
title: 使用hexo搭建博客并部署到Github Pages
date: 2022-01-21 16:28:56
tags: hexo
categories: hexo
---

原来一直用的是jekyll，但是最近不懂为啥jekyll突然崩了，编译一直失败。
自己对jekyll的语法又不是很熟悉，尝试了好几种方法都没有成功解决。就改换成hexo了。  
换成hexo之后，发现果然方便了许多。包括发布更新到github的流程。

下面是这次使用hexo搭建博客并部署到 Github Pages的步骤记录。

<!-- more -->

### 前言
一直想要有个地方记录自己在工作或者学习过程中遇到的问题。  
有次偶然得知 github pages 有提供搭建免费博客的入口。就开始了研究。  
用github pages搭建博客的话：
- 全是静态文件，访问速度快；
- 免费方便，不需要服务器不需要后台；
- 可以随意绑定自己的域名，不仔细看的话根本看不出来你的网站是基于github的；
- 数据安全，基于github的版本管理，想恢复到哪个历史版本都行；
- 博客内容可以轻松打包、转移、发布到其它平台；

### Github Pages 获取
1. 去[Github官网](https://github.com/)注册账号并登陆
2. 新建一个repo仓库，名称为 **your_name.github.io**。新建的时候下拉勾选创建README.md文件
    >比如我的用户名是 HiutingYin，那么我的repo的名称即为 HiutingYin.github.io
3. 创建完成后，等待一会，然后就可以请求 https://your_name.github.io ，就可以看到README.md中的内容了，则github page获取完成。

### Hexo搭建
#### Hexo原理
由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，所以hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。

所以这就要求我们的仓库需要有两个分支。一个分支（master）用来存放负责展示的静态网页，一个分支（hexo 或其他命名）用来备份本地的hexo文件。

#### 创建博客
1. 新建博客
新建一个网站。如果没有设置 folder ，Hexo 默认在目前的文件夹建立网站。
```sass
hexo init <folder>
cd folder
npm install
```

2.新建完成后，可以得到以下文件夹目录：
```
    .
    ├── _config.yml  #网站的配置信息
    ├── package.json #应用程序的信息
    ├── scaffolds    #模版文件夹，当新建文章时，Hexo 会根据 scaffold 来建立文件
    ├── source       #存放用户资源的文件夹
    |   ├── _drafts
    |   └── _posts
    └── themes       #主题文件夹，根据主题来生成静态页面
```
3.克隆刚刚新建的github项目到本地
```
git clone git@github.com:your_name/your_name.github.io.git
``` 

4.将刚刚新建的blog相关文件全部复制到该文件夹  
5.检查 .gitignore ， 包含以下需要忽略的内容
```sass
    .DS_Store
    Thumbs.db
    db.json
    *.log
    node_modules/
    public/
    .deploy*/
```
6.创建hexo分支并且切换到该分支
```sass
git checkout -b hexo
```

    master分支：负责展示静态网页
    hexo分支：备份本地hexo文件
    
7.提交分支内容至github
```sass
git add --all
git commit -m "blog init"
git push -u origin hexo
```
后续分支更新的话：
- master分支更新
```sass
hexo d
```

- hexo分支更新  
```sass
git add . #添加所有文件到暂存区
git commit -m "新增文章"  #提交
git push origin hexo #推送hexo分支到Github
```

- 新电脑环境下的恢复，安装hexo以及相关依赖，克隆hexo分支到本地
```sass
git clone -b hexo git@github.com:your_name/your_name.github.io.git
```

#### hexo常用指令
|  指令   | 简写指令  |  指令说明    |
|  ----  | ----  |----   |
| hexo server  | hexo s |Hexo 会监视文件变动并自动更新，除修改站点配置文件外,无须重启服务器,直接刷新网页即可生效。|
| hexo server -s  | hexo s -s |以静态模式启动|
| hexo server -p 5000  | hexo s -p 5000 |更改访问端口 (默认端口为4000，'ctrl + c'关闭server)|
| hexo clean  | - |清除缓存文件 (db.json) 和已生成的静态文件 (public)|
| hexo generate  | hexo g |生成静态文件|
|hexo deploy |hexo d|将本地数据部署到远端服务器|
|hexo new "第一篇文章"|-|生成文章|


```
参考：
1.https://www.jianshu.com/p/2b09156ee5b1
2.https://blog.csdn.net/qq_45271256/article/details/105800705
3.https://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html
```