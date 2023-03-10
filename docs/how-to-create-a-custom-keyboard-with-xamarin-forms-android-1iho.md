# 如何用 Xamarin Forms 创建自定义键盘(Android)

> 原文：<https://dev.to/fabribertani/how-to-create-a-custom-keyboard-with-xamarin-forms-android-1iho>

前一段时间，一个客户要求为他的应用程序提供一个特殊的键盘，这个键盘必须具备常规 Android 键盘所不具备的某些条件。

### **正在研究**

我想到的第一个选择是添加一个禁用的`Entry`和一个`GestureRecognizer`来显示一个带有动画的控件，以模拟键盘的外观，但很快就放弃了这个想法，因为它不可重用。
最好的选择是走本地道路，所以我花了相当多的时间研究，但我只找到了创建键盘即服务的解决方案。我知道我们的客户不会喜欢必须下载一个单独的键盘以便只在应用程序中使用的想法，我需要一个能够处理 Xamarin 表单的解决方案！
由于我不得不考虑它在 Xamarin 表单中的实现，我决定最好的选择是尝试使用入口控件的[自定义渲染器](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/custom-renderer/)，因为它使用 *EditText* 作为本机控件的基础，并尝试应用我之前在其中读到的所有解决方案。

### **我们开始工作吧！**

注意:为了正确实现自定义键盘，我们需要 Xamarin Forms 版本为`3.6.0.135200-pre1`或更高版本，因为我们需要 [OnFocusChangeRequest](https://github.com/xamarin/Xamarin.Forms/blob/release-3.6.0-rc1/Xamarin.Forms.Platform.Android/Renderers/EntryRenderer.cs#L113) 方法，该方法仅在该版本中可用。

首先，我们将创建自定义控件，它将具有下一个可绑定属性:

*   `EnterCommand` : typeof `ICommand`，绑定*回车*按键动作。

这就是我们的自定义控件: