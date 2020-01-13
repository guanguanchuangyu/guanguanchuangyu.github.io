---
title: WPF常见问题及解决方案
tags: ['WPF']
categories: ['知识补丁']
typora-root-url: ./
---

# HandyControlAnswer

###### `WPF`细节问题

**字体图标的使用**

如何`引入字体图标`不再赘述，可参考：[WPF引入字体图标](https://blog.csdn.net/songyi160/article/details/54894233)，引入字体时，需要注意的是，如果希望字体通过后台或者后端进行传递字体字符串时，对应字体的参数格式会有所不同，此处以阿里巴巴开源字体库为例，参考链接[WPF使用阿里巴巴字体库](https://www.iconfont.cn/)

`xaml`中使用如下：

```xml
<StackPanel Orientation="Horizontal">
    <StackPanel.Resources>
        <!--仅仅用于演示-->
        <!--引入字体库,路径#字体库名称-->
        <FontFamily x:Key="iconfont">pack://application,,,/所在命程序集名称;Component/Resources/#iconfont</FontFamily>
    </StackPanel.Resources>
        <TextBlock Text="&#xe8ac;           &#xe8ab;" FontSize="30" Margin="10" 
               FontFamily="{StaticResource iconfont}" 
               HorizontalAlignment="Center" 
               VerticalAlignment="Center"></TextBlock>
</StackPanel>
```

效果：

![bugthink-xamlIcon](https://file.budbud.cn/ggcyblog/bugthink/bugthink-xamlIcon.png)

在`.cs`文件中通过代码设定字体图标，需要将`&#xe8ac;`中的`&#x`替换为`\u`以及去掉尾部`;`：

```c#
private string strIcon;
/// <summary>
/// 页面中使用的属性
/// </summary>
public string StrIcon
{
    get
    {
        //模拟后台给对应属性赋值
        if (string.IsNullOrEmpty(strIcon))
        {
            strIcon = "\ue8ac           \ue8ab";
        }
        return strIcon;
    }
    set { strIcon = value; }
}
```

`xaml`文件中使用对应的属性：

```xml
<!--忽略重复代码--> 
<TextBlock Text="{Binding StrIcon}" FontSize="30" Margin="10" FontFamily="{StaticResource iconfont}" 
Foreground="YellowGreen"
HorizontalAlignment="Center"
VerticalAlignment="Center"></TextBlock>
<!--忽略重复代码--> 
```

效果：

![bugthink-codeIcon](https://file.budbud.cn/ggcyblog/bugthink/bugthink-codeIcon.png)

前端`xaml`中支持使用的是`&#+Unicode码;`字体对应的字符实体，而在`.cs`中则是使用的是对应的`\u+Unicode码`十六进制结果，具体可查阅资料[WPF中显示UniCode字符](https://www.bbsmax.com/A/xl561Zo9Jr/)

**外部资源的引入**

在`WPF`项目中，作为系统嵌入引用程序集中资源，例如资源字典文件、字体库、图片等等，编译生成后，直接嵌入到应用程序集中，如果希望在`xaml`中使用又不希望资源嵌入，而是希望随时能过替换应用程序所在目录下的外部资源文件如何去处理？

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

**`Popup`、`Contextmenu` 绑定资源为`ElementName`失效**



**`Converter`返回内置画刷页面失效**



**`List<T>`和`ObservableCollection<T>`**

时常会遇到，作为实体集合，希望对数据集合子项的操作响应到页面的`TreeView`的节点上，例如：希望通过删除数据集合中的某一项，数据变更能够通知到`UI`中的`TreeView`的对应节点，实现移除。`List<T>`对应的数据子项操作无法通知到`TreeView`，而`ObservableCollection<T>`可行，案例如下：



###### `HandyControl`使用技巧

`HC`自定义Window非用户区域操作无效

###### `HandyControl`与兼容

`LiveCharts`和`HandyControl`关于`Path`的样式冲突

###### 其他问题

如何不被问题解决

啥时候写完说明文档

