---
layout: post
title: "Tools For Developers on Windows Platform"
categories:
  - Edge Case
tags:
  - tools
  - windows
last_modified_at: 2017-10-04T14:25:52-05:00
---

Windows用户配置开发环境指南
===========


- [1. 安装MSYS2](#2-安装MSYS2)
- [2. 安装cmder](#3-安装cmder)
- [3. pacman](#4-pacman)
- [4. 安装开发工具](#5-安装开发工具)
- [5. 其他需要安装的工具](#6-其他需要安装的工具)
- [6. pull代码](#7-pull代码)
- [7. 安装并配置docker](#8-安装并配置docker)
- [8. 配置本地mysql](#9-配置本地mysql)

## 前言
介绍一些本人在windows平台下开发所使用的工具，包括好用的命令行，类linux环境，数据库及docker工具等。注意，本文用到的所有工具都是免费且正版的。

## 1. 安装MSYS2
安装在C盘根目录, 打开MSYS2 MSYS终端，然后运行
```
cd /msys64/home
rd /s /q %USERNAME%
mklink /j %USERNAME% %USERPROFILE%
```
安装完成后需要更新msys2的镜像源，修改msys2安装目录下的\etc\pacman.d文件夹里面的3个mirrorlist文件
mirrorlist.mingw32：
```
##
## 32-bit Mingw-w64 repository mirrorlist
##

## Primary
Server = http://mirrors.ustc.edu.cn/msys2/REPOS/MINGW/i686
## msys2.org
Server = http://repo.msys2.org/mingw/i686
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MINGW/i686
Server = http://www2.futureware.at/~nickoe/msys2-mirror/i686/
```
mirrorlist.mingw64：
```
##
## 64-bit Mingw-w64 repository mirrorlist
##

## Primary
Server = http://mirrors.ustc.edu.cn/msys2/REPOS/MINGW/x86_64
## msys2.org
Server = http://repo.msys2.org/mingw/x86_64
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MINGW/x86_64
Server = http://www2.futureware.at/~nickoe/msys2-mirror/x86_64/
```
mirrorlist.msys:
```
##
## MSYS2 repository mirrorlist
##

## Primary
Server = http://mirrors.ustc.edu.cn/msys2/REPOS/MSYS2/$arch
## msys2.org
Server = http://repo.msys2.org/msys/$arch
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MSYS2/$arch
Server = http://www2.futureware.at/~nickoe/msys2-mirror/msys/$arch/
```

## 2. 安装cmder（推荐）
安装在C盘根目录
安装完成后打开cmder，settings -> Main -> Tab bar -> 取消勾选Tabs on bottom
Settings -> startup -> Tasks -> 左下角+添加predefined tasks（添加2个，内容如下）
1)	title: zsh::mintty as Admin
content: C:\msys64\usr\bin\mintty.exe /bin/zsh -l -new_console:d:%userProfile%
2)	title: zsh::minty
content: C:\msys64\usr\bin\mintty.exe /bin/zsh -l -new_console:d:%userProfile%
后面所有的操作默认情况下都在zsh::minty命令行下执行（cmder顶端右键new console选择zsh::minty）

## 3. pacman
在MSYS2 MSYS终端中

用pacman安装一系列插件：
```
pacman –Sy git vim zsh tmux tar patch ed winpty man zip unzip
```

安装oh my zsh（推荐）：
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## 4. 安装开发工具
安装JDK，groovy, maven等开发工具并配置环境变量（在vim ~/.zshrc中配置，下文中也一样）

## 5. 其他需要安装的工具
其他需要安装的工具：intellij idea, atom, virtualbox, dockerTool(dt.exe), visualcppbuildtools_full, vagrant, dbvisualizer, openvpn, jq node，并且在~/.zshrc中配置变量；将bin文件夹放置在C盘根目录并配置变量

需要前端开发请安装：
chrome, firefox, postman, fiddler, fiddler syntax editor

## 6. pull代码并编译
Pull代码，如要配置maven的settings.xml，可以：
```
cp ~/ cloud-pay2/settings.xml ~/.m2/ 并mvn编译
```
或每次编译时使用-s settings.xml参数

编译代码：
```
mvn
mvn install
```
本地起程序：
```
java -jar host/host-homesite/target/cloud-homesite-1.0.0.war
```

## 7. 安装并配置docker
1）确认在第5步中dockerTool已正确安装(docker --version)

2）运行建立虚拟机
```
docker-machine create --driver virtualbox --virtualbox-cpu-count 2 --virtualbox-disk-size 200000 --virtualbox-memory 2048 default
```
3）启动docker machine：
```
docker-machine start
```
4）zsh中运行
```
for i in `docker-machine env --shell cmd | grep "^SET" | cut -d\  -f2`; do export $i; done
```
如果是cmd运行：
```
FOR /f "tokens=*" %i IN ('docker-machine env') DO @%i
```
5）拉取alpine
```
docker pull hub.c.163.com/library/alpine:latest
docker tag hub.c.163.com/library/alpine:latest alpine
```
6）运行docker images查看此时是否正确pull了alpine，预期结果会显示    
alpine                         latest              baa5d63471ea        7 weeks ago         4.803 MB

7）在cloud-pay2/docker目录下, 分别进入各个子目录中（如cloud-pay2/docker/base）执行
```
docker build –t $(basename `pwd`) .
```
注意glibc和java在最后执行，期间可能会因为网络问题而install失败，多试几次即可

8）执行完毕后执行docker images查看是否正确install

## 8. 配置本地mysql

打开DBVisualizer，建立localhost:3306,用户名为root，无密码的数据库connection

打开virtualbox中正在运行的名为default的虚拟机，在顶部设备->网络->网络->（网卡1）高级->端口转发
添加转发规则如下：
名称：mysql     协议: TCP      主机端口: 3306    子系统端口：3306 其余为空

配置指向本地的连接：
```
docker run -d -p 3306:3306 --add-host localhost:10.0.2.2 mysql
```
在DBVisualizer里尝试连接数据库

在DBVisualizer里新建pay和website数据库（create database pay; create database website;）
分别在cloud-pay2/external/mysql-pay及cloud-pay2/external/mysql-website中运行
```
mvn flyaway:migrate
```
运行成功后即可在pay和website库中看见表
