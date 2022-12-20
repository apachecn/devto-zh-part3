# 一些 Windows Phone 7 开发技巧

> 原文：<https://dev.to/onmyway133/some-windows-phone-7-development-tips-ahc>

这篇文章是很久以前我做 Windows Phone 7 的时候写的。我学到的许多东西今天仍然适用

欢迎回来，今天我将开始写所有的东西，所有我在开发 Windows Phone 7 时透露的秘密。

1.  当将`UIElement`的`Visibility`设置为`Collapsed`时，OS 将其`Height`设置为 0。因此，如果你需要有一个 0 的高度，只需设置`Height="0"`，这在显示时间方面更快。

2.  清除数据源时，使用`VirtualizingStackPanel`的`ListBox`不会记住它最后的视图，而`StackPanel`会

3.  `ListBox`里面的`ScrollViewer`会失去它的 UI 虚拟化，即使你用了`VirtualizingStackPanel`

4.  使用`GestureListener`不正确会导致其他`UIElement`不工作，即`Slider`，...

5.  `AdControl`、`ContextMenu`可能是内存泄漏的来源。

6.  当只有 1 个`PanoramaItem`时，全景控制仍然能够滑动，而`Pivot`则不能。

7.  枢轴控制将加载当前的`PivotItem`，以及它的直接左右`PivotItem`。

8.  有时 SIP 键盘不显示，请检查您的手机的物理键盘问题。

9.  无法为第三个应用程序评价歌曲

10.  当模拟器渲染有重叠问题时，解决方法是将`LayoutRoot`的`Background`设置为纯色

11.  假设您正在使用`ObservableCollection` bindSource 作为`ListBox`的绑定源。当快速向 bindSource 添加项目时，`ListBox`似乎会抑制创建`ListBoxItem`，直到所有项目都被添加。建议[批量](http://blogs.msdn.com/b/rohantha/archive/2010/09/12/silverlight-wp7-list-scroll-with-items-as-image-description-from-web-bing-image-search.aspx)增加项目

12.  `WebBrowser`控件没有滚动条

13.  尽可能使用`StoryBoard`,因为它的所有动画都是在合成器线程中完成的，这利用了 GPU

14.  使用`ProgressIndicator`获得最佳性能和 UX 熟悉度

15.  折叠不重要的`UIElement`以减少加载时间，并在页面完全加载时显示它们。

16.  总是取消订阅当页面被导航时可以继续运行的事件源，因为这可能导致内存泄漏。例子有定时器、`BackgroundAudioPlayer.PlayeStateChanged`、...

17.  使用文件比本地数据库快得多

18.  记住查询字符串的 260 个字符的限制

原帖[https://github.com/onmyway133/blog/issues/156](https://github.com/onmyway133/blog/issues/156)