---
title: 外部资源的引入
tags: ['WPF']
categories: ['知识补丁']
---

在`WPF`项目中，作为系统嵌入引用程序集中资源，例如资源字典文件、字体库、图片等等，编译生成后，直接嵌入到应用程序集中，如果希望在`xaml`中使用又不希望资源嵌入，而是希望随时能过替换应用程序所在目录下的外部资源文件如何去处理？

<!--more-->

使用`Pack URI`中的外部方式[源站点 Pack URI](https://docs.microsoft.com/zh-cn/dotnet/framework/wpf/app-development/pack-uris-in-wpf#site-of-origin-pack-uris)，`pack://siteoforigin:,,,/XXXXXXXXX`，详情请查看链接，案例如下：

`xaml`中：

```xaml
<Border Margin="10">
    <Border.Background>
        <ImageBrush ImageSource="pack://siteoforigin:,,,/bg.png"></ImageBrush>
    </Border.Background>
</Border>
```

文件路径：

![bugthink-packpath](https://file.budbud.cn/ggcyblog/bugthink/bugthink-packpath.png)

效果：

原图：

![bugthink-packuri](https://file.budbud.cn/ggcyblog/bugthink/bugthink-packuri.png)

替换后：

![bugthink-packpath-new](https://file.budbud.cn/ggcyblog/bugthink/bugthink-packpath-new.png)

![bugthink-packuri-new](https://file.budbud.cn/ggcyblog/bugthink/bugthink-packuri-new.png)