---
title: 使用docker搭建dnmp环境
date: 2022-03-18 15:42:42
tags: docker
categories: [读书笔记, docker]
---

dnmp（Docker + Nginx + MySQL + PHP7/5 + Redis）是基于docker的lnmp一键安装程序。

因为目前的搭建环境为win10上使用docker，所以
首先在win10里面创建文件夹来存放 nginx/php/mysql 的配置及日志文件
```shell script
E:\docker\conf\nginx-conf
E:\docker\conf\nginx-log
E:\docker\conf\php-conf
E:\docker\conf\php-log
E:\docker\conf\mysql-conf
E:\docker\conf\mysql-log
```

# 安装mysql
1.拉取mysql镜像
```shell script
docker pull mysql:5.7
```
2.创建mysql容器
```shell script
docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=rxt123456 --name mysql-5.7 mysql:5.7
```
参数说明：  
-d 让容器在后台运行  
-p 添加主机到容器的端口映射  
-e 设置环境变量，这里设置mysql的root用户的初始密码  
-name 容器的名称、只要求唯一性  

3.进入容器
```shell script
docker exec -ti mysql-5.7 /bin/bash  
```
参数说明：  
exec:                                     Run a command in a running container(在运行的容器中运行命令)  
exec -i:  --interactive(相互作用的)       Keep STDIN open even if not attached(即使没有连接，也要保持STDIN打开)  
exec -t:   --tty                          Allocate a pseudo-TTY(分配一个 冒充的终端设备)  

执行 **apt update** ，安装vim编辑器
```shell script
apt update  
apt-get install vim  
```

4.因为mysql配置文件路径是 /etc/mysql/conf.d ，把宿主机上的配置映射到容器里
```shell script
docker run -d -v E:/docker/conf/mysql-log:/var/log/mysql -v E:/docker/conf/mysql-conf:/etc/mysql/conf.d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=rxt123456 --name mysql-5.7 mysql:5.7  
```

# 安装php
1. 获取php7.2镜像
```shell script
docker pull php:7.2-fpm
```
2.创建对应phpfpm容器
```shell script
docker run -d -v F:/www:/var/www/html -p 9000:9000 --link mysql-5.7:mysql --name php-7.2 php:7.2-fpm  
```
参数说明：  
-v 添加目录映射
--link 与另外一个容器建立起联系 

3.进入容器，安装对应扩展
```shell script
docker exec -ti php-7.2 /bin/bash


docker-php-ext-install pdo_mysql  
docker-php-ext-install mysqli 
docker-php-ext-install memcache 
```


# 安装nginx
1.获取nginx镜像
```shell script
docker pull nginx:1.15.11
```
2.创建并启动容器（添加对应目录映射）
```shell script
docker run -d -p 80:80 -v F:/www:/var/www/html --link php-7.2:phpfpm --name nginx-1.15.11 nginx:1.15.11
```
3.修改配置文件
```shell script
docker exec -it nginx-1.15.11 /bin/bash

vim /etc/nginx/conf.d/default.conf
```