# 在 Xamarin 中可视化颜色选择器控件的 5 种不同方法。形式

> 原文：<https://dev.to/syncfusion/5-different-ways-to-visualize-a-color-picker-control-in-xamarin-forms-ne6>

## 简介

如今，用于选择颜色的 UI 在一些移动应用程序中起着至关重要的作用。颜色选择器为适应移动设备的紧凑屏幕提出了独特的 UI 挑战。所以， [Syncfusion Xamarin。像](https://www.syncfusion.com/xamarin-ui-controls/)[芯片](https://www.syncfusion.com/xamarin-ui-controls/chips)、[圆形菜单](https://www.syncfusion.com/xamarin-ui-controls/radial-menu)、[拾色器](https://www.syncfusion.com/xamarin-ui-controls/picker)、[分段控制](https://www.syncfusion.com/xamarin-ui-controls/segmented-control)、[范围滑块](https://www.syncfusion.com/xamarin-ui-controls/range-slider)等表单控件将帮助我们轻松实现各种拾色器 ui。

## Xamarin。形成作为颜色选择器的芯片

Xamarin。表格[芯片](https://www.syncfusion.com/xamarin-ui-controls/chips)控件将颜色呈现为多个圆圈。它在 flex 布局中排列多个色卡。它们可以被包装和分组以便于选择。

[![Xamarin.Forms Chips as Xamarin.Forms Color Picker](img/0fae94f8f94d4b2fa4dc95188775714e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ZiNmzse--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/chips-300x51.jpg)

<figcaption>Xamarin。将芯片作为颜色选择器</figcaption>

下面的代码演示了 Xamarin。表单[芯片](https://www.syncfusion.com/xamarin-ui-controls/Chips)控件填充有颜色值，绑定为带有自定义角的背景色。

```
<sfbuttons:SfChipGroup  Type="Choice"
                        ChipBorderWidth="1"
                        SelectedItem="{Binding SelectedItem}"
                        ItemsSource="{Binding Colors, Converter={StaticResource ColorsToChips}}"> 
           <sfbuttons:SfChipGroup.ChipLayout>
                     <FlexLayout HorizontalOptions="Start"
                                 VerticalOptions="Center"
                                 Direction="Row"
                                 Wrap="Wrap"
                                 JustifyContent="Start"
                                 AlignContent="Start"
                                 AlignItems="Start"/> 
           </sfbuttons:SfChipGroup.ChipLayout> 
</sfbuttons:SfChipGroup> 
```

## Xamarin。作为颜色选择器的窗体选择器控件

Xamarin。表单[选择器](https://www.syncfusion.com/xamarin-ui-controls/picker)控件是颜色选择器最常见的用户界面之一。用户使用颜色的名称来选择颜色。您可以通过填充 Xamarin 来实现此颜色选择器。形成颜色值。

[![Xamarin.Forms Picker as Xamarin.Forms Color Picker](img/e8fb1038dd00bfc624fac3e107075245.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yadFuFCc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Picker-300x210.jpg)

<figcaption>Xamarin。作为颜色选择器的表单选择器</figcaption>

下面的代码演示了 Xamarin。用颜色名称作为字符串值填充的表单[选取器](https://www.syncfusion.com/xamarin-ui-controls/picker)控件。

```
<sfpicker:SfPicker HeaderText="Selected Color"
                   SelectedItem="{Binding SelectedItem}"
                   ItemsSource="{Binding Colors}"/> 
```

## Xamarin。将圆形菜单作为颜色选择器

Xamarin。表单[圆形菜单](https://www.syncfusion.com/xamarin-ui-controls/radial-menu)提供了一个优雅的颜色选择器 UI。这种用户界面可以适应任何紧凑的移动屏幕。菜单项的层次结构允许您从一种原色导航到下一级的色调。在一级放射式菜单中填充一种主色，并将其阴影填充为子菜单，将类似于下面的 UI。

[![Xamarin.Forms Radial Menu as Color Picker](img/086b94914f340207c96956cb6874559d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tqp9mTq2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Radial-Menu-300x154.jpg)

<figcaption>Xamarin。将圆形菜单作为颜色选择器</figcaption>

下面的代码演示了 Xamarin。表单[圆形菜单](https://www.syncfusion.com/xamarin-ui-controls/radial-menu)控件，以原色及其阴影作为子项。

```
<sfradialmenu:SfRadialMenu  CenterButtonBackgroundColor="#383838" >
    <sfradialmenu:SfRadialMenu.Items>
        <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF0000">
            <sfradialmenu:SfRadialMenuItem.Items> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF0000" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF1E1E" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF3C3C" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF5A5A" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF7878" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF9696" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFB4B4" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFD2D2" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFF0F0" /> </sfradialmenu:SfRadialMenuItem.Items>
        </sfradialmenu:SfRadialMenuItem>
        <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFF00">
            <sfradialmenu:SfRadialMenuItem.Items> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFF00" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFF1E" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFF3C" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFF5A" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFF78" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFFB4" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFFD2" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFFFF0" /> </sfradialmenu:SfRadialMenuItem.Items>
        </sfradialmenu:SfRadialMenuItem>
        <sfradialmenu:SfRadialMenuItem BackgroundColor="#00FF00">
            <sfradialmenu:SfRadialMenuItem.Items> <sfradialmenu:SfRadialMenuItem BackgroundColor="#00FF00" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#1EFF1E" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#3CFF3C" />  <sfradialmenu:SfRadialMenuItem BackgroundColor="#78FF78" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#96FF96" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#B4FFB4" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#D2FFD2" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#F0FFF0" /> </sfradialmenu:SfRadialMenuItem.Items>
        </sfradialmenu:SfRadialMenuItem>
        <sfradialmenu:SfRadialMenuItem BackgroundColor="#00FFFF">
            <sfradialmenu:SfRadialMenuItem.Items> <sfradialmenu:SfRadialMenuItem BackgroundColor="#00FFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#1EFFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#3CFFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#78FFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#96FFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#B4FFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#D2FFFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#F0FFFF" /> </sfradialmenu:SfRadialMenuItem.Items>
        </sfradialmenu:SfRadialMenuItem>
        <sfradialmenu:SfRadialMenuItem BackgroundColor="#0000FF">
            <sfradialmenu:SfRadialMenuItem.Items> <sfradialmenu:SfRadialMenuItem BackgroundColor="#0000FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#1E1EFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#3C3CFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#7878FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#9696FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#B4B4FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#D2D2FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#F0F0FF" /> </sfradialmenu:SfRadialMenuItem.Items> 
        </sfradialmenu:SfRadialMenuItem>
        <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF00FF">
            <sfradialmenu:SfRadialMenuItem.Items> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF00FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF1EFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF3CFF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF78FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FF96FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFB4FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFD2FF" /> <sfradialmenu:SfRadialMenuItem BackgroundColor="#FFF0FF" /> </sfradialmenu:SfRadialMenuItem.Items>
        </sfradialmenu:SfRadialMenuItem>
    </sfradialmenu:SfRadialMenu.Items>
</sfradialmenu:SfRadialMenu> 
```

## Xamarin。作为颜色选择器的表单范围滑块

Xamarin。表单[范围滑块](https://www.syncfusion.com/xamarin-ui-controls/range-slider)通过定义 RGB 值帮助用户选择颜色。三个范围滑块控件代表 RGB 颜色代码，选择范围限制在 0 到 255 之间。

[![Xamarin.Forms Range Slider as Color Picker](img/18bf80a0ee01482f83e2ce7f088cae39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--55X8imxC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/Range-Slider-300x217.jpg)

<figcaption>Xamarin。表单范围滑块作为颜色选择器</figcaption>

下面的代码演示了三个 Xamarin。窗体[范围滑块](https://www.syncfusion.com/xamarin-ui-controls/range-slider)控件制作了以前的 UI。

```
<ContentView.Resources>
        <Style TargetType="sfrangeslider:SfRangeSlider">
            <Setter Property="Orientation" Value="Horizontal"/>
            <Setter Property="ShowValueLabel" Value="False"/>
            <Setter Property="ShowRange" Value="False"/>
            <Setter Property="TrackSelectionThickness" Value="2"/>
            <Setter Property="TrackThickness" Value="1"/>
            <Setter Property="TickFrequency" Value="255"/>
            <Setter Property="StepFrequency" Value="1"/>
            <Setter Property="TickPlacement" Value="None"/>
            <Setter Property="ThumbSize" Value="20"/>
            <Setter Property="Minimum" Value="0"/>
            <Setter Property="Maximum" Value="255"/>
        </Style>
</ContentView.Resources>

<sfrangeslider:SfRangeSlider  Value="{Binding RValue, Mode=TwoWay}"
                              TrackColor="#77FF0000"
                              KnobColor="#FFFF0000"
                              TrackSelectionColor="#CCFF0000"/>

<sfrangeslider:SfRangeSlider  Value="{Binding GValue, Mode=TwoWay}"
                              TrackColor="#7700FF00"
                              KnobColor="#FF00FF00"
                              TrackSelectionColor="#CC00FF00"/>

<sfrangeslider:SfRangeSlider  Value="{Binding BValue, Mode=TwoWay}"
                              TrackColor="#770000FF"
                              KnobColor="#FF0000FF"
                              TrackSelectionColor="#CC0000FF"/> 
```

## Xamarin。将分段控件作为颜色选择器

Xamarin。Forms [Segmented Control](https://www.syncfusion.com/xamarin-ui-controls/segmented-control) 提供了一组线性的圆形小块，每个小块都可以作为 UI 来选择颜色。尽管它提供了与 Xamarin 相似的 UI。形成芯片，但它提供选择动画。

[![Xamarin.Forms Segmented Control as Color Picker](img/8b74d7d7c33e17496d77fca55d23ce36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_1txm1LE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/05/segmented-control-300x47.jpg)

<figcaption>Xamarin。将分段控件作为颜色选择器</figcaption>

下面的代码演示了 Xamarin。表单[分段控件](https://www.syncfusion.com/xamarin-ui-controls/segmented-control)填充有颜色值，并绑定有分段项，如自定义视图颜色和自定义角。

```
<sfsegmentedcontrol:SfSegmentedControl BackgroundColor="{Binding SelectedColor}"
                                       SegmentHeight="40"
                                       SegmentWidth="40"
                                       SelectedItem="{Binding SelectedItem}"
                                       ItemsSource="{Binding ViewCollection}"
                                       SelectedIndex="{Binding SelectedIndex, Mode=TwoWay}"
                                       SegmentPadding="5">
      <sfsegmentedcontrol:SfSegmentedControl.SelectionIndicatorSettings>
            <sfsegmentedcontrol:SelectionIndicatorSettings Color="#FFFFFF"
                                                           Position="Border"
                                                           CornerRadius="20"/>
      </sfsegmentedcontrol:SfSegmentedControl.SelectionIndicatorSettings>
</sfsegmentedcontrol:SfSegmentedControl> 
```

## 总结

在这篇博客文章中，我们已经走过了 [Syncfusion Xamarin。用于创建各种颜色选择器 ui 的表单](https://www.syncfusion.com/xamarin-ui-controls/)控件。在这个 [GitHub 位置](https://github.com/SyncfusionExamples/syncfusion-xamarin-forms-color-picker-demo)可以找到这个例子。我们邀请您来看看我们的 [Xamarin。表单控件](https://www.syncfusion.com/xamarin-ui-controls/)。您可以随时[下载我们的免费评估](https://www.syncfusion.com/downloads/xamarin)来查看所有这些控件的运行情况。您也可以在 [Google Play](https://play.google.com/store/apps/details?id=com.syncfusion.samplebrowser) 和[微软商店](https://www.microsoft.com/en-us/p/syncfusion-essential-studio-for-xamarin/9nn069tldzf4)上探索我们的样品。在[我们的文档](https://help.syncfusion.com/xamarin/)中了解高级功能。

如果您对这些控制有任何疑问或需要澄清，请在下面的评论中告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)、[直接跟踪](https://www.syncfusion.com/support/directtrac/)或[反馈门户](https://www.syncfusion.com/feedback/xamarin-forms)联系我们。我们很乐意为您提供帮助！

在 Xamarin 中可视化颜色选择器控件的 5 种不同方法。表单首先出现在 [Syncfusion 博客](https://blog.syncfusion.com/blogs)上。