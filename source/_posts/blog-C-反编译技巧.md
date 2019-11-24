---
title: 'C#-查看微软内置程序集技巧'
tags: 'C#'
categories: 
  - 相见恨晚
typora-root-url: blog-C-反编译技巧
date: 2019-11-24 22:35:28
---


`Vs2019`提供了反编译查看的内置功能，本来高高兴兴，使用时间一长，自己渐渐不满足，需要更好的体验，找到一种新的方式，倍加感叹，相见恨晚

<!--more-->

通过开启`Vs2019`反编译功能，本以为万事大吉，再也不用担心，自己看不到源码的困扰，事实证明我想多了，选中需要反编译的类或者函数，`F12`进入其中，如下：

`F12`

![skill-case01](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-C-%E5%8F%8D%E7%BC%96%E8%AF%91%E6%8A%80%E5%B7%A7/skill-case01.png)

再次查看

![skill-case01-01](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-C-%E5%8F%8D%E7%BC%96%E8%AF%91%E6%8A%80%E5%B7%A7/skill-case01-01.png)

这就难受了，反编译查看只能看到半截

这类类型，无法通过`Vs2019`自带的反编译工具查看到底层的源码，虽然说上层源码也能够猜测出部分功能，但是要是能够直接看到接下来的源码，当然更好，于是，找老哥，询问到一个在线源码链接[referencesource:](https://referencesource.microsoft.com/)

![skill-refrencesource](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-C-%E5%8F%8D%E7%BC%96%E8%AF%91%E6%8A%80%E5%B7%A7/skill-refrencesource.png)

输入对应的需要查询的内容，如`ItemStructList`

![skill-refrencesource-use](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-C-%E5%8F%8D%E7%BC%96%E8%AF%91%E6%8A%80%E5%B7%A7/skill-refrencesource-use.png)

满意，个人非常满意，这个站点的具体细节需要进一步去探索