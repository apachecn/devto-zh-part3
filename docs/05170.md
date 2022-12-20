# 优化 Xamarin 图表性能的 7 个技巧

> 原文：<https://dev.to/syncfusion/7-tips-to-optimize-xamarin-charts-performance-3m1h>

可视化原始数据在任何应用程序中都很重要，这样您就可以快速清晰地理解大量数据，并比较不同种类的数据集。折线图是最常用的图表类型之一。它由直线上的一系列数据点表示。它应该能够可视化大量的数据点，这是大多数应用程序的典型要求。

我们构建这个图表时考虑到了绘制大量数据点，并包括了一些优化来处理大量数据点，同时仍然为用户提供流畅的体验。尽管如此，当加载超过 100K 的数据点时，可以在应用程序级别进行更多的优化以获得最佳性能，这些技术在这篇博客中有所解释。

## **#1:用快线系列代替线系列**

line 系列是为提供各行的丰富定制而构建的，因此它不适合加载大量数据点和执行实时数据更新。然而，我们也有 FastLineSeries 系列。它通常用于加载大量的数据点，因为它使用单个点集合呈现线条。FastLineSeries 的配置(例如设置项目源和绑定路径)与任何其他系列相同。以下代码片段显示了如何在 XAML 代码中配置 fast line 系列。

```
<chart:SfChart.Series>
      <chart:FastLineSeries XBindingPath="XValue" YBindingPath="YValue" ItemsSource="{Binding Data}"/>
</chart:SfChart.Series> 
```

## **#2:不使用数据标记，使用轨迹球或工具提示**

当您绘制许多数据点时，为每个数据点绘制数据标记和标签看起来会很混乱。然而，在所需的索引处跟踪数据点是很重要的。这就是工具提示和轨迹球发挥作用的地方。当您触摸任何数据点时，工具提示将显示数据。类似地，当您在图表区域上触摸并按住轨迹球时，它会显示最近的数据点的信息以及一条垂直线。使用内置的 API 和数据模板自定义轨迹球和工具提示标签的外观。下面的代码片段显示了如何启用工具提示和轨迹球，以及如何使用数据模板定制它们的标签。

使用数据模板启用和自定义工具提示。

```
chart:FastLineSeries  EnableTooltip="true"> 

     <chart:FastLineSeries.TooltipTemplate> 
           <DataTemplate> 
                <Label Text="{Binding TooltipString}" /> 
           </DataTemplate> 
     </chart:FastLineSeries.TooltipTemplate> 

</chart:FastLineSeries> 
```

启用轨迹球。

```
<chart:SfChart.ChartBehaviors>
      <chart:ChartTrackballBehavior/>
</chart:SfChart.ChartBehaviors> 
```

用数据模板定制轨迹球标签

```
<chart:FastLineSeries>

    <chart:FastLineSeries.TrackballLabelTemplate>
                  <DataTemplate>
                        <Label Text="{Binding TooltipString}" />
                  </DataTemplate>
    </chart:FastLineSeries.TrackballLabelTemplate > 

</chart:FastLineSeries> 
```

## **#3:将快线条系列的笔画宽度减少为 1**

默认情况下，快速线条系列的描边宽度为 2。如果用两个像素而不是一个像素来渲染线条，会导致延迟。因此，如果您没有任何特定要求来呈现粗细为 2 的线条，请将 FastLineSeries 的 StrokeWidth 设置为 1。线条的粗细可以在运行时增加。您可以在加载时以 1 的厚度渲染线条，在缩放到某一级别后，您可以将厚度增加到 2 或更多，以便清楚地看到线条。下面的代码片段演示了如何配置线条的粗细。

```
<chart:SfChart.Series> 
       <chart:FastLineSeries StrokeWidth="1" /> 
</chart:SfChart.Series> 
```

## **#4:利用暂停系列通知和简历通知**

将数据绑定到 ChartSeries 的 ItemsSource 属性后，如果图表实现了 INotifyCollectionChanged 接口，它将始终侦听数据中发生的更改。但是在某些情况下，您可以向数据集合中添加批量对象。如果您认为图表中的这些更新没有任何意义，您可以停止对 items source 集合中的每个修改更新图表。图表控件可以使用 SuspendSeriesNotification 和 ResumeSeriesNotification 方法。调用 SuspendSeriesNotification 方法后，图表将不会响应 items 源集合中的任何更改。在调用 ResumeSeriesNotification 方法后，它将使用最新数据进行更新。请参考下面的代码片段。

```
Chart.SuspendSeriesNotification();

//Make the required changes in the items source that don’t need to be updated in UI immediately.

Chart.ResumeSeriesNotification(); 
```

## **#5:禁用抗锯齿**

