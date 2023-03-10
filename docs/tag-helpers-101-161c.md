# 标签助手 101

> 原文：<https://dev.to/turnerj/tag-helpers-101-161c>

为了我的一个大型网络项目，我最近从全职。NET 框架到。网芯。你的武器库中有许多新工具，其中之一就是[标签助手](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-2.2)。

这篇文章将介绍使用标签助手和自己制作标签助手的基础知识。它要求你对 ASP.NET 有一个基本的了解，尤其是表格。

以下示例将基于以下模型:

```
class PersonViewModel
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 背景

在标记帮助器出现之前，类似表单的东西在 Razor 模板中通常是这样的:

```
@using (Html.BeginForm("Edit"))
{
    @Html.AntiForgeryToken()

    <div class="field">
        @Html.LabelFor(m => m.FirstName)
        <div class="input-container">
            @Html.TextBoxFor(m => m.FirstName)
        </div>
        @Html.ValidationMessageFor(m => m.FirstName)
    </div>
    <div class="field">
        @Html.LabelFor(m => m.LastName)
        <div class="input-container">
            @Html.TextBoxFor(m => m.LastName)
        </div>
        @Html.ValidationMessageFor(m => m.LastName)
    </div>
    <div class="field">
        <button>Continue</button>
    </div>
} 
```

Enter fullscreen mode Exit fullscreen mode

像`@helper`这样能让事情变得更好的事情并没有出现([目前是](https://github.com/aspnet/AspNetCore/issues/5110))。网芯。你可以使用[局部视图](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/partial?view=aspnetcore-2.2)，但在这里这并不是一个很好的选择。您可以查看[视图组件](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/view-components?view=aspnetcore-2.2)，但它确实不是这里的合适工具。

让我们看看标记帮助器如何使它更容易阅读，并避免一些通常困扰 Razor 模板的语法高亮和支撑的奇怪现象。

## 基本标签助手

让我们看一下我们的`FirstName`属性的`input`标签助手。

```
<input asp-for="FirstName" /> 
```

Enter fullscreen mode Exit fullscreen mode

是的，就这样-我们结束了。我们有一个标签助手将`input`标签绑定到我们的`FirstName`属性。

*感谢阅读！喜欢，订阅评论！*

不，说真的，这是所有的`InputTagHelper`实际上是设置要求。通过指定`asp-for`，普通的 HTML 元素实际上变成了由`InputTagHelper`在服务器上处理的标签。

您将知道它是随着 Visual Studio 中标记颜色的变化而应用的:

[![Input Tag, with Tag Helper styling](img/0d8d3c372a5f1825e39c03571939d22e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Rb7-Iv6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/by5n2977qwm3i3jnu8we.png)

你可能会问为什么这比`@Html.TextBoxFor(m => m.FirstName)`更好？这实际上可以归结为灵活性、简单性和您可以使用标签助手执行的功能。

再来看另一个例子:

```
<form asp-controller="Person" asp-action="Edit" method="post">
    <!-- Input and Submit elements -->
</form> 
```

Enter fullscreen mode Exit fullscreen mode

`form`标签的标签助手实际上是启用的，不需要指定任何属性——标签助手的另一个很酷的特性。在上面的例子中，我使用了`asp-controller`和`asp-action`来设置表单提交的位置。

因为它仍然是*只是* HTML，没有什么可以阻止你添加一个`class`属性，并指定你需要的任何东西，就像你对任何其他 HTML 元素所做的那样。

```
<form asp-controller="Person" asp-action="Edit" method="post" class="my-super-special-form-class">
    <!-- Input and Submit elements -->
</form> 
```

Enter fullscreen mode Exit fullscreen mode

如果你想在使用旧方法的时候设置一个类，这会变得更加混乱:

```
using (Html.BeginForm("Edit", "Person", FormMethod.Post, new { @class = "my-super-special-form-class" })) 
```

Enter fullscreen mode Exit fullscreen mode

所以标签助手可以帮助你继续使用 HTML，就像你以前一样，而不需要使用 Razor 语法。标签助手也有更好的智能感知支持——在`asp-for`中看起来像字符串的东西实际上是模型表达式。对于您可能创建的其他自定义属性也是如此，一切都是类型安全的。

## 带标签助手的完整示例

现在你已经了解了标签助手是什么以及它们是如何工作的，让我们看看如果所有东西都是标签助手的话，第一个例子会是什么样子:

```
<form asp-action="Edit" method="post">
    <div class="field">
        <label asp-for="FirstName"></label>
        <div class="input-container">
            <input asp-for="FirstName" />
        </div>
        <span asp-validation-for="FirstName"></span>
    </div>
    <div class="field">
        <label asp-for="LastName"></label>
        <div class="input-container">
            <input asp-for="LastName" />
        </div>
        <span asp-validation-for="LastName"></span>
    </div>
    <div class="field">
        <button>Continue</button>
    </div>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

## 制作自己的标签助手

首先，我们可能应该回答:为什么我要制作自己的标签助手？

部分是因为它提供的灵活性，部分是因为它使 HTML 标记更容易使用，部分是因为你可以做一些真正酷的东西。

我们将添加我们自己的标签助手，根据我们设置的属性为我们定义一些类，使我们的“继续”按钮看起来更好。

要创建一个标签助手，你需要创建一个扩展了`TagHelper`的新类——我将把我的类命名为`ButtonStyleTagHelper`。它将使用属性`button-style`将自己应用到标签上。

