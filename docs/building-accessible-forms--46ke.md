# 构建可访问的(A11y) HTML 表单

> 原文：<https://dev.to/finallynero/building-accessible-forms--46ke>

### 什么是可达性(a11y)？

按照[维基百科](https://en.wikipedia.org/wiki/Accessibility)的说法，就是为残疾人设计产品、设备、服务或环境。软件开发中的可访问性就是构建每个人都可以使用的应用程序。

这可能是你第一次听说或读到与软件开发相关的可访问性，或者你可能听说过它，但你没有真正研究它，可访问性是你正在构建的任何应用程序的重要部分。它不应该是某个功能，而应该是构建 web 应用程序时的一个标准，因为重要的是，无论每个人的能力、残疾或语言如何，都可以平等地访问 web。在此之前，我还没有开发出最易访问的网络应用程序，我想向那些不得不使用我糟糕的应用程序的人道歉。让您的 web 应用程序具有可访问性并不难实现，只是在大多数情况下需要您使用语义 HTML。

所以在这篇文章中，我将简要地解释如何使你的(糟糕的)表单具有可访问性。

#### 标注表单控件。

这可以使用标签元素`<label>`来完成。表单标签很重要，因为它们告诉用户在表单控件中输入什么，想象一个没有任何标签表单，你怎么知道你需要在那个字段中输入什么。你可能会想“占位符是什么？”，占位符不能替代`label`，因为屏幕阅读器不会像对待标签一样对待占位符文本，并且占位符文本没有得到辅助技术的广泛支持。

所以在你的表单中为所有的表单控件提供标签是很重要的，这包括文本输入、复选框、单选按钮等等。简单地在表单控件的上面/下面放一个标签是不够的，语义 HTML 为我们提供了一种将标签与特定的表单控件相关联的方法，为此表单控件的`Id`必须匹配标签的`for`属性。

[https://codepen.io/finallynero/embed/KbRJry?height=600&default-tab=result&embed-version=2](https://codepen.io/finallynero/embed/KbRJry?height=600&default-tab=result&embed-version=2)

上面的演示展示了如何标记表单控件。您可能没有注意到，这个标签充当了一个聚焦表单控件的可点击区域。

#### 分组表单控件。

将相关的表单域分组很重要，因为这样用户更容易理解表单，也更容易输入适当的值。
看看下面的例子

```
 <form>
               <p>Do you prefer remote work</p>

               <input type="radio" id="yes">
               <label for="yes">Yes</label>

               <input type="radio" id="no">
               <label for="no">No</label>

            </form> 
```

Enter fullscreen mode Exit fullscreen mode

从视觉上看，该表单没有任何问题，但是如果您使用屏幕阅读器来浏览该表单，您将会听到表单标签“是”或“否”，但是您不知道正在询问什么问题。

使用`fieldset`元素和`legend`元素作为该组的标题，可以很容易地对相关的表单域进行分组。

[https://codepen.io/finallynero/embed/RvyRQE?height=600&default-tab=result&embed-version=2](https://codepen.io/finallynero/embed/RvyRQE?height=600&default-tab=result&embed-version=2)

这只是一个使表单可访问的介绍，你可以从下面的链接获得更多信息

[W3C Web Accessibility Initiative](https://www.w3.org/WAI/)
[Web Accessibility in Mind](https://webaim.org/techniques/forms/)
[a11y matters](https://www.a11ymatters.com/)