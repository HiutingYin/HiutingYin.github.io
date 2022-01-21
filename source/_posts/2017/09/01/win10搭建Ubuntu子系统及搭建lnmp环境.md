---
title: win10搭建Ubuntu子系统及搭建lnmp环境
date: 2017-09-01 10:08:42
tags: PHP
categories: [环境搭建, PHP]
---

刚开始开发的时候，因为更熟悉windows系统，加之公司的电脑系统也基本上是windows，所以在环境搭建的时候，用的是VMware虚拟机安装centos，再在上面搭建对应的环境。  
用虚拟机的话就很占用内存。体验很不好。  
后面又用了集成好的wamp或者是phpstudy，但是感觉已经集成好的，虽然便捷，但是作为程序员还是需要熟悉一下基础的nginx配置等等。  
后面win10出来，有内置的ubuntu子系统，就可以直接用这个子系统直接部署基于linux的服务器环境了。（虽然有时候还是很麻烦，但是对比与虚拟机来说真的很不错了）  
下面就开始超级详细的搭建步骤。

<!-- more -->

# 开启开发者模式
系统设置 -> 更新和安全 -> 针对开发人员 -> 选择开发者模式  
![](14867887-ae25fff5199a11bc.jpg)

# 启用Linux子系统组件
系统设置 -> 应用 -> 右侧的程序和功能 -> 启动或关闭windows功能 -> 勾选适用于 Linux 的 Windows 子系统。
设置完成后重启更新即可


# 更换阿里云源
**阿里云Ubuntu 18.04源**
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse  
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse  
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse  
```

更换apt源：（先备份一下）  
```
cd /etc/apt/  
sudo cp sources.list sources.list.bak  
sudo vim sources.list
```

删除其中所有内容，替换成最上边的源内容：（vim 下删除所有行的命令 :1,$d ）

然后执行
```
sudo apt update
sudo apt upgrade
```

#  安装nginx
## 安装nginx
```
sudo apt-get install nginx
```

    在ubuntu下使用 apt-get install 或 apt install 下载安装软件，软件下载及安装后的目录
    A、下载的软件的存放位置：/var/cache/apt/archives
    B、安装后软件的默认位置：/usr/share
    C、可执行文件位置：/usr/bin
    D、配置文件位置：/etc
    E、lib文件位置：/usr/lib

## 查看nginx版本
```
sudo nginx -v
```

## 启动nginx
```
sudo /etc/init.d/nginx start
```

## 停止nginx
```
sudo /etc/init.d/nginx stop
```

## 重启nginx
```
sudo /etc/init.d/nginx restart
```

# 安装apache2
## 安装apache2
```
sudo apt-get install apache2
```

## 查看apache2版本
```
sudo apache2 -v
```

## 修改端口号
修改apache2端口号为8080（因为和nginx的端口号冲突了）
1.在文件ports.conf中修改
```
sudo vi /etc/apache2/ports.conf
```

对应文件内容修改为：
```
NameVirtualHost *:8080  #注意：Apache2.4.x版本 NameVirtualHost 已经无效，可以不设置
Listen  8080
```

2.修改default中VirtualHost
```
sudo vi /etc/apache2/sites-available/000-default.conf
```

文件第一行修改为：
```
<VirtualHost *:8080>
```

## 启动apache2
```
sudo /etc/init.d/apache2 start
```

## 停止apache2
```
sudo /etc/init.d/apache2 stop
```

## 重启apache2
```
sudo /etc/init.d/apache2 restart
```

## 可能遇到的问题

    Starting Apache httpd web server apache2     
    [Mon Jun 15 10:23:47.234556 2020] [core:warn] [pid 153] (92)Protocol not available: AH00076: Failed to enable APR_TCP_DEFER_ACCEPT
    

解决办法：
在/etc/apache2/apache2.conf加入一行
```
AcceptFilter http none
AcceptFilter https none（如果开启https）
```
重启apache，该修改可以解决某些浏览器导致apache慢或者假死不响应的情况，提高兼容性。

# 安装php7.2
```
sudo apt-get -y install php7.2
```

## 启动php-fpm
```
sudo /etc/init.d/php-fpm start   #或   sudo service php7.2-fpm start
```

# 安装mysql
```
sudo apt-get install mysql-server
sudo apt-get  install mysql-client
```
