---
layout: post
title:  WordPress 搭建
category: it
copyright: it
tags: [life]
excerpt: 感受下 WP 的新鲜，但遗憾的就是尝试，没有投入使用。
---



如果你没有使用过容器，还是得了解下这门强大的技术带给我们的便利性，这里简单介绍下它的概念：

- **Docker**：是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口

- **Docker-compose**：是 Docker 的一种编排服务，是一个用于在 Docker 上定义并运行复杂应用的工具，可以让用户在集群中部署分布式应用。
- ***kubernetes***： 是Google开源的一个容器编排引擎，它支持自动化部署、大规模可伸缩、应用容器化管理。在生产环境中部署一个应用程序时，通常要部署该应用的多个实例以便对应用请求进行负载均衡。

好了，如果你不了解也没关系，只要在机器上面按照下面步骤搭建就可以了，你会发现很神奇。



## 一、WORDPRESS 搭建

### 1.基础环境

- linux 环境 （这里我使用的 [阿里云虚拟主机](https://www.aliyun.com/minisite/goods?userCode=stms6wkr&share_source=copy_link)）
- 安装 Docker，Docker-compose



### 2.安装 Docker 系列

#### 2.1 安装 docker

```shell
#查看你当前的内核版本
uname -r 
#更新yum
yum update 
#安装 docker
yum -y install docker 
#启动 docker 服务
systemctl start docker.service
#查看 docker版本
docker version 
```

#### 2.2 安装 Docker-compose

```shell
#安装 docker-compose
yum install docker-compose 
#查看版本
docker-compose version 
```



### 3.安装 Wordpress

创建 *wordpress-docker-compose.yml*

```yml
version: '3.3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: wordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "2021:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

执行命令：

```shell
#后台运行
docker-compose -f wordpress_docker-compose.yml up -d 
#停止并删除服务
docker-compose -f wordpress_docker-compose.yml down 
```

访问地址：`IP:2021`

> 注：如果无法访问，腾讯云/阿里云，记得开启 *2021* 端口。



如上就安装完成了，是不是很简单，最后成功后就可以看到下面的画面了：

![image-20210319135047829-tuya](E:\2020-new-life\images\image-20210319135047829-tuya.png)



参考文章：

​		[Docker 安装 Wordpress 博客](https://juejin.cn/post/6844904067227975693)