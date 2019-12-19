---
title: WPF基础容器与布局
tags: WPF
categories:
  - 知识补丁
date: 2019-12-19 23:18:17
---


`WPF`的`xaml`中进行布局设定时，少不了和布局容器打交道，合理的使用和搭配这些容器，能够让页面美观的同时，可维护性和可拓展性更高

<!--more-->

# 布局容器（面板控件）

容器中的控件（子元素）理论上是建议不显式指定尺寸，不使用屏幕坐标设定控件自身的位置

常见容器如下：

| 控件名称    | 用途                                                         |
| ----------- | ------------------------------------------------------------ |
| DockPanel   | 子级元素依靠在容器边缘，分为 顶部、底部、左侧、右侧，用于实现靠近边缘的工具栏、表单提交按钮等布局，通过在子元素中使用附加属性DockPanel.Dock进行控制，同时需要注意是否需要考虑设定LastChildFill控制容器的最后一个元素是否填充剩余空间，顶部和底部优先级高于左侧和右侧 |
| WrapPanel   | 将每个子元素水平（默认）或垂直方向定位到另一个子元素的旁边，水平或垂直方向不满足当前元素的宽度或者高度时，元素自动换行或换列 |
| StackPanel  | 子元素在某一方向（水平/垂直）相互之间有序靠近，子元素控件的高度或宽度决定了子元素的填充范围，操作子元素自身并不会换行或者换列 |
| Grid        | 自带面板中最复杂的，网格布局风格，通过设定行列数以及Grid.Column、Row以及跨行跨列数附加型依赖属性，能够灵活的控制子元素的布局，几乎能够实现其他常用布局容器的大部分功能 |
| UniformGrid | 等比网格，比起grid使用上更简单，不需要为子元素设定位子，相邻元素一次排列，通过显式或者默认的行列数量，决定行列均匀分布的网格布局 |
| Canvas      | 不会默认做任何事，而是允许你把控件放在它的内部，然后显式地给这些控件定位，用于绘制图案，作为画布使用，同时内部控件使用相对位置定位，Canvas.Top、Canvas.Left、Canvas.Right、Canvas.Bottom控制子元素的位置，其中Z轴决定多个图层间的上下层排列顺序 |
| InkCanvas   | 手写板，用于进行三方笔触手写，不同模式拥有不同的手写或者操作功能，能够实现粘贴复制等功能 |

## 容器嵌套

各个容器的特性不同，可以通过各个容器的组合实现符合业务需求的页面布局效果，例如一个上下结构的页面，效果如下：

![blog-wpf-layout-case01](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-wpf-layout/blog-wpf-layout-case01.png)

xaml代码如下：

```xml
    <DockPanel>
        <Border Height="70" hc:WindowAttach.IsDragElement="True" BorderThickness="0,0,0,1" BorderBrush="{DynamicResource BorderBrush}" DockPanel.Dock="Top">
            <DockPanel Background="Transparent">
                <UniformGrid DockPanel.Dock="Right" Rows="1" Margin="0,0,50,0">
                    <Button Content="登录" Margin="5"></Button>
                    <Button Content="注册" Margin="5" Style="{DynamicResource BtnSuccess}"></Button>
                </UniformGrid>
                <DockPanel>
                    <Image Width="50" Height="50"></Image>
                    <TextBlock Text="NgxMarker" VerticalAlignment="Center"></TextBlock>
                    <StackPanel Orientation="Horizontal" DockPanel.Dock="Right" Margin="40,0,0,0">
                    </StackPanel>
                </DockPanel>
            </DockPanel>
        </Border>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="0.45*"/>
                <RowDefinition Height="*"/>
                <RowDefinition Height="0.7*"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <StackPanel VerticalAlignment="Top" Grid.Row="1" HorizontalAlignment="Left" Margin="90,0">
                <TextBlock Text="你的文档，你的笑" FontSize="50" FontWeight="Bold"/>
                <TextBlock Text="与团队一起编写文档，极致体验，高效协同,在微笑中构建自己的专属知识库"
                           Margin="0,30"
                           LineHeight="30" Foreground="{DynamicResource SecondaryTextBrush}" TextWrapping="Wrap" FontSize="18"></TextBlock>
                <Button Margin="0,16" Content="开始Marker" HorizontalAlignment="Left" Padding="30,12" Style="{DynamicResource BtnSuccess}"></Button>
            </StackPanel>
        </Grid>
    </DockPanel>
```



## 网格拆分器

`GridSplitter`能够允许用户手动`Grid`调整单元格行或者列的尺寸大小

```xaml
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"></ColumnDefinition>
                <ColumnDefinition Width="Auto"></ColumnDefinition>
                <ColumnDefinition Width="*"></ColumnDefinition>
            </Grid.ColumnDefinitions>
            <Button Content="左侧"></Button>
            <GridSplitter Grid.Column="1" Width="1" HorizontalAlignment="Stretch"></GridSplitter>
            <Button Content="右侧" Grid.Column="2"></Button>
        </Grid>
```

![blog-wpf-layout-splitter01](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-wpf-layout/blog-wpf-layout-splitter01.png)

![blog-wpf-layout-splitter02](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-wpf-layout/blog-wpf-layout-splitter02.png)

其中的**ResizeDirection** 属性可以设定分割器的行列模式

## 布局舍入

`UseLayoutRounding`属性设置为`True`时，能够对布局中的显示部分进行尺寸舍入，保证控件的清晰度

案例：

