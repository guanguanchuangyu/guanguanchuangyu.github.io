---
title: 自定义面板优化统计标题卡
tags:
  - WPF
  - Components
categories:
  - 唐门暗器
date: 2020-02-24 18:52:56
---


上篇文章[WPF实现高仿统计标题卡](https://github.budbud.cn/2020/02/20/blog-wpf-titleCard)中，实现了依据自己喜欢的统计卡片组件样式，实现过程中，发现，现有的`WPF`默认自带面板`Grid`、`UniformGrid`、`StackPanel`、`DockPanel`、`WrapPanel`以及`Canvas`等暂时没有满足条件的面板容器，接下来就是一个简单的容器面板我称之为`BootstrapPanel`，参考容器面板`UniformGrid`和`WrapPanel`两个容器的功能，**项目仍然为上篇文章使用的项目为例**

![blog-wpf-panel](https://file.budbud.cn/ggcyblog/blog-wpf-BootStrapPanel/blog-wpf-panel.png)

<!--more-->

# 基础面板分析

## `UniformGrid`

### 提供功能

该容器面板能够依据内部的可视子级数量，在未设置`Rows`、`Columns`、`FirstColumn`输习惯的条件下，进行自动填充均分容器面板的各区域

```xml
<ItemsControl ItemsSource="{Binding Path=Data}" FocusVisualStyle="{x:Null}">
                <ItemsControl.ItemsPanel>
                    <ItemsPanelTemplate>
                        <UniformGrid></UniformGrid>
                    </ItemsPanelTemplate>
                </ItemsControl.ItemsPanel>
</ItemsControl>
```

![blog-wpf-UniformGrid](https://file.budbud.cn/ggcyblog/blog-wpf-BootStrapPanel/blog-wpf-UniformGrid.gif)

### 问题

当随着容器面板大小缩放时，容器内的布局并不能够根据容器大小做出相应调整（换行减列）

## `WrapPanel`

### 提供功能

能够根据容器的大小，合理为容器内的各个可见元素做自适应处理（加列减行或减列加行）

```xml
<ItemsControl ItemsSource="{Binding Path=Data}" FocusVisualStyle="{x:Null}">
                <ItemsControl.ItemsPanel>
                    <ItemsPanelTemplate>
                        <WrapPanel></WrapPanel>
                    </ItemsPanelTemplate>
                </ItemsControl.ItemsPanel>
</ItemsControl>
```

![blog-wpf-WrapPanel](https://file.budbud.cn/ggcyblog/blog-wpf-BootStrapPanel/blog-wpf-WrapPanel.gif)

### 问题

虽然能够自动处理容器中元素的位置，但过于容器内的尺寸过于依赖已定尺寸，变换过程中，显示过程中，元素的尺寸无法自动填充容器的富余空间

#  思路分析

实际使用的过程中，我需要使用的容器需求归纳是如下几点：

- 能够实现容器内元素均分（`UniformGrid`具备）
- 能够在容器变化时，自动处理容器中的元素位置变化（`WrapPanel`具备）
- 能够自动填充富余空间（`UniformGrid`具备）

# 编辑与实现

于是，开启源码的浏览过程，自定义项目，结构如下：

![blog-wpf-protect](https://file.budbud.cn/ggcyblog/blog-wpf-BootStrapPanel/blog-wpf-protect.png)

## 测量容器、元素尺寸

此处忽略，菜鸟博主详细的探索细节，基本方向就是，通过看源码，重新构建上述两个容器的源码，通过源码最终决定以`UniformGrid`作为基础参照类，将动态设置元素布局的`ArrangeOverride`函数保留，能够保留元素动态适应容器布局，ps，可以直接挪用

```c#
protected override Size ArrangeOverride(Size arrangeSize)
        {
            Rect finalRect = new Rect(0.0, 0.0, arrangeSize.Width / (double)_columns, arrangeSize.Height / (double)_rows);
            double width = finalRect.Width;
            double num = arrangeSize.Width - 1.0;
            foreach (UIElement internalChild in base.InternalChildren)
            {
                internalChild.Arrange(finalRect);
                if (internalChild.Visibility != Visibility.Collapsed)
                {
                    finalRect.X += width;
                    if (finalRect.X >= num)
                    {
                        finalRect.Y += finalRect.Height;
                        finalRect.X = 0.0;
                    }
                }
            }
            return arrangeSize;
}
```

## 处理容器行列

主要需要考虑的是在测量也就是`MeasureOverride`函数对容器尺寸变更时，对行列的动态计算，我采用的方式，对列作处理，在不同条件下，显示不同的数据列，改变原有`UniformGrid`的固定行列方式，于是改动其中的`UpdateComputedValues`函数，将固定均分改为自定义实现逻辑，同时为了能够控制均分后，统一控制每个元素中的外边距，添加了一个依赖属性`Gutter`

### `Gutter`依赖属性

```c#
        /// <summary>
        /// 元素之间的间距
        /// </summary>
        public Thickness Gutter
        {
            get { return (Thickness)GetValue(GutterProperty); }
            set { SetValue(GutterProperty, value); }
        }
        public static readonly DependencyProperty GutterProperty =
            DependencyProperty.Register("Gutter", typeof(Thickness), typeof(BootstrapPanel), new PropertyMetadata(new Thickness(0)));
```

### `UpdateComputedValues`函数

```c#
		protected override Size MeasureOverride(Size constraint)
        {
            //自动获取行列数量
            UpdateComputedValues(constraint);

            //设置容器测量尺寸
            Size availableSize = new Size(constraint.Width / (double)_columns, constraint.Height / (double)_rows);
            double num = 0.0;
            double num2 = 0.0;
            int i = 0;
            for (int count = base.InternalChildren.Count; i < count; i++)
            {
                UIElement uIElement = base.InternalChildren[i];
                //Margin
                uIElement.SetValue(MarginProperty, Gutter);
                uIElement.Measure(availableSize);
                Size desiredSize = uIElement.DesiredSize;
                if (num < desiredSize.Width)
                {
                    num = desiredSize.Width;
                }
                if (num2 < desiredSize.Height)
                {
                    num2 = desiredSize.Height;
                }
            }
            return new Size(num * (double)_columns, num2 * (double)_rows);
        }

        /// <summary>
        /// 自动设置行列规则
        /// </summary>
        private void UpdateComputedValues(Size constraint)
        {
            //通过当前容器宽度以及默认最小宽度
            //获取到实际默认容器的列数
            if (DoubleUtil.IsNaN(ItemMinWidth))
            {
                throw new ArgumentNullException("请设置ItemMinWidth的目标值");
            }
            #region 统计容器中可见元素数量
            //统计未隐藏不占位的子级元素数量
            int childrenCount = 0;
            int i = 0;
            for (int count = base.InternalChildren.Count; i < count; i++)
            {
                UIElement uIElement = base.InternalChildren[i];
                if (uIElement.Visibility != Visibility.Collapsed)
                {
                    childrenCount++;
                }
            }
            if (childrenCount == 0)
            {
                childrenCount = 1;
            }
            #endregion

            //同行列数
            int colCount = (int)(constraint.Width / (ItemMinWidth + Gutter.Left + Gutter.Right));
            if (colCount >= childrenCount)//同行最大列数
            {
                _columns = childrenCount;
            }
            else if (colCount == 0)//容器宽度不能满足最小宽度
            {
                _columns = 1;
            }
            else
            {
                _columns = colCount;
            }
            if (_columns > 0)
            {
                _rows = (childrenCount + (_columns - 1)) / _columns;
                return;
            }
            //通过元素数量获取数据行数
            _rows = (int)Math.Sqrt(childrenCount);
            if (_rows * _rows < childrenCount)
            {
                _rows++;
            }
            if (_columns == 0)
            {
                _columns = (childrenCount + (_rows - 1)) / _rows;
            }
        }
```

###  容器代码

```c#
    /// <summary>
    /// 实现简单动态均分Panel容器
    /// </summary>
    public class BootstrapPanel : Panel
    {

        #region 依赖属性
        /// <summary>
        /// 子级最小宽度
        /// </summary>
        public double ItemMinWidth
        {
            get { return (double)GetValue(ItemMinWidthProperty); }
            set { SetValue(ItemMinWidthProperty, value); }
        }


        public static readonly DependencyProperty ItemMinWidthProperty = DependencyProperty.Register("ItemMinWidth", typeof(double), typeof(BootstrapPanel), new FrameworkPropertyMetadata(double.NaN, FrameworkPropertyMetadataOptions.AffectsMeasure), IsWidthHeightValid);
        private static bool IsWidthHeightValid(object value)
        {
            double num = (double)value;
            if (!DoubleUtil.IsNaN(num))
            {
                if (num >= 0.0)
                {
                    return !double.IsPositiveInfinity(num);
                }
                return false;
            }
            return true;
        }


        /// <summary>
        /// 元素之间的间距
        /// </summary>

        public Thickness Gutter
        {
            get { return (Thickness)GetValue(GutterProperty); }
            set { SetValue(GutterProperty, value); }
        }
        public static readonly DependencyProperty GutterProperty =
            DependencyProperty.Register("Gutter", typeof(Thickness), typeof(BootstrapPanel), new PropertyMetadata(new Thickness(0)));

        #endregion
        /// <summary>
        /// 实际行数
        /// </summary>
        private int _rows;
        /// <summary>
        /// 实际列数
        /// </summary>
        private int _columns;

        protected override Size MeasureOverride(Size constraint)
        {
            //自动获取行列数量
            UpdateComputedValues(constraint);

            //设置容器测量尺寸
            Size availableSize = new Size(constraint.Width / (double)_columns, constraint.Height / (double)_rows);
            double num = 0.0;
            double num2 = 0.0;
            int i = 0;
            for (int count = base.InternalChildren.Count; i < count; i++)
            {
                UIElement uIElement = base.InternalChildren[i];
                //Margin
                uIElement.SetValue(MarginProperty, Gutter);
                uIElement.Measure(availableSize);
                Size desiredSize = uIElement.DesiredSize;
                if (num < desiredSize.Width)
                {
                    num = desiredSize.Width;
                }
                if (num2 < desiredSize.Height)
                {
                    num2 = desiredSize.Height;
                }
            }
            return new Size(num * (double)_columns, num2 * (double)_rows);
        }

        /// <summary>
        /// 自动设置行列规则
        /// </summary>
        private void UpdateComputedValues(Size constraint)
        {
            //通过当前容器宽度以及默认最小宽度
            //获取到实际默认容器的列数
            if (DoubleUtil.IsNaN(ItemMinWidth))
            {
                throw new ArgumentNullException("请设置ItemMinWidth的目标值");
            }
            #region 统计容器中可见元素数量
            //统计未隐藏不占位的子级元素数量
            int childrenCount = 0;
            int i = 0;
            for (int count = base.InternalChildren.Count; i < count; i++)
            {
                UIElement uIElement = base.InternalChildren[i];
                if (uIElement.Visibility != Visibility.Collapsed)
                {
                    childrenCount++;
                }
            }
            if (childrenCount == 0)
            {
                childrenCount = 1;
            }
            #endregion

            //同行列数
            int colCount = (int)(constraint.Width / (ItemMinWidth + Gutter.Left + Gutter.Right));
            if (colCount >= childrenCount)//同行最大列数
            {
                _columns = childrenCount;
            }
            else if (colCount == 0)//容器宽度不能满足最小宽度
            {
                _columns = 1;
            }
            else
            {
                _columns = colCount;
            }
            if (_columns > 0)
            {
                _rows = (childrenCount + (_columns - 1)) / _columns;
                return;
            }
            //通过元素数量获取数据行数
            _rows = (int)Math.Sqrt(childrenCount);
            if (_rows * _rows < childrenCount)
            {
                _rows++;
            }
            if (_columns == 0)
            {
                _columns = (childrenCount + (_rows - 1)) / _rows;
            }
        }

        protected override Size ArrangeOverride(Size arrangeSize)
        {
            Rect finalRect = new Rect(0.0, 0.0, arrangeSize.Width / (double)_columns, arrangeSize.Height / (double)_rows);
            double width = finalRect.Width;
            double num = arrangeSize.Width - 1.0;
            foreach (UIElement internalChild in base.InternalChildren)
            {
                internalChild.Arrange(finalRect);
                if (internalChild.Visibility != Visibility.Collapsed)
                {
                    finalRect.X += width;
                    if (finalRect.X >= num)
                    {
                        finalRect.Y += finalRect.Height;
                        finalRect.X = 0.0;
                    }
                }
            }
            return arrangeSize;
        }
    }
```

# 容器使用

构造好的容器所在项目`Bolg.AntPanel`，被`Blog.TotalCard`项目引用，在页面中使用

## 命名空间引用

统计标题卡片对应的`xaml`中引入对应命名空间

```xml
xmlns:ant="clr-namespace:Blog.AntPanel.Controls;assembly=Blog.AntPanel"
```

组件中引用，替换掉之前的`ItemControl`对应的`ItemsPanel`,其中`ItemMinWidth`设置最小子级元素的宽度，`Gutter`设置子级元素间的外边距，主要作用到`MeasureOverride`中元素的属性设定

```c#
        protected override Size MeasureOverride(Size constraint)
        {
            //省略部分内容
            for (int count = base.InternalChildren.Count; i < count; i++)
            {
                //省略部分内容
                //Margin-设置元素外边距
                uIElement.SetValue(MarginProperty, Gutter);
                //省略部分内容
            }
            //省略部分内容
        }
```

## `xaml`中使用

统计标题卡片对应的`xaml`中`ItemsControl`模板的使用

```xml
<ItemsControl.ItemsPanel>
    <ItemsPanelTemplate>
        <ant:BootstrapPanel ItemMinWidth="240" Gutter="10"></ant:BootstrapPanel>
    </ItemsPanelTemplate>
</ItemsControl.ItemsPanel>
```

# 运行效果

![blog-wpf-BootstrapPanel](https://file.budbud.cn/ggcyblog/blog-wpf-BootStrapPanel/blog-wpf-BootstrapPanel.gif)

## 实现功能

- 能够根据容器中元素数量设定行列
- 通过容器的尺寸变化变更元素排列
- 元素均分的区域块元素自动填充

# Q&A

如果需要项目源码，可以关注一下公众号，回复【`BootstrapPanel`】获取源码资源

![WXGZ](https://file.budbud.cn/ggcyblog/images/WXGZ.jpg)