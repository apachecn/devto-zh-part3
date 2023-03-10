# 自定义视图生命周期和处理 onMeasure()

> 原文：<https://dev.to/amrfarid140/custom-views-lifecycle-and-handling-onmeasure-47b0>

这最初发布在我的博客上。

当构建您自己的视图(自定义视图)时，您需要考虑一些事项。首先，让我们介绍一个视图的生命周期。
[![View lifecycle](img/28884bfece8aacd15c6b59e6c547d404.png "View Life Cycle")](https://res.cloudinary.com/practicaldev/image/fetch/s--6lLE8WZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.amryousef.me/media/android_view_lifecycle.png) 
*[图像原点](https://codentrick.com/android-view-lifecycle/)*
你需要担心的最重要的函数是- `onMeasure()` - `onLayout()` - `onDraw()`

## 进行测量

onMeasure 提供了一种协商视图宽度和高度的方法。
如果你不覆盖它，当`match_parent`和`wrap_content`被用来定义它的尺寸时，你的视图会以同样的方式运行。您的视图也将考虑为高度和宽度传递的值。如果你选择在定义视图大小时有更多的控制权，那么你可能需要覆盖它，不要调用`super.onMeasure()`。相反，你应该调用`setMeasuredDimension(width,height)`。

### 覆盖 onMeasure()

当 onMeasure 被调用时，你得到`widthMeasureSpec`和`heightMeasureSpec`。这个*规格*是父视图通知您视图所需尺寸和尺寸模式的一种方式。尺寸模式是父视图为其子视图设置的约束。非此即彼——`UNSPECIFIED`:父母没有对孩子施加任何约束。它可以是它想要的任何大小。

*   父母已经为孩子确定了一个准确的尺寸。不管孩子想要多大，都要给他们这些界限。
*   孩子可以想要多大就有多大，直到指定的尺寸。记住这一点，这里有一个关于如何实现`onMeasure()`的例子

```
override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        val requestedWidth = MeasureSpec.getSize(widthMeasureSpec)        
        val requestedWidthMode = MeasureSpec.getMode(widthMeasureSpec)
        val requestedHeight = MeasureSpec.getSize(heightMeasureSpec)        
        val requestedHeightMode = MeasureSpec.getMode(heightMeasureSpec)
        val desiredWidth: Int = //TODO("Define your desired width") 
        val desiredHeight: Int = //TODO("Define your desired height")
        val width = when (requestedWidthMode) {            
               MeasureSpec.EXACTLY -> requestedWidth                        
               MeasureSpec.UNSPECIFIED -> desiredWidth
               else -> Math.min(requestedWidth, desiredWidth)        
        }
        val height = when (requestedHeightMode) {            
                MeasureSpec.EXACTLY -> requestedHeight 
                MeasureSpec.UNSPECIFIED -> desiredHeight
                else -> Math.min(requestedHeight, desiredHeight)        
        }
        setMeasuredDimension(width, height)
} 
```

如果模式是`EXACTLY`,这是将大小设置为请求的大小，因为我们必须尊重父节点请求的模式。如果模式是`UNSPECIFIED`，那么我们可以随意使用我们想要的大小，这通常是`wrap_content`的情况。如果模式是`AT_MOST`，那么我们比较`desired`和`requested`的尺寸，并使用最小值。

## onLayout

父节点使用`onLayout()`来通知你它的位置。你可以知道位置是否改变，以及你的视图的左、上、右、下位置。您应该使用来计算您的绘图宽度和高度。
记住`onMeasure()`发生的事情会影响你从父母那里得到的职位。在继续绘制视图之前，应始终在此处计算绘图尺寸。

## 翁德拉

这是所有绘画发生的地方。就像你得到一个`Canvas`对象的实例一样简单，你可以随意绘制你想要的东西。

## 性能考虑

您的`onDraw()`实现应该是高效的。您应该避免任何长循环或创建新实例。谷歌的官方文档说“你的应用程序应该在 16 毫秒内渲染帧，以达到每秒 60 帧”。
查看[这篇文档](https://developer.android.com/topic/performance/vitals/render.html)了解更多关于解决渲染缓慢的信息。