```xaml
        <Border BorderThickness="1.4" BorderBrush="Black" Margin="10"></Border>
        <Border BorderThickness="1.4" BorderBrush="Black" Margin="10" UseLayoutRounding="True"></Border>
        <Border BorderThickness="1" BorderBrush="Black" Margin="10"></Border>
        <Border BorderThickness="1" BorderBrush="Black" Margin="10" UseLayoutRounding="True"></Border>
```

效果：

![blog-wpf-layout-UseLayoutRound01](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-wpf-layout/blog-wpf-layout-UseLayoutRound01.png)

![blog-wpf-layout-UseLayoutRound02](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-wpf-layout/blog-wpf-layout-UseLayoutRound02.png)

# 自定义布局容器

通过查看[微软文档]()知道，容器的父类为`Panel`类型如下：

- 继承

  [Object](https://docs.microsoft.com/zh-cn/dotnet/api/system.object?view=netframework-4.8) 

  [DispatcherObject](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.threading.dispatcherobject?view=netframework-4.8) 

  [DependencyObject](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.dependencyobject?view=netframework-4.8) 

  [Visual](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.media.visual?view=netframework-4.8) 

  [UIElement](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.uielement?view=netframework-4.8) 

  [FrameworkElement](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement?view=netframework-4.8) 

Panel

- 派生

  [System.Windows.Controls.Canvas](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.canvas?view=netframework-4.8)
  [System.Windows.Controls.DockPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.dockpanel?view=netframework-4.8)
  [System.Windows.Controls.Grid](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.grid?view=netframework-4.8)
  [System.Windows.Controls.StackPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.stackpanel?view=netframework-4.8)
  [System.Windows.Controls.VirtualizingPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.virtualizingpanel?view=netframework-4.8)
  [System.Windows.Controls.WrapPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.wrappanel?view=netframework-4.8)
  [System.Windows.Controls.Primitives.TabPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.primitives.tabpanel?view=netframework-4.8)
  [System.Windows.Controls.Primitives.ToolBarOverflowPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.primitives.toolbaroverflowpanel?view=netframework-4.8)
  [System.Windows.Controls.Primitives.UniformGrid](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.primitives.uniformgrid?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonContextualTabGroupsPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribboncontextualtabgroupspanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonGalleryCategoriesPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbongallerycategoriespanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonGalleryItemsPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbongalleryitemspanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonGroupItemsPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbongroupitemspanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonQuickAccessToolBarOverflowPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbonquickaccesstoolbaroverflowpanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonTabHeadersPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbontabheaderspanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonTabsPanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbontabspanel?view=netframework-4.8)
  [System.Windows.Controls.Ribbon.Primitives.RibbonTitlePanel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.ribbon.primitives.ribbontitlepanel?view=netframework-4.8)

通过了解[Panel-.Net 4.8](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.panel?view=netframework-4.8)、[Panel-core3.1](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.panel?view=netcore-3.1)类相关的函数

正如文档所说：

> WPF provides a comprehensive suite of derived [Panel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.panel?view=netcore-3.1) implementations, enabling many complex layouts. If you want to implement new layout containers, use the [MeasureOverride](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement.measureoverride?view=netcore-3.1) and [ArrangeOverride](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement.arrangeoverride?view=netcore-3.1) methods. For a demonstration of how to use these methods, see [Create a Custom Content-Wrapping Panel Sample](https://go.microsoft.com/fwlink/?LinkID=159979).

> WPF提供了一套全面的[Panel](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.controls.panel?view=netframework-4.8)派生实现, 从而实现了很多复杂的布局。 如果要实现新的[MeasureOverride](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement.measureoverride?view=netframework-4.8)布局容器, 请使用和[ArrangeOverride](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.frameworkelement.arrangeoverride?view=netframework-4.8)方法。 有关如何使用这些方法的演示, 请参阅[创建自定义内容换行面板示例](https://go.microsoft.com/fwlink/?LinkID=159979)。(来源于微软文档)

也就是说，构建自己的容器可以继承`Panel`，重写函数`MeasureOverride`和`ArrangeOverride`实现，`MeasureOverride`函数用于测量子元素期望的布局尺寸，`ArrangeOverride`函数用于设定子元素在容器中的位置和布局大小，那么开始自定义容器之旅吧！

简单实现一个自定义`Panel`，容器内的元素水平均分布局：

自定义`MarkPanel.cs`

```c#
    [System.Windows.Localizability(System.Windows.LocalizationCategory.Ignore)]
    [System.Windows.Markup.ContentProperty(nameof(Children))]
    public class MakerPanel:Panel
    {
        //用来安排子元素在容器中的布局
        protected override Size ArrangeOverride(Size finalSize)
        {
            int count = Children.Count;
            double width = finalSize.Width / count;
            for (int i = 0; i < count; i++)
            {
                Children[i].Arrange(new Rect(new Point(i*width,0),new Size(width,finalSize.Height)));
            }
            return finalSize;
        }

        //用来测量子元素期望的布局尺寸
        protected override Size MeasureOverride(Size availableSize)
        {
            int count = Children.Count;
            for (int i = 0; i < count; i++)
            {
                Children[i].Measure(availableSize);
            }
            return availableSize;
        }
```

在`xaml`引用对应自定义所在的命名空间：

```
xmlns:local="clr-namespace:Blog.CasePanel"
```

`xaml`：

```xml
        <local:MakerPanel>
                <Border Background="LightBlue">
                    <TextBox Text="{Binding RelativeSource={RelativeSource Mode=Self},Path=FontSize}"></TextBox>
                </Border>
                <Border Background="Yellow"></Border>
                <Border Background="LightGray"></Border>
                <Border Background="LightGreen"></Border>
        </local:MakerPanel>
```



效果：

![blog-wpf-layout-definePanel](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-wpf-layout/blog-wpf-layout-definePanel.png)