如果您正在编写[实用程序优先的 CSS](https://frontstuff.io/in-defense-of-utility-first-css) ，那么标签助手的这个用例会非常有用。

我们需要在类上指定一些属性来定义我们希望我们的标签助手应用什么标签以及我们需要定义什么属性。

```
[HtmlTargetElement("button", Attributes = "button-style", TagStructure = TagStructure.NormalOrSelfClosing)]
[HtmlTargetElement("a", Attributes = "button-style", TagStructure = TagStructure.NormalOrSelfClosing)]
public class ButtonStyleTagHelper : TagHelper
{

} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子说明，只要`button-style`被设置为属性，我们就允许我们的标签助手为`<button>`和`<a>`工作。

现在让我们定义我们的属性将继续的属性及其类型:

```
[HtmlTargetElement("button", Attributes = "button-style", TagStructure = TagStructure.NormalOrSelfClosing)]
[HtmlTargetElement("a", Attributes = "button-style", TagStructure = TagStructure.NormalOrSelfClosing)]
public class ButtonStyleTagHelper : TagHelper
{
    [HtmlAttributeName("button-style")]
    public Style ButtonStyle { get; set; }

    public enum Style
    {
        Primary,
        Secondary
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将属性`ButtonStyle`显式绑定到属性`button-style`，并使用自定义的`Style`枚举，允许我们选择`Primary`或`Secondary`。

我们需要为`Style`枚举定义到我们想要的类的绑定:

```
private static readonly Dictionary<Style, string> ButtonStyles = new Dictionary<Style, string>
{
    { Style.Primary, "whatever-classes i-might-specifically want-for-all primary-button-styles" },
    { Style.Secondary, "the-classes i-might-specifically want-for-secondary button-styles" }
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了实际使用标签助手，我们需要覆盖`Process`方法并给它一些事情做。

```
public override void Process(TagHelperContext context, TagHelperOutput output)
{
    var classes = ButtonStyles[ButtonStyle];
    var classAttr = output.Attributes.Where(
        a => a.Name.Equals("class", StringComparison.InvariantCultureIgnoreCase)
    ).FirstOrDefault();

    if (classAttr != null)
    {
        output.Attributes.Remove(classAttr);
        classes += " " + classAttr.Value.ToString();
    }

    classAttr = new TagHelperAttribute("class", classes);
    output.Attributes.Add(classAttr);
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来有点令人生畏，但让我们来分解一下:

```
var classes = ButtonStyles[ButtonStyle];
var classAttr = output.Attributes.Where(
    a => a.Name.Equals("class", StringComparison.InvariantCultureIgnoreCase)
).FirstOrDefault(); 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们得到基于我们设置的按钮样式定义的类。属性`ButtonStyle`具有我们在 HTML 中定义的枚举值，所以我们只需将它传递给字典。

因为您可能已经在元素上定义了一个`class`属性，所以我们想确保我们只是预先考虑了我们的新类。

```
if (classAttr != null)
{
    output.Attributes.Remove(classAttr);
    classes += " " + classAttr.Value.ToString();
}

classAttr = new TagHelperAttribute("class", classes);
output.Attributes.Add(classAttr); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在检查是否有一个`class`属性，将它从标签中移除，并将这些类应用到为`ButtonStyle`定义的类中。

最后，我们重新创建一个新的属性对象，并将其应用于我们的标签。

在我们可以使用我们的标签之前，我们需要将它添加到`_ViewImports.cshtml`文件中，类似于`@addTagHelper *, <YourAssemblyName>`的东西会将该程序集中的所有标签助手添加到所有 Razor 模板中。

你的最终类应该看起来像这样:

```
[HtmlTargetElement("button", Attributes = "button-style", TagStructure = TagStructure.NormalOrSelfClosing)]
[HtmlTargetElement("a", Attributes = "button-style", TagStructure = TagStructure.NormalOrSelfClosing)]
public class ButtonStyleTagHelper : TagHelper
{
    private static readonly Dictionary<Style, string> ButtonStyles = new Dictionary<Style, string>
    {
        { Style.Primary, "whatever-classes i-might-specifically want-for-all primary-button-styles" },
        { Style.Secondary, "the-classes i-might-specifically want-for-secondary button-styles" }
    };

    [HtmlAttributeName("button-style")]
    public Style ButtonStyle { get; set; }

    public enum Style
    {
        Primary,
        Secondary
    }

    public override void Process(TagHelperContext context, TagHelperOutput output)
    {
        var classes = ButtonStyles[ButtonStyle];
        var classAttr = output.Attributes.Where(
            a => a.Name.Equals("class", StringComparison.InvariantCultureIgnoreCase)
        ).FirstOrDefault();

        if (classAttr != null)
        {
            output.Attributes.Remove(classAttr);
            classes += " " + classAttr.Value.ToString();
        }

        classAttr = new TagHelperAttribute("class", classes);
        output.Attributes.Add(classAttr);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的最终结果会变成这样:

```
<button button-style="Primary">Continue</button> 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
<button class="whatever-classes i-might-specifically want-for-all primary-button-styles">Continue</button> 
```

Enter fullscreen mode Exit fullscreen mode

这只是标签助手的一个例子，你可以用标签助手做很多其他的事情。我可能会在以后的文章中提到其中的一些。

## 结论

标签助手不仅仅是一种更简洁的方式，而且是一个强大的工具，可以为我们的 Razor 模板添加更多的功能。

## 进一步阅读

*   [ASP.NET 核心中的标签助手](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/intro?view=aspnetcore-2.2)