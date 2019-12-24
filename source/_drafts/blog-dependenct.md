---
title: 依赖属性基础巩固
tags: WPF
categories: ['知识补丁']
---

依赖属性是何物？

>Windows Presentation Foundation (WPF) 提供一组服务，这些服务可用于扩展类型的[属性](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/common-type-system#Properties)的功能。 这些服务通常统称为 WPF 属性系统。 由 WPF 属性系统支持的属性称为依赖属性

<!--more-->

简单的来说就是在`WPF`中有别于面向对象中普通属性是其专属属性，依赖于`WPF属性系统`，对于依赖属性，并不像普通类类型属性那般进行实例化操作，而是依靠`DependencyProperty`进行类型函数建立静态实例，这样又可以说依赖属性为`DependencyProperty`支持的属性，依赖属性的实际声明和使用过程如下：

1. 生成依赖属性类
2. 声明静态只读属性名称
3. 进行依赖属性注册
4. 构建依赖属性封装器

为什么引入依赖属性？



好处与弊端

常见使用方式



