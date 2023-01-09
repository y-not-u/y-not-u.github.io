---
title: Jenkins Docker in Docker 部署
date: 2018-05-05 12:00:00
tags: jenkins,docker
---

通常情况，Docker作为容器级别的程序，会直接运行在高性能的宿主机上；或者宿主机上运行虚拟机，虚拟机内运行Docker容器；但是怎么会还有在Docker内运行Docker的容器这种需求呢？
<!--more-->

这种情况主要是会发生在运行于容器内的CI服务（如Jenkins）。我们需要在构建服务中使用Docker提供的功能打包代码镜像，推送到私有仓库，再到发布节点拉取镜像启动发布。

有两种解决方案：

1. 在镜像内再次安装一边Docker-CE（镜像会增大100M左右）
2. 共享宿主机的Docker进程，挂载sock和bin文件



在这边主要尝试了第二种：

网络上现有的比较简单

```shell
docker run -d \
	--name jenkins \
	-v /var/run/docker.sock:/var/run/docker.sock \
	-v $(which docker):/usr/bin/docker \
	-v /data/jenkins:/var/jenkins_home \
	-p 127.0.0.1:8080:8080 \
	jenkins:latest
```

当你进入容器中尝试运行`docker ps`时，可能发生报错：

`/usr/bin/docker: error while loading shared libraries: libltdl.so.7: cannot open shared object file: No such file or directory`

无法加载依赖文件，这是由于原Jenkins的镜像中没有安装依赖包。

查寻解决方案也是挂载文件进入容器，但可能你会发现挂载了也不起作用。



容易让人想到的是，既然缺少依赖，不如在原版Jenkins镜像上加装再打包。

```dockerfile
FROM jenkins/jenkins:lts

USER root
RUN apt-get update \
      && apt-get upgrade -y \
      && apt-get install -y sudo libltdl-dev \
      && rm -rf /var/lib/apt/lists/*
RUN echo "jenkins ALL=NOPASSWD: ALL" >> /etc/sudoers

USER jenkins
```

run起来之后，进入容器还是无法使用docker命令，Permission denied，原因是不在jenkins用户无法使用docker用户组的文件。加入用户组即可。

```shell
gourp_docker=`cut -d: -f3 < <(getent group docker)`

docker run -d --name jenkins \
	--group-add gourp_docker \
	-v /var/run/docker.sock:/var/run/docker.sock \
	-v $(which docker):/usr/bin/docker \
	-v /data/jenkins:/var/jenkins_home \
	-p 127.0.0.1:8080:8080 \
	jesusperales/jenkins-docker-run-inside
```

