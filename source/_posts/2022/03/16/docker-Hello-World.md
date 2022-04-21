---
title: docker-Hello World
date: 2022-03-16 15:35:19
tags: docker
categories: [读书笔记, docker]
---

# 使用docker镜像

Docker运行容器之前，需要本地存在对应的镜像，如果镜像不存在，Docker会尝试先从默认镜像仓库下载（默认使用 **Docker Hub** 公共注册服务器中的仓库）,用户也可以通过配置，使用自定义的镜像仓库。
- Docker [image] pull [registry/]NAME[:TAG]
  - 如果不显示指定registry，则默认使用registry.hub.docker.com （，国内连接 Docker 的官方仓库很慢，还会断线，需要将默认仓库改成国内的镜像网站。）
  - 如果不显示指定TAG，则会默认选择latest标签，这会下载仓库中最新版本的镜像（一般在生产环境不要使用latest标签，因为最新版本往往比较不稳定）


- 修改方法（windows）：  
在系统右下角托盘图标内右键菜单选择 Settings，打开配置窗口后左侧导航菜单选择 Docker Engine。编辑窗口内的JSON串，填写下方加速器地址：
```json
{
  "registry-mirrors": ["https://jx6ljjsm.mirror.aliyuncs.com"]
}
```
编辑完成后点击 Apply 保存按钮，等待Docker重启并应用配置的镜像加速器。

做程序员，不管新学什么东西，都要通过一个最简单的“hello world"。
下面通过最简单 image 文件“hello world"，感受一下docker

## 获取镜像
```shell script
$ docker image pull library/hello-world
```

上面代码中，**docker image pull**是抓取 image 文件的命令。**library/hello-world**是 image 文件在仓库里面的位置，其中**library**是 image 文件所在的组，**hello-world**是 image 文件的名字。

由于 Docker 官方提供的 image 文件，都放在**library**组里面，所以它的是默认组，可以省略。因此，上面的命令可以写成下面这样。
```shell script
$ docker image pull hello-world
```

抓取成功以后，就可以在本机看到这个 image 文件了。
```shell script
$ docker image ls
```
现在，运行这个 image 文件。
```shell script
$ docker container run hello-world
```

**docker container run**命令会从 image 文件，生成一个正在运行的容器实例。

注意，**docker container run**命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。因此，前面的**docker image pull**命令并不是必需的步骤。

如果运行成功，你会在屏幕上读到下面的输出。
```shell script
Hello from Docker!
This message shows that your installation appears to be working correctly.

... ...
```

输出这段提示以后，**hello world**就会停止运行，容器自动终止。

有些容器不会自动终止，因为提供的是服务。比如，安装运行 Ubuntu 的 image，就可以在命令行体验 Ubuntu 系统。
```shell script
$ docker container run -it ubuntu bash
```

对于那些不会自动终止的容器，必须使用**docker container kill**命令手动终止。
```shell script
$ docker container kill [containID]
```