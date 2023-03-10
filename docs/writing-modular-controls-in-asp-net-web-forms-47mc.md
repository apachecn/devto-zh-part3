# 在 ASP.NET Web 窗体中编写模块化控件

> 原文：<https://dev.to/crenshaw_dev/writing-modular-controls-in-asp-net-web-forms-47mc>

大多数网站都有通用的组件，比如页眉和页脚。一些组件在整个站点上仅显示出微小的变化。

您可以复制和粘贴标记的公共部分。但是，当需要对组件的每个副本进行更改时，这会导致令人头疼的问题。

ASP.NET 通过嵌套组件提供了一种更加模块化的方法。

嵌套的一个常见例子是内置的 Repeater 控件。

```
<asp:Repeater ID="ExampleRepeater" runat="server">
   <HeaderTemplate></HeaderTemplate>
   <ItemTemplate></ItemTemplate>
   <FooterTemplate></FooterTemplate>
</asp:Repeater> 
```

Enter fullscreen mode Exit fullscreen mode

`Repeater`具有应用于`Template`标签中内容的通用行为。

请看一下 Visual Studio 2017 附带的样板 ASP.NET Web 表单网站的标记:

```
<div class="row">
    <div class="col-md-4">
        <h2>Getting started</h2>
        <p>
            ASP.NET Web Forms lets you build dynamic websites using a familiar drag-and-drop, event-driven model.
        A design surface and hundreds of controls and components let you rapidly build sophisticated, powerful UI-driven sites with data access.
        </p>
        <p>
            <a class="btn btn-default" href="https://go.microsoft.com/fwlink/?LinkId=301948">Learn more &raquo;</a>
        </p>
    </div>
    <div class="col-md-4">
        <h2>Get more libraries</h2>
        <p>
            NuGet is a free Visual Studio extension that makes it easy to add, remove, and update libraries and tools in Visual Studio projects.
        </p>
        <p>
            <a class="btn btn-default" href="https://go.microsoft.com/fwlink/?LinkId=301949">Learn more &raquo;</a>
        </p>
    </div>
    <div class="col-md-4">
        <h2>Web Hosting</h2>
        <p>
            You can easily find a web hosting company that offers the right mix of features and price for your applications.
        </p>
        <p>
            <a class="btn btn-default" href="https://go.microsoft.com/fwlink/?LinkId=301950">Learn more &raquo;</a>
        </p>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这段代码在主页上显示了三列:

[![Screenshot of Visual Studio 2017 boilerplate ASP.NET Web Forms application homepage](img/07de2293b72c2ca837fa2dd6fccb7a64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FpsrNtKk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5yfiwbp6wq7r40yughkk.png)

让我们将标记的公共部分合并到一个模块化的用户控件中。

# 设置项目

如果您知道如何在 Visual Studio 2017 中设置 ASP.NET Web 窗体项目，或者正在不同的 IDE 中进行设置，请随意跳到下一部分。

首先，创建一个新项目。我正在使用 ASP.NET Web 应用程序项目的 C#风格。

`File > New > Project...`

[![Screenshot of the new project dialog](img/e910b7e16463beeda745fd683e03033d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CdglGrZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ojflffxswr7cqvuug890.png)

选择 Web 应用程序的 Web 表单类型。

[![Web application type dialog](img/7fe6fa918e0caaeed1c1a03ab0a9ddb7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jWr393jB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d9imdouhiza9wi2aql13.png)

(我知道 Web Forms 已经过时了，但许多网站仍在使用它。据我所知，这仍然是一种完全可以接受的建站方式。)

[![Imperial guard from Star wars saying "It's an old code, sir, but it checks out."](img/6de41ba15e1effb267fe6c9a0c40ccb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nnS1z0Sz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i53ps2f847vn6mxac3fu.jpg)

如果你在浏览器中启动网站，你会看到如上所示的默认主页。

我们将创建一个新的用户控件来保存公共的“列”代码。

# 设置模块化控制

我正在创建一个新的“控制”文件夹，只是为了保持有序。

`Solution Explorer > Right-click ModularControls project > Add > New Folder`

[![Screenshot showing how to create a new folder](img/309275514e0c51d3c8e8f3b5da35919a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qzcGYCNn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nnptsw2vc669mw9r1may.png)

现在创建控件。我称之为“主页卡片”，尽管你可能只会为在多个页面上使用的东西创建一个模块化的控件。

`Solution Explorer > Right-Click Controls folder > Add > Web Forms User Control`

[![Screenshot showing how to create a new user control](img/f5e7b4b6eb1e4489d7387d5a4a22fe1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GZVU-VG9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/609gig7h19w1fr166up3.png)

将任何列的代码复制并粘贴到新控件中。然后用适当命名的`<asp:PlaceHolder>`控件替换标题和段落标签的内容。

```
<div class="col-md-4">
    <h2>
        <asp:PlaceHolder ID="TitlePlaceHolder" runat="server"></asp:PlaceHolder>
    </h2>
    <p>
        <asp:PlaceHolder ID="ContentPlaceHolder" runat="server"></asp:PlaceHolder>
    </p>
    <p>
        <asp:PlaceHolder ID="FooterPlaceHolder" runat="server"></asp:PlaceHolder>
    </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后设置控件的代码隐藏，如下所示:

