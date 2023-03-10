# 在 Xamarin 表单中实现一个简单的 TabView，零依赖

> 原文：<https://dev.to/herocod3r/implementing-a-simple-tabview-in-xamarin-forms-with-zero-dependencies--dm5>

TabView 在移动开发中是一个非常流行的视图，在 xamarin 表单中没有 tabview 实现，*边注*这就是为什么我喜欢 xamarin 表单，给你火石，让你生火，听起来很有压力吧？，但在我看来，这是一笔资产...这是我们与众不同的地方...它们赋予您对抽象和本机实现的控制权。

回到主题，最近我正在构建一个应用程序，UI 的一部分是一个 tabview，其中一些其他静态视图将被放置在视图之上，自动地，我知道表单中的默认 tab 页面将不起作用...经过一些谷歌搜索，我发现了这个 [TabView](https://github.com/chaosifier/TabView) 查看它，我发现它在 TabView 中使用了一个 carousel 插件的实现，虽然这很好，但我只需要一些轻的东西，没有依赖性。

所以，经过一番头脑风暴后，我想，tabview 实际上是一个扩展内容的单一视图，可以向右滚动吗？，那么扩展视图的最佳方式是什么..在应用中？...然后嘣！！像一道光**卷视图**前来救援。

理论很简单

*   在禁用滚动的情况下获得水平滚动视图
*   具有 100%屏幕宽度的视图
*   以编程方式滚动视图(选项卡)以在选项卡之间移动

所以

**第一步**我创建了一个 TabScrollView，带有一个禁用滚动的属性
**第二步**创建了 android 和 ios 的渲染器来处理这个问题，因为默认情况下没有禁用滚动的选项。在 ios 上有一个禁用滚动的属性，在 android 上，这有点棘手，我们需要捕获滚动事件，而不是处理它
**步骤 3** 创建了一个 tabitem，一个带有`HeaderText`属性的普通 content view
**步骤 4** 在 TabView 上这个技巧很简单有一个 TabItem 的集合，为 HeaderText 创建带有指示器的视图..然后将 TabItem 内容添加到 tabview 的主体中，并将宽度设置为其父级的宽度。

在表单中，没有办法在初始化时得到父宽度，布局必须先测量，宽度随后给出...因此，在初始化时，我只是分配了一个恒定的宽度...对于预览器来说。

然后我不得不重写这个方法来设置宽度

```
 protected override void LayoutChildren(double x, double y, double width, double height)
        {

            this.width = width;
            base.LayoutChildren(x, y, width, height);
            foreach (var item in body.Children)
            {
                item.WidthRequest = width;
            }
            InvalidateMeasure();

        } 
```

因此宽度现在是其父级的 100%。

最后一个难题是如何通过程序滚动标签，scrollview 有一个通过 X/Y 坐标滚动视图的方法，在我们的例子中，由于身体视图具有相同的宽度，它们的 X 坐标是宽度的倍数，所以第一个视图将是 *0* ，第二个视图是*宽度*，第三个视图是*宽度*2* 等等

所以这很简单，只需将你想看的标签的索引乘以宽度

```
 await scroll.ScrollToAsync(width * SelectedIndex, 0, true); 
```

是的，基本上就是这样，这个方法很棒，你可以在标签体中放任何你想要的东西...即使是滚动视图，也能完美地工作，零依赖性，你可以把视图放在标签视图的上面或下面...基本上这是一个正常的观点。

[![drawing](img/cc2dde2e14f62aa54265417b1c93278e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o_fPHPa7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jyxdpbov604me6lowo0w.gif)

> ### Here you can see the complete source code

总之，滚动视图就像胶带一样，你可以用它做很多事情，甚至旋转木马，水平列表视图等等

**PS** 这是我第一次写我的开发者经历，如果我的文章很怪异请见谅