抗锯齿支持使线条呈现平滑的边缘。但是，当您绘制大量数据点时，这并不重要。使用 FastLineSeries 的 EnableAntiAliasing 属性禁用线条的抗锯齿功能。不过，只要您希望线条消除锯齿，请在运行时启用消除锯齿功能。下面的代码片段显示了如何禁用抗锯齿功能。

```
<chart:SfChart.Series>
       <chart:FastLineSeries EnableAntiAliasing="false"  />
</chart:SfChart.Series> 
```

## **#6:避免分类混乱，使用数轴和日期时间轴**

当您没有任何特定需要在 x 轴上显示字符串值时，请不要使用 CategoryAxis。CategoryAxis 根据各个数据点填充标签，而 NumericalAxis 和 DateTimeAxis 根据点的范围和间隔填充标签。当加载大量数据点时，这些轴总能提供良好的性能。下面的代码片段显示了如何将数轴配置为主轴。

```
<chart:SfChart.PrimaryAxis>
       <chart:NumericalAxis/>
</chart:SfChart.PrimaryAxis> 
```

## **#7:在 UWP 用 FastLineBitmapSeries 替换 FastLineSeries】**

因为 FastLineSeries 的默认呈现质量更好，所以 Xamarin。窗体 FastLineSeries 已经用它进行了映射，而不是 UWP 平台中的 FastLineBitmapSeries。FastLineSeries 使用多段线绘制数据，但 FastLineBitmapSeries 使用位图绘制数据，这比多段线渲染更快。要用 FastLineBitmapSeries 替换 FastLineSeries，您需要编写一个自定义渲染器，如下面的代码片段所示。

```
public class ChartRendererExt : SfChartRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            Native.SfChart chart = this.Control as Native.SfChart;
            chart.Series.Clear();

            //Replace the fast line series with fast line bitmap series
            foreach (var formsSeries in e.NewElement.Series)
            {
                if (formsSeries is FastLineSeries)
                {
                    Native.FastLineBitmapSeries fastLine = new Native.FastLineBitmapSeries();

                    formsSeries.PropertyChanged += (sender, a) =>
                    {
                        if(a.PropertyName.Contains("ItemsSource"))
                        {
                            fastLine.ItemsSource = formsSeries.ItemsSource;
                        }
                    };

                    var properties = SfChartRenderer.GetPropertiesChanged(typeof(ChartSeries), formsSeries);

                    foreach (var name in properties)
                    {
                        ChartSeriesMapping.OnXyDataSeriesPropertiesChanged(name, formsSeries as FastLineSeries,
                            fastLine);
                    }
                    chart.Series.Add(fastLine);
                }
            }
        }
    } 
```

所有上述技术都有助于改善加载时间，并在复杂的应用程序中提供流畅的实时更新。但是，性能仍然取决于设备及其配置。我们已经应用了这些优化，并用 500，000 个数据点测试了控件。它能在一秒钟内载入所有的 Xamarin。表单平台。我们用 iPhone 7 测试了 iOS，用 Google Pixel 测试了 Android，用一台 16GB 内存和英特尔酷睿 i5 第七代 7200U 的设备在 Windows 平台上进行了测试。

下面的 GitHub 存储库包含带有所有建议优化的示例项目。您可以下载或克隆它，以便在您的环境中执行该测试。

[Xamarin。表单图表性能示例](https://github.com/SyncfusionExamples/xamarin.forms-sfchart-performance)

请在下面的评论区分享您对本文的宝贵反馈。如果您对图表性能有任何具体要求，也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/) 联系我们。我们很乐意为您提供帮助！

*如果你喜欢这篇文章，我们相信你也会喜欢:*

*   【电子书】 *[Visual Studio for Mac 简洁明了](https://www.syncfusion.com/ebooks/visual-studio-for-mac-succinctly)*
*   【电子书】 *[Xamarin。](https://www.syncfusion.com/ebooks/xamarin-forms-succinctly)形式简洁*
*   【电子书】 [*Xamarin。简洁的 macOS 表单*](https://www.syncfusion.com/ebooks/xamarin_forms_for_mac_os_succinctly)
*   [博客文章][2018 年新功能第 4 卷:Syncfusion Xamarin 的亮点](https://blog.syncfusion.com/post/whats-new-in-2018-volume-4-highlights-for-syncfusion-xamarin.aspx)
*   [博文][2018 年新内容第 3 卷:Xamarin。表格地图更新](https://blog.syncfusion.com/post/whats-new-in-2018-volume-3-xamarin-forms-maps-updates.aspx)

帖子[优化 Xamarin Charts 性能的 7 个技巧](https://blog.syncfusion.com/post/7-tips-to-optimize-xamarin-charts-performance.aspx)首先出现在 [Syncfusion 博客](https://blog.syncfusion.com)上。