```
using System;
using System.Web.UI;

namespace ModularControls.Controls
{
    [ParseChildren(typeof(Control), DefaultProperty = "Content", ChildrenAsProperties = true)]
    public partial class HomepageCard : UserControl
    {
        public HomepageCard()
        {
            Title = new ControlCollection(this);
            Content = new ControlCollection(this);
            Footer = new ControlCollection(this);
        }

        [PersistenceMode(PersistenceMode.InnerProperty)]
        public ControlCollection Title { get; private set; }

        [PersistenceMode(PersistenceMode.InnerDefaultProperty)]
        public ControlCollection Content { get; private set; }

        [PersistenceMode(PersistenceMode.InnerProperty)]
        public ControlCollection Footer { get; private set; }

        protected void Page_Load(object sender, EventArgs e)
        {
            foreach (Control control in Title)
            {
                TitlePlaceHolder.Controls.Add(control);
            }

            foreach (Control control in Content)
            {
                ContentPlaceHolder.Controls.Add(control);
            }

            foreach (Control control in Footer)
            {
                FooterPlaceHolder.Controls.Add(control);
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

事情很多，我就把关键部分分解一下。

```
[ParseChildren(typeof(Control), DefaultProperty = "Content", ChildrenAsProperties = true)] 
```

Enter fullscreen mode Exit fullscreen mode

属性的三个参数指示 ASP.NET

1.  将嵌套内容解析为`Control`对象(`typeof(Control)`)
2.  如果没有指定节(我们将在下面看看如何做)，将控件解析到`Content`属性中
3.  将子元素解析为属性，如下所述

```
public HomepageCard()
{
    Title = new ControlCollection(this);
    Content = new ControlCollection(this);
    Footer = new ControlCollection(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

默认构造函数更新控件集合，以便它们准备好接受由 ASP.NET 解析的控件。`this`参数指定这些控件集合的“所有者”控件。

```
[PersistenceMode(PersistenceMode.InnerProperty)]
public ControlCollection Title { get; private set; }

[PersistenceMode(PersistenceMode.InnerDefaultProperty)]
public ControlCollection Content { get; private set; }

[PersistenceMode(PersistenceMode.InnerProperty)]
public ControlCollection Footer { get; private set; } 
```

Enter fullscreen mode Exit fullscreen mode

`PersistentMode`属性告诉 ASP.NET 如何将内容保存到呈现的页面中。注意，`Content`属性被标记为`InnerDefaultProperty`。

每个`ControlCollection`都需要将 setter 显式设置为`private`。如果您跳过这一步，您将得到难以调试的运行时错误。

```
protected void Page_Load(object sender, EventArgs e)
{
    foreach (Control control in Title)
    {
        TitlePlaceHolder.Controls.Add(control);
    }

    foreach (Control control in Content)
    {
        ContentPlaceHolder.Controls.Add(control);
    }

    foreach (Control control in Footer)
    {
        FooterPlaceHolder.Controls.Add(control);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Load`事件处理程序遍历经过解析的控件，并将它们放到适当的占位符中。

# 使用模块化控制

首先，在 Default.aspx.
中注册控件

```
<%@ Register Src="~/Controls/HomepageCard.ascx" TagPrefix="ex" TagName="HomepageCard" %> 
```

Enter fullscreen mode Exit fullscreen mode

然后用下面的代码替换第一列。

```
<ex:HomepageCard runat="server">
    
        <asp:PlaceHolder runat="server">
            Getting started
        </asp:PlaceHolder>
    
    <Content>
        <asp:PlaceHolder runat="server">
            ASP.NET Web Forms lets you build dynamic websites using a familiar drag-and-drop, event-driven model.
            A design surface and hundreds of controls and components let you rapidly build sophisticated, powerful 
            UI-driven sites with data access.
        </asp:PlaceHolder>
    </Content>
    <Footer>
        <asp:PlaceHolder runat="server">
            <a class="btn btn-default" href="https://go.microsoft.com/fwlink/?LinkId=301948">Learn more &raquo;</a>
        </asp:PlaceHolder>
    </Footer>
</ex:HomepageCard> 
```

Enter fullscreen mode Exit fullscreen mode

标题、内容和页脚标签告诉 ASP.NET 在哪里放置内容。

`<asp:PlaceHolder>`控件允许 ASP.NET 将标记解析为控件。

有点啰嗦。但是一旦写了冗长的部分，就再也不用修改了。可以在 HomepageCard.ascx 中更改控件的公共部分。

如果您重新构建并重新加载主页，它看起来应该与您编写模块化控件之前完全相同。就是这个想法！这种模式不会改变呈现的内容——只是使它更加模块化和可维护。

# 改进？

在研究这项技术时，我发现*很少有文档。由于 ASP.NET 网络表单的第一个版本是在 2002 年发布的，这种技术的大部分指南可能都是印刷形式。*

如果你知道如何改进这种模式，请告诉我！我很想去掉`PlaceHolder`标签来加强标记。我真的很想从代码隐藏中去掉所有属性和占位符，以支持一些更具表现力的语法。但我不确定这两种可能性都有。

希望这是一个有帮助的模式！如果有，请评论，让我知道你是怎么用的！