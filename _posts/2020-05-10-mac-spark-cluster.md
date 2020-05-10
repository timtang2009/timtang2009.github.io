---
layout: post
title: "Build spark cluster with docker on mac"
categories:
  - Edge Case
tags:
  - spark
  - docker
last_modified_at: 2020-05-10T14:25:52-05:00
---

本地通过docker部署spark集群
===========


- [1. 下载docker脚本](#1-下载docker脚本)
- [2. 更改spark版本](#2-更改spark版本)
- [3. 运行建立镜像指令](#3-运行建立镜像指令)
- [4. 启动集群](#4-启动集群)

## 前言
本文用docker-spark官方的方式安装本地docker容器中启动spark集群的方法。1master, **任意**workers! 如果是3个worker，占用内存约2G。也可以自己调整资源占用。如官方文档所说：

![](https://jewelry-recognize.oss-cn-shenzhen.aliyuncs.com/uploads/c329c53188138de1e92630eb1eb15e541.jpg)


前置步骤：1. 需要在本机安装docker。mac用户可在docker官网直接下载docker并安装，非常方便。windows用户需要通过docker machine来安装，详情可以看我另一篇博客 [Windows用户配置开发环境指南](https://timtang2009.github.io/edge%20case/2017/10/04/windows-development.html)。 2. 熟悉docker的基本指令 3. 安装docker-compose 安装指令如下：

```
sudo pip3 install docker-compose
```

## 1. 下载docker脚本
```
git clone https://github.com/mvillarrealb/docker-spark-cluster.git
```

## 2. 更改spark版本
原版本的spark版本（2.4.3）已在其镜像源中下架，目前稳定版本为2.4.5，因此需要修改其默认的spark版本。
在代码的./docker/base/Dockerfile中，找到SPARK_VERSION，改成如下

![](https://jewelry-recognize.oss-cn-shenzhen.aliyuncs.com/uploads/ab190e83fbb1317358f623781672537f2.jpg)



## 3. 运行建立镜像指令

```
chmod +x build-images.sh
./build-images.sh
```
![](https://jewelry-recognize.oss-cn-shenzhen.aliyuncs.com/uploads/81c43ae76c1e16f78b8a117eb30676bf3.jpg)

根据电脑配置不同安装时间会有差异。我的老款高配Mac大约跑了5分钟左右，耐心等待。

安装完成的界面如下：
![](https://jewelry-recognize.oss-cn-shenzhen.aliyuncs.com/uploads/8210cde4d8d02ab6b299666354e5e8494.jpg)

## 4. 启动集群

```
docker-compose up --scale spark-worker=5
```
其中这个5是可以任意调整，你懂的

启动成功后访问localhost:9090(mac)，如果是windows访问localhost:8080，就会出现spark集群管理界面：
![](https://jewelry-recognize.oss-cn-shenzhen.aliyuncs.com/uploads/af71c14329bf376f365ad7e3ae7cf8b55.jpg)


