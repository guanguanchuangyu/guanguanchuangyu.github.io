---
title: WPF实现高仿统计标题卡
tags:
  - WPF
  - Components
categories:
  - 唐门暗器
date: 2020-02-20 00:51:23
---


飘哇~~~，在家数瓜子仁儿，闲来无事，看东看西，也找点儿，最近正在看看WPF动画，光看也是不行，需要带着目的去学习，整合知识碎片，恰巧，看到`Github`中一个基于`Ant Designer`设计风格的[后台管理系统](https://github.com/liuguanhua/react-antd-admin)，看到统计标题卡，就它了，于是就开始造作

![image-20200219203826827](https://file.budbud.cn/ggcyblog/blog-wpf-titleCard/wpf-titleCard.png)

<!--more-->

# 动画分析

## 原始效果

![wpf-titleCard](https://file.budbud.cn/ggcyblog/blog-wpf-titleCard/wpf-titleCard.gif)

## 布局

卡片由外层边框块`Border`、内层左右布局构成，图例如下：

![wpf-titleCard-layout](https://file.budbud.cn/ggcyblog/blog-wpf-titleCard/wpf-titleCard-layout.png)

## 动效

### 触发事件

鼠标进入卡片区域开始动效

```xml
<EventTrigger RoutedEvent="MouseEnter">
    ......
</EventTrigger>
```

鼠标离开卡片区域退出动效

```xml
<EventTrigger RoutedEvent="MouseLeave">
    .......
</EventTrigger>
```

### 动效结果

#### 左半部分

外部圆形边框收缩，使用图形变换中[缩放对象ScaleTransform](https://docs.microsoft.com/zh-cn/dotnet/framework/wpf/graphics-multimedia/how-to-scale-an-element)

```xml
<Ellipse x:Name="Bd" Stroke="#55ffffff" StrokeThickness="2" Width="74" Height="74" RenderTransformOrigin="0.5,0.5">
    <Ellipse.RenderTransform>
        <ScaleTransform></ScaleTransform>
    </Ellipse.RenderTransform>
</Ellipse>
```

内部圆形区域放大，使用图形变换中[缩放对象ScaleTransform](https://docs.microsoft.com/zh-cn/dotnet/framework/wpf/graphics-multimedia/how-to-scale-an-element)

```xml
<Ellipse x:Name="IconContent" Fill="#55ffffff" Width="54" Height="54" RenderTransformOrigin="0.5,0.5">
    <Ellipse.RenderTransform>
        <ScaleTransform></ScaleTransform>
    </Ellipse.RenderTransform>
</Ellipse>
```

显示的图标

```xml
<Path Data="{Binding Icon,Converter={x:Static local:KeyToResourceConverter.Instance}}" Stretch="Uniform" Width="34px" Height="34px" Fill="White"></Path>
```

#### 右半部分

```xml
<TextBlock Text="{Binding Path=Value}" FontSize="36px"></TextBlock>
<TextBlock Text="{Binding Path=Name}" FontSize="16px"></TextBlock>
```

#### 整体部分

向上位移，使用的是图形变换中[转换对象TranslateTransform](https://docs.microsoft.com/zh-cn/dotnet/framework/wpf/graphics-multimedia/how-to-translate-an-element)

```xml
<Border x:Name="Bg" UseLayoutRounding="True" Background="{Binding ItemBg}" VerticalAlignment="Center" Cursor="Hand" HorizontalAlignment="Stretch" Padding="15,25,40,25" Margin="12" RenderTransformOrigin="0.5,0.5">
    <Border.Effect>
        <DropShadowEffect BlurRadius="15" ShadowDepth="2" Direction="270" Opacity="0"></DropShadowEffect>
    </Border.Effect>
	<Border.RenderTransform>
    	<TransformGroup>
        	<TranslateTransform X="0"></TranslateTransform>
        	<ScaleTransform></ScaleTransform>
    	</TransformGroup>
	</Border.RenderTransform>
    <!--整体内容-->
</Border>
```

横向拉伸，使用是图形变换中[缩放对象ScaleTransform](https://docs.microsoft.com/zh-cn/dotnet/framework/wpf/graphics-multimedia/how-to-scale-an-element)

```xml
代码同上
```

# 动画与结构

## 整体内容

注释部分忽略到无关内容

```xml
<Border x:Name="Bg" UseLayoutRounding="True" Background="{Binding ItemBg}" VerticalAlignment="Center" Cursor="Hand" HorizontalAlignment="Stretch" Padding="15,25,40,25" Margin="12" RenderTransformOrigin="0.5,0.5">
                            <Border.Effect>
                                <DropShadowEffect BlurRadius="15" ShadowDepth="2" Direction="270" Opacity="0"></DropShadowEffect>
                            </Border.Effect>
                            <Border.RenderTransform>
                                <TransformGroup>
                                    <TranslateTransform X="0"></TranslateTransform>
                                    <ScaleTransform></ScaleTransform>
                                </TransformGroup>
                            </Border.RenderTransform>
                            <DockPanel HorizontalAlignment="Left">
                                <Grid DockPanel.Dock="Left">
                                    <!--左半部分内容-->
                                </Grid>
                                <StackPanel VerticalAlignment="Center" Margin="20,0,0,0">
                                    <!--右半部分内容-->
                                </StackPanel>
                            </DockPanel>
                            <Border.Triggers>
                                <EventTrigger RoutedEvent="MouseEnter">
								<!--动画实现-->
                                </EventTrigger>
                                <EventTrigger RoutedEvent="MouseLeave">
                                <!--动画实现-->
                                </EventTrigger>
                            </Border.Triggers>
                        </Border>
```

## 动画实现

### 鼠标进入卡片

```xml
<EventTrigger RoutedEvent="MouseEnter">
                                    <BeginStoryboard>
                                        <Storyboard>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="0.9" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="0.9" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="1.1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="1.1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[0].(TranslateTransform.Y)" Duration="0:0:0.2" To="-5"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[1].(ScaleTransform.ScaleX)" Duration="0:0:0.2" To="1.01"></DoubleAnimation>
                                            <!--<DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TranslateTransform.Y)" Duration="0:0:0.2" To="-5"></DoubleAnimation>-->
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.Effect).(DropShadowEffect.Opacity)" Duration="0:0:0.2" To="0.25"></DoubleAnimation>
                                        </Storyboard>
                                    </BeginStoryboard>
                                </EventTrigger>
```

### 鼠标离开卡片

```xml
<EventTrigger RoutedEvent="MouseLeave">
                                    <BeginStoryboard>
                                        <Storyboard>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[0].(TranslateTransform.Y)" Duration="0:0:0.2" To="0"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[1].(ScaleTransform.ScaleX)" Duration="0:0:0.2" To="1"></DoubleAnimation>
                                            <!--<DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TranslateTransform.Y)" Duration="0:0:0.2" To="0"></DoubleAnimation>-->
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.Effect).(DropShadowEffect.Opacity)" Duration="0:0:0.2" To="0"></DoubleAnimation>
                                        </Storyboard>
                                    </BeginStoryboard>
                                </EventTrigger>
```

# 案例实现

### 项目结构

项目为`Vs2019`创建，版本为`.NET4.5`

![wpf-titleCard-Case](https://file.budbud.cn/ggcyblog/blog-wpf-titleCard/wpf-titleCard-Case.png)

`KeyToResourceConverter.cs`包含**单值转换器**源码，用于将字符串`Key`转换为资源字典中的对象资源

`AntGeometies.xaml`包含**`Geometry`图形数据**

`Generic.xaml`包含`AntGeometies.xaml`的资源合并配置

`ItemsViewModel.cs`包含视图实体的**测试数据**，用于构件案例完整的数据结构

`App.xaml`包含`Generice.xaml`的资源合并配置

## 视图实体

```c#
    public class ItemViewModel
    {
        public string Name { get; set; }
        public string Value { get; set; }

        public string Icon { get; set; }

        public string ItemBg { get; set; }
    }
```

## 测试数据

```c#
    /// <summary>
    /// 统计展示卡视图实体
    /// </summary>
    public class ItemsViewModel
    {
        public ItemsViewModel()
        {
            InitialData();
        }
        /// <summary>
        /// 初始化数据
        /// </summary>
        private void InitialData()
        {
            //模拟初始化数据
            Data = new List<ItemViewModel> {
            new ItemViewModel{
             Name="商品总数",
             Value="67,512",
             Icon = "Bag",
             ItemBg="#1890ff"
            },
            new ItemViewModel{
             Name="销售总数",
             Value="51,881",
             Icon = "Money",
             ItemBg="#727cb6"
            },
            new ItemViewModel{
             Name="用户总数",
             Value="58,395",
             Icon = "User",
             ItemBg="#00acac"
            },
            new ItemViewModel{
             Name="访问量",
             Value="73,509",
             Icon = "Eye",
             ItemBg="#ff5b57"
            }
            };
        }

        public List<ItemViewModel> Data { get; set; }
    }
```

## 单值转换器

```c#
    /// <summary>
    /// 通过key将资源转换为Geometry类型数据
    /// </summary>
    public class KeyToResourceConverter : IValueConverter
    {
        private static KeyToResourceConverter _convert;
        public static KeyToResourceConverter Instance
        {
            get
            {
                if (_convert == null)
                {
                    _convert = new KeyToResourceConverter();
                }
                return _convert;
            }
        }
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            string key = value as string;
            if (string.IsNullOrEmpty(key))
            {
                return DependencyProperty.UnsetValue;
            }
            //明确返回类型
            //Geometry geometry = Application.Current.FindResource(key) as Geometry;
            //不明确返回类型
            var geometry = Application.Current.FindResource(key);
            return geometry;
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            return DependencyProperty.UnsetValue;
        }
    }
```

## 资源

### Geometry图形数据

`AntGeometries.xaml`内容，在`App.xaml`中引用

```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Geometry x:Key="Bag">
        M832 312H696v-16c0-101.6-82.4-184-184-184s-184 82.4-184 184v16H192c-17.7 0-32 14.3-32 32v536c0 17.7 14.3 32 32 32h640c17.7 0 32-14.3 32-32V344c0-17.7-14.3-32-32-32zm-432-16c0-61.9 50.1-112 112-112s112 50.1 112 112v16H400v-16zm392 544H232V384h96v88c0 4.4 3.6 8 8 8h56c4.4 0 8-3.6 8-8v-88h224v88c0 4.4 3.6 8 8 8h56c4.4 0 8-3.6 8-8v-88h96v456z
    </Geometry>
    <Geometry x:Key="Money">
        M512 64C264.6 64 64 264.6 64 512s200.6 448 448 448 448-200.6 448-448S759.4 64 512 64zm0 820c-205.4 0-372-166.6-372-372s166.6-372 372-372 372 166.6 372 372-166.6 372-372 372zm159.6-585h-59.5c-3 0-5.8 1.7-7.1 4.4l-90.6 180H511l-90.6-180a8 8 0 0 0-7.1-4.4h-60.7c-1.3 0-2.6.3-3.8 1-3.9 2.1-5.3 7-3.2 10.9L457 515.7h-61.4c-4.4 0-8 3.6-8 8v29.9c0 4.4 3.6 8 8 8h81.7V603h-81.7c-4.4 0-8 3.6-8 8v29.9c0 4.4 3.6 8 8 8h81.7V717c0 4.4 3.6 8 8 8h54.3c4.4 0 8-3.6 8-8v-68.1h82c4.4 0 8-3.6 8-8V611c0-4.4-3.6-8-8-8h-82v-41.5h82c4.4 0 8-3.6 8-8v-29.9c0-4.4-3.6-8-8-8h-62l111.1-204.8c.6-1.2 1-2.5 1-3.8-.1-4.4-3.7-8-8.1-8z
    </Geometry>
    <Geometry x:Key="User">
        M858.5 763.6a374 374 0 0 0-80.6-119.5 375.63 375.63 0 0 0-119.5-80.6c-.4-.2-.8-.3-1.2-.5C719.5 518 760 444.7 760 362c0-137-111-248-248-248S264 225 264 362c0 82.7 40.5 156 102.8 201.1-.4.2-.8.3-1.2.5-44.8 18.9-85 46-119.5 80.6a375.63 375.63 0 0 0-80.6 119.5A371.7 371.7 0 0 0 136 901.8a8 8 0 0 0 8 8.2h60c4.4 0 7.9-3.5 8-7.8 2-77.2 33-149.5 87.8-204.3 56.7-56.7 132-87.9 212.2-87.9s155.5 31.2 212.2 87.9C779 752.7 810 825 812 902.2c.1 4.4 3.6 7.8 8 7.8h60a8 8 0 0 0 8-8.2c-1-47.8-10.9-94.3-29.5-138.2zM512 534c-45.9 0-89.1-17.9-121.6-50.4S340 407.9 340 362c0-45.9 17.9-89.1 50.4-121.6S466.1 190 512 190s89.1 17.9 121.6 50.4S684 316.1 684 362c0 45.9-17.9 89.1-50.4 121.6S557.9 534 512 534z
    </Geometry>
    <Geometry x:Key="Eye">
        M942.2 486.2C847.4 286.5 704.1 186 512 186c-192.2 0-335.4 100.5-430.2 300.3a60.3 60.3 0 0 0 0 51.5C176.6 737.5 319.9 838 512 838c192.2 0 335.4-100.5 430.2-300.3 7.7-16.2 7.7-35 0-51.5zM512 766c-161.3 0-279.4-81.8-362.7-254C232.6 339.8 350.7 258 512 258c161.3 0 279.4 81.8 362.7 254C791.5 684.2 673.4 766 512 766zm-4-430c-97.2 0-176 78.8-176 176s78.8 176 176 176 176-78.8 176-176-78.8-176-176-176zm0 288c-61.9 0-112-50.1-112-112s50.1-112 112-112 112 50.1 112 112-50.1 112-112 112z
    </Geometry>
</ResourceDictionary>
```

### 上下文静态数据源

设定页面数据上下文，其中`local`为对应`ItemsViewModel`所在的命名空间别名，父级控件为`Window`

```xml
    <Window.Resources>
        <local:ItemsViewModel x:Key="Vm"></local:ItemsViewModel>
    </Window.Resources>
    <Window.DataContext>
        <Binding Source="{StaticResource Vm}"></Binding>
    </Window.DataContext>
```

## 完整代码

此处为自定义`ItemControl`模板实现卡片效果，数据均来自案例实现

```xml
<Grid Background="#55d9d9d9">
        <StackPanel Margin="30">
            <ItemsControl ItemsSource="{Binding Path=Data}" FocusVisualStyle="{x:Null}">
                <ItemsControl.ItemsPanel>
                    <ItemsPanelTemplate>
                        <UniformGrid></UniformGrid>
                    </ItemsPanelTemplate>
                </ItemsControl.ItemsPanel>
                <ItemsControl.ItemTemplate>
                    <DataTemplate>
                        <!--统计卡样式,也就是上述提到过的所有结构内容-->
                    </DataTemplate>
                </ItemsControl.ItemTemplate>
            </ItemsControl>
    </StackPanel>
</Grid>
```

`xaml`完整结构

```xml
<Window x:Class="Blog.TotalCard.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:Blog.TotalCard"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800" Foreground="White"
        xmlns:local="clr-namespace:Blog.TotalCard">
<Window.Resources>
        <local:ItemsViewModel x:Key="Vm"></local:ItemsViewModel>
    </Window.Resources>
    <Window.DataContext>
        <Binding Source="{StaticResource Vm}"></Binding>
    </Window.DataContext>
<Grid Background="#55d9d9d9">
        <StackPanel Margin="30">
            <ItemsControl ItemsSource="{Binding Path=Data}" FocusVisualStyle="{x:Null}">
                <ItemsControl.ItemsPanel>
                    <ItemsPanelTemplate>
                        <UniformGrid></UniformGrid>
                    </ItemsPanelTemplate>
                </ItemsControl.ItemsPanel>
                <ItemsControl.ItemTemplate>
                    <DataTemplate>
                        <Border x:Name="Bg" UseLayoutRounding="True" Background="{Binding ItemBg}" VerticalAlignment="Center" Cursor="Hand" HorizontalAlignment="Stretch" Padding="15,25,40,25" Margin="12" RenderTransformOrigin="0.5,0.5">
                            <Border.Effect>
                                <DropShadowEffect BlurRadius="15" ShadowDepth="2" Direction="270" Opacity="0"></DropShadowEffect>
                            </Border.Effect>
                            <Border.RenderTransform>
                                <TransformGroup>
                                    <TranslateTransform X="0"></TranslateTransform>
                                    <ScaleTransform></ScaleTransform>
                                </TransformGroup>
                            </Border.RenderTransform>
                            <DockPanel HorizontalAlignment="Left">
                                <Grid DockPanel.Dock="Left">
                                    <Ellipse x:Name="Bd" Stroke="#55ffffff" StrokeThickness="2" Width="74" Height="74" RenderTransformOrigin="0.5,0.5">
                                        <Ellipse.RenderTransform>
                                            <ScaleTransform></ScaleTransform>
                                        </Ellipse.RenderTransform>
                                    </Ellipse>
                                    <Ellipse x:Name="IconContent" Fill="#55ffffff" Width="54" Height="54" RenderTransformOrigin="0.5,0.5">
                                        <Ellipse.RenderTransform>
                                            <ScaleTransform></ScaleTransform>
                                        </Ellipse.RenderTransform>
                                    </Ellipse>
                                    <Path Data="{Binding Icon,Converter={x:Static local:KeyToResourceConverter.Instance}}" Stretch="Uniform" Width="34px" Height="34px" Fill="White"></Path>
                                </Grid>
                                <StackPanel VerticalAlignment="Center" Margin="20,0,0,0">
                                    <TextBlock Text="{Binding Path=Value}" FontSize="36px"></TextBlock>
                                    <TextBlock Text="{Binding Path=Name}" FontSize="16px"></TextBlock>
                                </StackPanel>
                            </DockPanel>
                            <Border.Triggers>
                                <EventTrigger RoutedEvent="MouseEnter">
                                    <BeginStoryboard>
                                        <Storyboard>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="0.9" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="0.9" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="1.1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="1.1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[0].(TranslateTransform.Y)" Duration="0:0:0.2" To="-5"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[1].(ScaleTransform.ScaleX)" Duration="0:0:0.2" To="1.01"></DoubleAnimation>
                                            <!--<DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TranslateTransform.Y)" Duration="0:0:0.2" To="-5"></DoubleAnimation>-->
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.Effect).(DropShadowEffect.Opacity)" Duration="0:0:0.2" To="0.25"></DoubleAnimation>
                                        </Storyboard>
                                    </BeginStoryboard>
                                </EventTrigger>
                                <EventTrigger RoutedEvent="MouseLeave">
                                    <BeginStoryboard>
                                        <Storyboard>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bd" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleX)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="IconContent" Storyboard.TargetProperty="(Ellipse.RenderTransform).(ScaleTransform.ScaleY)" To="1" Duration="0:0:0.2"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[0].(TranslateTransform.Y)" Duration="0:0:0.2" To="0"></DoubleAnimation>
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TransformGroup.Children)[1].(ScaleTransform.ScaleX)" Duration="0:0:0.2" To="1"></DoubleAnimation>
                                            <!--<DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.RenderTransform).(TranslateTransform.Y)" Duration="0:0:0.2" To="0"></DoubleAnimation>-->
                                            <DoubleAnimation Storyboard.TargetName="Bg" Storyboard.TargetProperty="(Border.Effect).(DropShadowEffect.Opacity)" Duration="0:0:0.2" To="0"></DoubleAnimation>
                                        </Storyboard>
                                    </BeginStoryboard>
                                </EventTrigger>
                            </Border.Triggers>
                        </Border>
                    </DataTemplate>
                </ItemsControl.ItemTemplate>
            </ItemsControl>
        </StackPanel>
    </Grid>
</Window>
```

## 实现效果

![wpf-titleCard-Result](https://file.budbud.cn/ggcyblog/blog-wpf-titleCard/wpf-titleCard-Result.gif)

# QAF

篇幅比较长，思路可能不是很清晰，如果需要项目源码，可以关注一下公众号，回复【高仿统计卡片】获取源码资源

![WXGZ](https://file.budbud.cn/ggcyblog/images/WXGZ.jpg)