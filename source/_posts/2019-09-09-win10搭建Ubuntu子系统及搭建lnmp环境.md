---
title: win10搭建Ubuntu子系统及搭建lnmp环境
author: HiuTingYin
math: true
mermaid: true
date: 2020-09-09 10:55:00 +0800
categories:
- 环境搭建
tags: 
- PHP
excerpt:  win10搭建Ubuntu子系统及搭建lnmp环境 详细教程
---

#开启开发者模式
系统设置 -> 更新和安全 -> 针对开发人员 -> 选择开发者模式
![](https://upload-images.jianshu.io/upload_images/14867887-ae25fff5199a11bc.png?imageMogr2/auto-orient/strip|imageView2/2/w/606/format/webp)

#启用Linux子系统组件
系统设置 -> 应用 -> 右侧的程序和功能 -> 启动或关闭windows功能 -> 勾选适用于 Linux 的 Windows 子系统。
设置完成后重启更新即可


#更换阿里云源
**阿里云Ubuntu 18.04源**
>deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

>deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

>deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

>deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

>deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

>deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse

>deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse

>deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse

>deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

>deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse

更换apt源：（先备份一下）  
> cd /etc/apt/

> sudo cp sources.list sources.list.bak 

> sudo vim sources.list

删除其中所有内容，替换成最上边的源内容：（vim 下删除所有行的命令 :1,$d ）

然后执行
> sudo apt update

> sudo apt upgrade


# 安装nginx
##### 安装nginx
>sudo apt-get install nginx

    在ubuntu下使用 apt-get install 或 apt install 下载安装软件，软件下载及安装后的目录
    A、下载的软件的存放位置：/var/cache/apt/archives

    B、安装后软件的默认位置：/usr/share

    C、可执行文件位置：/usr/bin

    D、配置文件位置：/etc

    E、lib文件位置：/usr/lib

##### 查看nginx版本
>sudo nginx -v

##### 启动nginx
>sudo /etc/init.d/nginx start

##### 停止nginx
>sudo /etc/init.d/nginx stop

##### 重启nginx
>sudo /etc/init.d/nginx restart

# 安装apache2
##### 安装apache2
>sudo apt-get install apache2

##### 查看apache2版本
>sudo apache2 -v

##### 修改apache2端口号为8080（因为和nginx的端口号冲突了）
1.在文件ports.conf中修改
>sudo vi /etc/apache2/ports.conf

对应文件内容修改为：
>NameVirtualHost *:8080  #注意：Apache2.4.x版本 NameVirtualHost 已经无效，可以不设置
>Listen  8080

2.修改default中VirtualHost
>sudo vi /etc/apache2/sites-available/000-default.conf

文件第一行修改为：
><VirtualHost *:8080>

##### 启动apache2
>sudo /etc/init.d/apache2 start

##### 停止apache2
>sudo /etc/init.d/apache2 stop

##### 重启apache2
>sudo /etc/init.d/apache2 restart

#### 可能遇到的问题
```
 * Starting Apache httpd web server apache2     
 [Mon Jun 15 10:23:47.234556 2020] [core:warn] [pid 153] (92)Protocol not available: AH00076: Failed to enable APR_TCP_DEFER_ACCEPT
 *
```

解决办法：

在/etc/apache2/apache2.conf加入一行
```
AcceptFilter http none
AcceptFilter https none（如果开启https）
```
重启apache，该修改可以解决某些浏览器导致apache慢或者假死不响应的情况，提高兼容性。

# 安装php7.2
>sudo apt-get -y install php7.2

#### 启动php-fpm
>sudo /etc/init.d/php-fpm start   #或   sudo service php7.2-fpm start

# 安装mysql
>sudo apt-get install mysql-server
>sudo apt-get  install mysql-client
