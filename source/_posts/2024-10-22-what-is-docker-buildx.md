---
title: Docker Buildx 是什么？
date: 2024-10-22 13:34:45
tags:
   - docker
---

有时网络冲浪看到文章说 docker build 传统方式在不久的将来会被废弃时，我感到莫名其妙。不过官方同时建议更换 docker buildx 来替代。此时我与你一样有疑惑。

Docker Buildx 首次出现在 19.03 版本中，于 2019 年 7 月发布。距离今日已经过去5年，我现在才感觉到它的存在。但平常可能不经意间已经受益于它的一些特性。

![cover](cover.webp)

<!--more-->

## Docker Build 不好吗？

在过去的开发环境中，当然是足够好的。但原因是来自于人性的贪婪。没错，我们想要更多更好的特性。很不幸，原来的架构不支持，只好放弃。

## Docker Buildx 带来了什么？

能实现更复杂更现代化开发需求的功能：

1. **生成多平台编译**：可以在一次打包编译命令输出 arm/x86 等多平台的镜像
2. **增强缓存：**自动跳过未使用的构建阶段，优化使用修改文件的上下文
3. **并行构建：**并行构建独立的阶段，加快编译速度
4. **支持更高级的 Dockerfile 语法**
5. …

## 如何使用 Docker Buildx

Docker Desktop、Docker Engine 大于 23.0 版本已经默认启用。

Docker Buildx 是交互客户端，而背后是全新的 BuildKit 提供了实际编译构建的服务进程。在你执行 `docker build` 时已经在使用 Buildx。Buildx 会解析用户传入的命令参数，并发送给后端 BuildKit 执行。（所以从另一个角度讲，BuildKit 才是核心功能的实现方。不使用 Buildx 也可以，选用其他支持的客户端同样能运用 BuildKit 的新特性。）

### 如何验证你是否安装了 BuildKit

```bash
docker buildx version
```

### 如何手动启用

在 Docker Engine 23.0 之前的需要手动开启该功能

1. 使用环境变量
    
    `DOCKER_BUILDKIT=1 docker build .` 
    
2. 编辑 Docker daemon 配置文件
    
    配置文件路径在：`/etc/docker/daemon.json`
    
    ```json
    {
    	"features": {
    		"buildkit": true
    	}
    }
    ```
    
    最后重启 Docker
    
    `sudo systemctl restart docker`
    

## 重要特性一探

### 生成多平台镜像

使用 `--platform` 参数来生成目标镜像：

```bash
docker buildx build --platform linux/amd64,linux/arm64
```

### 并行构建

传统的 build 会顺序依次执行构建命令，这样花费的时间非常长，没有优化方案。但是 BuildKit 则会分析相关性和依赖性。如果不同的 stage 阶段没有相互依赖，则会同时构建。大大缩短构建时间。

```docker
FROM ubuntu:bionic as base-builder

ENV GOPATH=$HOME/go
ENV PATH=$PATH:/usr/local/go/bin:$GOPATH/bin

RUN apt-get update \
    && apt-get install -y curl git build-essential

FROM base-builder as base-builder-extended
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g yarn

FROM base-builder as golang
RUN curl -O https://storage.googleapis.com/golang/go1.15.2.linux-amd64.tar.gz \
    && tar -xvf go1.15.2.linux-amd64.tar.gz

FROM base-builder as source-code
RUN git clone https://github.com/prometheus/prometheus.git prometheus/

FROM base-builder-extended as builder
COPY --from=golang go /usr/local
COPY --from=source-code prometheus/ prometheus/
RUN cd prometheus/ && make build

FROM ubuntu:bionic as final
COPY --from=builder prometheus/prometheus prometheus
# RUN ./prometheus --config.file=your_config.yml
```

可以看到 `base-builder-extended` 和 `golang` 以及 `source-code` 是相互没有依赖的 stage，所以构建时会同时执行。

## 结语

Docker 已经大大改变了现代软件工程的开发构建方式，对运维部署则是完全革命性的工具。随着网络世界的进一步发展，Docker 也在跟进，从而变得更现代化能够适应各类状况。
