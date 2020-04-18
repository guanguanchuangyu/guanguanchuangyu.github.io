---
title: IIS服务启动提示当文件已存在时，无法创建该文件，183
tags:
  - IIS
categories:
  - 江湖秘籍
date: 2020-04-18 12:11:40
---


遇到一个在`Windows Server2012 R2`系统，`IIS`服务器启动问题，无论如何尝试启动`IIS`，始终提示“当文件已存在时，无法创建该文件”，一个令人懵比的问题，文件已存在，哪里存在了？不知道，就只能够盲人摸象，一步步排查，开始填坑，此处以`Win10`作为问题复现环境

<!--more-->

# 填坑记录

## 作死流程

系统中安装好`IIS`，在未做任何修改之前，`IIS`能够正常启动和访问，因为个人需要对`C:\Windows\System32\inetsrv\config\schema\IIS_schema.xml`文件进行部分修改配置，由于涉及到系统，所有，一般在修改前会习惯性的对文件保留一个副本，于是出现如下结构目录

![blog-filelist-info](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-filelist-info.png)

## 意外出现

关闭`IIS`后，再次启动，出现如下错误：

![blog-error-info](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-error-info.png)

这是啥问题？于是将之前修改过的变更，进行还原，`万能重启`，还是不行，`IIS`这就崩了？心中无数头羊驼，在奔腾~，又不想重装系统，喝口热茶，缓解一下紧张气氛，接着找问题

## 问题排查

通过查阅网上资料可以知道，`IIS`正常启动依托至少两个服务，一个是`W3SVC`也就是`World Wide Web Publishing Service`，一个是`WAS`也就是`Windows Process Activation Service`，查找到这两个服务，发现当前两个服务都未能启动，先尝试`W3SVC`，启动提示“服务依赖或者工作组异常”，查看服务属性，其中的依赖，一个个核对

### 服务停止

![blog-service-stop](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-service-stop.png)

### 启动提示

启动`W3SVC`服务出现`依赖服务或组无法启动`，修改之前`IIS`是能够正常使用的，说明组件是完整的，需要查看服务的依赖服务或者相关组是否启动

![blog-service-reset](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-service-reset.png)

### 查看依赖

打开本地服务，在服务列表中找到`World Wide Web Publishing Service`，查看对应的属性信息，具体信息如下：

![blog-service-info](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-service-info.png)

发现`W3SVC`的依赖项其中之一是`WAS`，其余依赖性都已经启动，唯独`WAS`未启动，因而，异常问题定位到`WAS`服务未启动上，尝试启动，提示信息与启动`IIS`启动时保持一致

![blog-was-reset](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-was-reset.png)

# 原因与总结

##  原因

将对应的服务名称和错误信息，进行浏览器搜索，得到一个解决方案，此处为传送门[Windows Process Activation Service无法启动，错误183](https://zhidao.baidu.com/question/1175774999664015139.html)，虽然是描述较少，好在问题和本人的问题是一模一样，网友自问自答，也是很好的，在`inetsrv\config\schema`文件夹下，所有文件不能存在备份文件，之前为了保留原有文件，进行文件副本的保留，造成服务启动过程中，运行异常，而`WAS`又是`W3SVC`的依赖服务，故而，两个服务都无法启动运行，删除掉`C:\Windows\System32\inetsrv\config\schema\IIS_schema-副本.xml`文件

![blog-copy-delete](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-copy-delete.png)

依次启动`WAS`和`W3SVC`服务，能够正常启动

![blog-service-right](https://file.budbud.cn/ggcyblog/blog-iis-intelsrv/blog-service-right.png)

再次启动`IIS`就能够正常使用了，免于系统的重装，当然，环境也保住了

## 总结

因为自己保留原有文件的处理措施，给自己带来了两个小时的排错排查，实属填坑，没有想到`IIS`的相关服务会出现这样的问题，同时对服务依赖的异常排查有了一个更加深入的了解，积累成为接触疑难杂症的经验

如果你也是技术爱好者，可以关注一波~~

![WXGZ](https://file.budbud.cn/ggcyblog/images/WXGZ.jpg)