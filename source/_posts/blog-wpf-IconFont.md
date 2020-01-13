---
title: 字体图标的使用
tags: ['WPF']
categories: ['知识补丁']
---

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

`xaml`文件中使用对应的属性

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