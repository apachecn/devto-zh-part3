# 解开框架绑定适配器

> 原文：<https://dev.to/zmdominguez/unwrapping-framework-binding-adapters-nfc>

在过去一年左右的时间里，我的团队一直在全力进行数据绑定。如果你了解我的话，我会很高兴的！

随着时间的推移，该团队已经从将数据绑定视为 Butterknife 的替代品转向利用更多的数据绑定功能。事实上，我们已经建立了一个`@BindingAdapter`的兵工厂！

这正是促使我审视我们目前所拥有的一切的原因。我注意到一件奇怪的事情。我们写了一些我们并不真正需要的绑定适配器。

例如，我们有这个听 IME 动作按钮:

```
@BindingAdapter("onEditorActionClicked")
fun onEditorActionClicked(editText: EditText, editorActionListener: EditTextEditorActionListener) {
    editText.setOnEditorActionListener { _, actionId, _ ->
        editorActionListener.onEditorActionClicked(actionId)
        false
    }
} 
```

事实是，我们并不真的需要编写这个定制`BindingAdapter`,因为框架中已经存在这样一个东西。

虽然我们错过了这一点是可以理解的，因为关于这些东西的文档有点难以获得。即使我们查看框架源代码，不太熟悉数据绑定的人可能会发现它有点难以解析。

所以让我试着帮一点忙。

每当我们想要实现一个`BindingAdapter`——特别是如果它要挂钩到一个现有的监听器——第一步应该是查看在框架中实现的绑定适配器。有一大堆是为[各种小工具](https://android.googlesource.com/platform/frameworks/data-binding/+/master/extensions/baseAdapters/src/main/java/android/databinding/adapters)写的(也就是说[除了 AndroidX SearchView](https://issuetracker.google.com/issues/122856766) ，不过那是后话了)。

既然我们要与 IME 行动合作一个`EditText`，我们就应该看看 [`TextViewBindingAdapter`](https://android.googlesource.com/platform/frameworks/data-binding/+/master/extensions/baseAdapters/src/main/java/android/databinding/adapters/TextViewBindingAdapter.java) 。

在文件的顶部，我们看到这样的注释:`@BindingMethod(type = TextView.class, attribute = "android:onEditorAction", method = "setOnEditorActionListener")`

[文档](https://developer.android.com/topic/libraries/data-binding/binding-adapters#specify-method)掩盖了这意味着什么；但简而言之:

*   `attribute` =当该属性出现在布局文件中时
*   然后在这个类中寻找实现
*   `method` =在`type`中定义的类中具有该名称的方法

(注意:如果您对数据绑定库如何自动设置侦听器和管理接口感兴趣，我强烈建议您检查您的布局的生成绑定文件。)

哦，请注意`setOnEditorActionListener`是我们在自己的自定义`BindingAdapter`中实际调用的方法！看来我们有所发现了！

让我们试着使用这个属性:

```
<EditText
    android:id="@+id/sample_edit_action"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:inputType="text"
    android:imeOptions="actionSend"
    android:onEditorAction="@{() -> handlers.onEditorActionClicked()}"/> 
```

(注意:如果你不熟悉我在这里使用的 lambda 语法，这被称为“监听器绑定”,你可以在这里阅读所有相关内容。)

(第二个注意:Lint 会告诉你没有这个属性。林特撒谎！)

下一步我们要做的是告诉我们的`handlers`当`onEditorAction`被调用时需要发生什么。为了简单起见，假设我们想在用户点击发送按钮时显示一个`Toast`。

```
fun onEditorActionClicked() : Boolean {
    Toast.makeText(this, "Send was clicked!", Toast.LENGTH_LONG).show()
    return false
} 
```

实现相当简单，但有一点很重要: ***确保实现与接口*** 具有完全相同的返回值类型(否则数据绑定[会陷入 StackOverflowException](https://issuetracker.google.com/issues/123260053) )！

如果我们真的需要`KeyEvent`或者`ActionId`呢？让我们更新我们的实现，将这些因素考虑在内:

```
fun onEditorActionClicked(view: TextView, actionId: Int?, event: KeyEvent?) : Boolean {
    when(actionId) {
        EditorInfo.IME_ACTION_SEND -> {
            Toast.makeText(this, "Send was clicked!", Toast.LENGTH_LONG)
                    .show()
        }
    }
    return false
} 
```

让我们也更新一下我们的布局文件:

```
<EditText
    android:id="@+id/sample_edit_action"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:inputType="text"
    android:imeOptions="actionSend"
    android:onEditorAction="@{(v, id, event) -> handlers.onEditorActionClicked(v, id, event)}"/> 
```

我们终于可以删除我们的客户了！🎉

❗️还记得，默认情况下，数据绑定会查找以`set`为前缀的方法。这意味着，如果我们可以以编程方式调用 setter，我们就不必为它创建一个`BindingAdapter`。一些例子有`setEnabled()`、`setBackgroundColor()`等。

所以总而言之:

*   🙅如果我们想调用一个已有的 setter，不需要定制`BindingAdapter`。
*   🙅如果我们想连接一个现有的接口，不需要定制`BindingAdapter`。
*   🙆实现侦听器绑定时，返回值必须完全匹配
*   💁当有疑问时，探索现有的框架绑定

祝装订愉快！