# 为结果开始一个片段

> 原文：<https://dev.to/adammc331/starting-a-fragment-for-a-result-3o1>

许多开发人员鼓吹 Android 上的单一活动架构，这也是我一直努力推进的事情。然而，在这个过程中，我遇到了一个棘手的问题。我没有像`startActivityResult`那样的碎片。如果你不熟悉，`startActivityForResult`是一个允许你用一个特定的请求代码启动一个活动的方法，当那个活动结束时，你的第一个活动将在`onActivityResult`中得到一个回调，并可以用它做一些事情。

这篇文章将介绍我们如何使用片段来达到同样的效果。

## 举例

为了理解这个概念，我们将编写一个包含两个片段的小应用程序。一个是我们的`DisplayNameFragment`，它显示类似“你的名字是:Adam”的消息，有一个按钮将导航到一个新的片段`SetNameFragment`，在这里你可以在一个编辑文本中输入一个名字，点击一个完成按钮，它将返回到前一个片段并显示你输入的任何内容。这将是它的样子:

[![Sample](img/d303cf975d429347fee9c5663174b9f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iaeu5grE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zxqjpf90pbi0cnfieitw.gif)

## 目标碎片

关键是使用一个目标片段。当一个片段从另一个片段开始时，以及当第一个片段想要返回结果时，使用[目标片段](https://developer.android.com/reference/android/app/Fragment.html#setTargetFragment(android.app.Fragment,%20int))。我经常在对话片段中使用这个概念，但是没有意识到同样的概念也可以应用到其他片段中。

## 起始片段

第一步是从`DisplayNameFragment`内部开始我们的`SetNameFragment`。我们可以通过在按钮的点击监听器中放入下面的代码来做到这一点:

```
 private fun launchSetNameFragment() {
      val newFragment = SetNameFragment()
      val tag = SetNameFragment::class.java.simpleName

      newFragment.setTargetFragment(this, SET_NAME_REQUEST_CODE)
      (activity as? MainActivity)?.replace(newFragment, tag)
  } 
```

上面的`MainActivity.replace()`方法将用传递给它的新片段替换容器中现有的片段。我们使用 replace 这个事实很重要，我们一会儿会讲到。

## 返回结果

适当命名后，传递到`setTargetFragment()`的片段可以在新片段的`getTargetFragment()`中检索到。因此，在我们的`SetNameFragment`中，我们可以通过在按钮监听器中放入以下代码来传回结果:

```
 private fun returnWithName() {
      val name = name_input.text.toString()

      (targetFragment as? DisplayNameFragment)?.setName(name)
      activity?.supportFragmentManager?.popBackStackImmediate()
  } 
```

在这个例子中，我们*期望*目标片段是 DisplayNameFragment 的一个实例。我的第一反应是写一个基本的/通用的方法来处理这个问题，但是我决定不过度设计它。

当输入一个名字时，我们通过调用`setName()`将结果传递回`DisplayNameFragment`，然后告诉活动将这个片段从顶部弹出，这将重新显示我们的`DisplayNameFragment`。

## 处理结果

在我向您展示上面的`setName()`方法的代码之前，有必要提供一些片段回溯的背景知识。你如何使用背刺将决定你如何处理结果。具体来说，这可能取决于您使用的是`add`还是`replace`交易:

```
 // Adds a fragment on top of whatever might already be in the container.
  supportFragmentManager.beginTransaction()
     .add(R.id.container, newFragment, tag)
     .addToBackstack(tag)
     .commit()

  // Replaces whatever is in the container with the new fragment.
  supportFragmentManager.beginTransaction()
     .replace(R.id.container, newFragment, tag)
     .addToBackstack(tag)
     .commit() 
```

在`add`的例子中，一个新的片段被放在容器的顶部。前面的片段不会发生任何变化。不调用任何生命周期事件，如`onPause()`或`onDestroyView()`。

在`replace`的情况下，任何现有的片段都从容器中移除，并显示一个新的片段。这意味着前面的片段贯穿了几个生命周期方法，包括`onPause()`和`onDestroyView()`。虽然它不会一直到`onDestroy()`，但是这个片段仍然存在。

我强烈推荐[这个关于栈溢出的答案](https://stackoverflow.com/questions/18634207/difference-between-add-replace-and-addtobackstack/48106957#48106957)来看看生命周期的区别。

### 使用添加

如果您正在使用一个添加事务，这个片段在屏幕上仍然可见(就在前一个的后面)，所以您可以在处理结果时自由地直接操作视图:

```
 fun setName(name: String) {
      name_textview.text = getString(R.string.your_name_is).format(name)
  } 
```

### 使用替换

但是，如果使用 replace，上面的代码会崩溃。这是因为`DisplayNameFragment`视图被破坏了，因此它可以被新的视图所取代。上面的代码试图访问一个不存在的视图，所以它会崩溃。为了处理这个问题，我们可以在一个类级别的变量中设置名称，并在`onResume()`中读取它来更新我们的文本视图:

```
 class DisplayNameFragment : Fragment() {

      private var name: String = ""

      override fun onResume() {
          super.onResume()

          name_textview.text = getString(R.string.your_name_is).format(name)
      }

      fun setName(name: String) {
          this.name = name
      }

      // ...
  } 
```

这段代码甚至不能在 add 的情况下工作，因为`onResume()`不会在 back press 上被调用。

虽然这样做需要更多的代码，但我仍然更喜欢使用替换事务，但这取决于您和您的需求。请注意，您的选择将影响您如何在片段之间传递信息。

## 结论

TL；您可以使用目标片段在片段之间传递数据，就像使用`startActivityForResult()`和`onActivityResult()`一样。您只需要知道`add`和`replace`事务如何影响片段生命周期，以及如何处理每种情况下的结果。

如果你想看完整的样本，你可以在 GitHub 上找到这个项目。