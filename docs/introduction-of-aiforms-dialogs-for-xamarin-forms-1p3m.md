# AiForms 简介。Xamarin 的对话框。形式

> 原文：<https://dev.to/muak_x/introduction-of-aiforms-dialogs-for-xamarin-forms-1p3m>

说到 Xamarin 的对话。Forms， [ACR 用户对话框](https://github.com/aritchie/userdialogs)非常有名，我在业务中用过很多。
在个人发展中，我用的不是 it，而是使用 DependencyService 自我实现。我用它实现了一个加载对话框。
在 Android 上，我使用了 ProgressDialog，这变成了弃用，所以我想重构它。
当我重构它的时候，我偶然发现“这可以通过一点努力独立为一个库”。虽然结果很不容易，但我几乎完成了它，所以我发布了一个 NuGet 包。

首先，我认为我想取代自实现的 ACR 用户对话框。但是它的 GitHub 库并不是活动的，也不可定制，比如替换一个加载图标。所以我想我应该自己做一个图书馆。

在实现它的时候，我在我的 twitter 时间轴上发现了[弹出页面插件](https://github.com/rotorgames/Rg.Plugins.Popup)。我认为这个图书馆正是我想要的。但是因为那时我已经实现了它的大部分，我决定完成它以区别于弹出页面插件。

## 储存库

### 来源

[https://github.com/muak/AiForms.Dialogs](https://github.com/muak/AiForms.Dialogs)

### Nuget

[https://www.nuget.org/packages/AiForms.Dialogs/](https://www.nuget.org/packages/AiForms.Dialogs/)

> 这是目前的预发布版本。

## 演示

[https://www.youtube.com/embed/-DzMvQCQU1Q](https://www.youtube.com/embed/-DzMvQCQU1Q)

## 总结

AiForms。Dialogs 就是可以把一个由 XAML 或 c#代码定义的 ContentView 放到每个平台的原生对话框中并显示出来。

对话框的位置可以用`VerticalLayoutAlignment`、`HorizontallayoutAlignment`、`OffsetX`和`OffsetY`属性指定在屏幕的任何位置。

## 特性

*   对话框–显示常规对话框。
*   敬酒——展示什么叫做敬酒。
*   loading–显示用于加载的全屏模式对话框。

## 用法

```
var ret = await Dialog.Instance.ShowAsync<MyDialogView>(new{Title="Hello"});
// If canceled, return false. If completed, return true.
// Optionally, you can pass a ViewModel to a DialogView.
// When the dialog is closed, all related resources are automatically disposed of.

Toast.Instance.Show<MyToastView>(new{Title="Hello",Duration=1500});
// Optionally, you can pass a ViewModel to a ToastView.

await Loading.Instance.StartAsync(async progress =>{
    // Heavy process
    for (var i = 0; i < 100; i++)
    {
        await Task.Delay(50);
        // You can send the progress to a dialog with IProgress<double> value.
        progress.Report((i + 1) * 0.01d);
    }
}); 
```

有关如何使用 AiForms 的更多信息。对话框，参见[自述文件](https://github.com/muak/AiForms.Dialogs/blob/master/README.md)。

## 总结性的

如果你能使用这个库在 NETStandard 项目中使用一个精心制作的对话框，我将不胜感激。

如果有一些问题、请求、bug 报告，请报告给 GitHub issues 或者 [twitter](https://twitter.com/muak_x) 。