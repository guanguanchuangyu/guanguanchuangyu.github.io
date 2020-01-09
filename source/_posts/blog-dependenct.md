---
title: 依赖属性基础巩固
tags: WPF
categories:
  - 知识补丁
date: 2019-12-29 00:35:25
---


依赖属性是何物？

>Windows Presentation Foundation (WPF) 提供一组服务，这些服务可用于扩展类型的[属性](https://docs.microsoft.com/zh-cn/dotnet/standard/base-types/common-type-system#Properties)的功能。 这些服务通常统称为 WPF 属性系统。 由 WPF 属性系统支持的属性称为依赖属性

<!--more-->

![DependencyProperty](https://ggcy-static-01.oss-cn-hangzhou.aliyuncs.com/ggcyblog/blog-dependenct/DependencyProperty.png)


简单的来说就是在`WPF`中有别于面向对象中普通属性是其专属属性，依赖于`WPF属性系统`，对于依赖属性，并不像普通类类型属性那般进行实例化操作，而是依靠`DependencyProperty`进行类型函数建立静态实例，这样又可以说依赖属性为`DependencyProperty`支持的属性，依赖属性的自定义过程如下：

```c#
    /// <summary>
    /// 依赖属性案例类
    /// 创建依赖对象
    /// </summary>
    public class DependencyCase:DependencyObject
    {
        //声明静态只读属性
        public static readonly DependencyProperty CaseProperty;

        //注册依赖属性
        static DependencyCase()
        {
            //创建属性元数据-可选
            PropertyMetadata metadata = new PropertyMetadata(0);
            ////也可以进行创建框架属性元数据
            //FrameworkPropertyMetadata metadata = new FrameworkPropertyMetadata(default(int));
            CaseProperty =
                DependencyProperty.Register("Case", typeof(int), typeof(DependencyCase), metadata);
        }

        //构建属性封装器
        public int Case
        {
            get { return (int)GetValue(CaseProperty); }
            set { SetValue(CaseProperty, value); }
        }
    }
```

**具体解析如下：**

1. 创建依赖对象

   ```c#
   public class DependencyCase:DependencyObject
   {}
   ```

   必须为`DependencyObject`的子类，对于依赖属性来说，这是必须的，构建依赖属性时，对依赖属性读取和设置属性值时，使用到的`SetValue`和`GetValue`来源于`DependencyObject`

2. 声明静态只读属性名称

   ```c#
   //声明静态只读属性
   public static readonly DependencyProperty CaseProperty;
   ```

   属性的类型为`DependencyProperty`，名称为`XXX`加`Property`（作为约定俗成的规则，并不是强制要求），作为普通属性和依赖属性的命名规则区分

3. 进行依赖属性注册

   ```
   //创建属性元数据-可选
   PropertyMetadata metadata = new PropertyMetadata(0);
   ////也可以进行创建框架属性元数据
   //FrameworkPropertyMetadata metadata = new FrameworkPropertyMetadata(default(int));
   CaseProperty =
   DependencyProperty.Register("Case", typeof(int), typeof(DependencyCase), metadata);
   ```

   注册使用的是`DependencyProperty`中的函数`Register`，依据参数主要负责提供如下功能：

   - 设定依赖属性对应的属性名称，此处为`Case`

   - 设定属性的类型，`CaseProperty`为`int`类型

   - 设定拥有该属性的类，`DenpendencyCase`类

   - `PropertyMetadata`用于设定依赖属性能够支持的什么类型服务（动画，数据绑定以及日志等）

     属于可选项，同时如果需要处理依赖属性的数据变化以及相关行为操作，`PropertyMetadata`就变得必须，可使用`FrameworkPropertyMetadata`进行细化处理

4. 构建依赖属性封装器

   ```c#
           //构建属性封装器
           public int Case
           {
               get { return (int)GetValue(CaseProperty); }
               set { SetValue(CaseProperty, value); }
           }
   ```

   依赖属性的属性封装和对普通字段的封装并无太大区别，主要用于在属性对外进行实例化后，普通属性能够实现的功能，依赖属性也就能够实现，属性名称就是依赖属性的注册时名称，属性可以直接在`xaml`中使用，同时也可以直接在代码中使用

   `xaml`中使用：

   ```xaml
   <Window x:Class="Blog.AttachProperty.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:Blog.AttachProperty"
           mc:Ignorable="d"
           Title="MainWindow" Height="450" Width="800">
       <!--使用时引入对应依赖属性所在命名空间
   此处代码仅仅为演示依赖属性能够在xaml中使用，代码本身无其他意义-->
       <local:DependencyCase Case="12"></local:DependencyCase>
   </Window>
   ```

   代码中使用：

   ```c#
   DependencyCase dependencyCase = new DependencyCase();
   dependencyCase.Case = 12;
   ```

# 依赖属性提供的功能

- 资源
- 数据绑定
- 样式
- 动画
- 元数据重写
- 属性值继承
- `WPF`设计器集成

## 资源

> 依赖属性能够通过引用资源进行设置，资源一般是在目标根元素的`Resources`属性值中。

资源定义如下：

```xaml
<Window.Resources>
	<SolidColorBrush x:Key="comBg" Color="Red"></SolidColorBrush>
</Window.Resources>
```

控件中`Background`为控件的依赖属性，可通过使用标记拓展（如：`DynamicResource`、`StaticResource`）进行资源引用：

```xaml
<StackPanel>
	<TextBlock Background="{DynamicResource comBg}" Text="测试依赖属性引用资源"></TextBlock>
</StackPanel>
```

其中需要注意的是，如资源引用使用的标记扩展为`DynamicResource`时，属性必须设置为依赖属性，资源被视为本地值，需要考虑当出现多个本地值时，依赖属性值作用优先级

## 数据绑定

> 依赖属性可以通过数据绑定来引用值。数据绑定通过特定标记扩展（`xaml`中）或`Binding`对象（代码中）起作用。使用数据绑定，最终属性值得确定将延迟到运行时，在运行时，从数据源获取属性值。

```xaml
<TextBlock Text="{Binding Name}"></TextBlock>
```

绑定视为本地值，需要考虑依赖属性值的作用优先级

## 样式

样式和模板是使用依赖属性的两个主要激发方向，在`xaml`中，资源通常作为样式资源定义在`Resources`中。

资源：

```xaml
<Style x:Key="txtblock" TargetType="TextBlock">
	<Setter Property="Background" Value="YellowGreen"></Setter>
	<Setter Property="Foreground" Value="White"></Setter>
</Style>
```

使用：

```xaml
<TextBlock Style="{StaticResource txtblock}" Text="测试依赖属性引用样式"></TextBlock>
```

## 动画

> 可以对依赖属性进行动画处理。在应用和运动动画时，经过动画处理的值得操作优先级高于该属性以及其他方式具有的任何值（如本地值）

此处案例为，通过设定鼠标进入时，动画变更`TextBlock`的背景色

```xaml
<TextBlock Text="测试依赖属性作用于动画" Style="{StaticResource txtblock}">
            <TextBlock.Background>
                <SolidColorBrush x:Name="bg" Color="YellowGreen"></SolidColorBrush>
            </TextBlock.Background>
            <TextBlock.Triggers>
                <EventTrigger RoutedEvent="MouseEnter">
                    <BeginStoryboard>
                        <Storyboard>
                            <ColorAnimation AutoReverse="True" RepeatBehavior="Forever"
                                    Storyboard.TargetName="bg" Storyboard.TargetProperty="(SolidColorBrush.Color)"
                                    To="Red" Duration="0:0:3"></ColorAnimation>
                        </Storyboard>
                    </BeginStoryboard>
                </EventTrigger>
            </TextBlock.Triggers>
        </TextBlock>
```

## 元数据重写

依赖属性能够被派生类继承，可以通过重写依赖属性元数据更改该属性的某些行为。

```c#
    /// <summary>
    /// 依赖对象子类继承依赖对象
    /// </summary>
    public class ChildCase:DependencyCase
    {
        static ChildCase()
        {
            CaseProperty.OverrideMetadata(typeof(ChildCase), new FrameworkPropertyMetadata(12));
        }
    }
```

## 属性值继承

> 元素可以从其在对象树中的父级继承依赖属性的值

当时对于元素来说，并不是所有的依赖属性都启用了相关配置，仅仅是需要用到的特定方案的依赖属性值才对属性进行启用，例如`DataContext`，使用`FrameworkPropertyMetadata`设定该依赖属性是否被子级元素继承

```c#
DataContextProperty = DependencyProperty.Register("DataContext", typeof(object), _typeofThis, new FrameworkPropertyMetadata(null, FrameworkPropertyMetadataOptions.Inherits, OnDataContextChanged));
```

自定义时，无特殊需要，基本上不需要进行特定的启用处理，本身继承带来的计算时间会给系统性能带来一定的影响。

`WPF`设计器集成

> 具有作为依赖属性实现的属性，自定义控件将接收适用于Visual Studio支持的相应`WPF`设计器。一个示例就是能够在“属性”窗口中编辑直接依赖属性和附加依赖属性。

# 依赖属性优先级

> 获取依赖属性值时，获得的值可能是通过参与`WPF`属性系统的其他任一基于属性的输入而在该属性上设置的。由于存在依赖属性的优先级，是的属性获取值的方式的各种方案得以按可预测的方式进行交互

意思就是，存在优先级能够更好把控住依赖属性中属性值的不同时期的数据状态（动画和强制设定除外），了解优先级有助于避免不必要的属性设置，避免混淆，排查某些影响或者预测依赖属性值的最终尝试结果没有得到预期的原因

以下为分配依赖属性的运行时值时所使用的最终顺序，由高到低：

## 属性系统强制

## 活动动画或者Hold行为的动画

## 本地值

其中可能包含`StaticeSource`、`Binding`、`DynamicSource`等标记拓展引用的依赖属性值

## `TemplateParent`模板属性

a.模板中的触发器

b.模板中的属性集

该优先级项不直接声明元素的任何属性，仅仅对于应用模板而产生的可视化树中的元素项而言有效，当使用TemplateParent的依赖属性会在模板中进行搜索，优先级低于本地值

## 隐式样式（style）

不需要直接设置`Style`样式，在某一范围内，定义的元素`Style`，自动作用其对应类型子元素

```xaml
<StackPanel>
        <StackPanel.Resources>
            <Style TargetType="Button">
                <Setter Property="Foreground" Value="White"/>
            </Style>
        </StackPanel.Resources>
        <Button Content="隐式样式">
            <Button.Resources>
                <Style TargetType="Button">
                    <Setter Property="Foreground" Value="YellowGreen"/>
                </Style>
            </Button.Resources>
        </Button>
</StackPanel>
```

隐式样式定义越是接近元素，优先级越高

## 样式触发器（style）

该项在`Style`中`Triggers`中

```xaml
        <Button Content="样式触发器">
            <Button.Resources>
                <Style TargetType="Button">
                    <Setter Property="Foreground" Value="Red"/>
                    <Style.Triggers>
                        <Trigger Property="IsMouseOver" Value="True">
                            <Setter Property="Foreground" Value="Blue"/>
                        </Trigger>
                    </Style.Triggers>
                </Style>
            </Button.Resources>
        </Button>
```

## 模板触发器（style）

该项在`Style`中`ControlTemplate`或者`DataTemplate`中

```xaml
<Button Content="模板触发器">
            <Button.Resources>
                <Style TargetType="Button">
                    <Setter Property="Foreground" Value="YellowGreen"/>
                    <Setter Property="Template">
                        <Setter.Value>
                            <ControlTemplate TargetType="Button">
                                <ContentPresenter Content="{TemplateBinding Content}"></ContentPresenter>
                                <ControlTemplate.Triggers>
                                    <Trigger Property="IsMouseOver" Value="True">
                                        <Setter Property="Foreground" Value="Blue"/>
                                    </Trigger>
                                </ControlTemplate.Triggers>
                            </ControlTemplate>
                        </Setter.Value>
                    </Setter>
                    <Style.Triggers>
                        <Trigger Property="IsMouseOver" Value="True">
                            <Setter Property="Foreground" Value="Orange"/>
                        </Trigger>
                    </Style.Triggers>
                </Style>
            </Button.Resources>
        </Button>
```

## 样式资源库（style）

来自页面或应用程序的样式中的`Setter`的值

## 默认（主题）样式（`DefaultStyleKey`）

每个控件附带的一个默认样式，默认样式可能因主题而存在，故而叫主题样式

## 继承

依赖属性从父元素向子元素继承值，而不需要每个子元素上都设置该属性

## 来自依赖属性元数据默认值

任何给定的依赖属性都有可能有一个默认值，由该特定的属性系统注册确定