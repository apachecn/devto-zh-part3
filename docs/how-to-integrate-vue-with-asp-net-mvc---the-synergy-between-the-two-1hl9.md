# 如何将 Vue 与 ASP 整合？NET MVC——两者的协同作用

> 原文：<https://dev.to/proticm/how-to-integrate-vue-with-asp-net-mvc---the-synergy-between-the-two-1hl9>

在所有越来越流行前端开发的世界里，似乎另一面，后端，已经被放到了一边。这是因为有非常有用的库和框架，比如 Vue、React 或 Angular。

如果你问我，我不同意后端不如前端开发受欢迎或有用的假设。ASP。NET 有很多前几年发布的很棒的特性，而且它是开源的，我相信这对微软来说是一个巨大的进步。

在这篇文章中，我将展示如何将 Vue 与你的 ASP 相结合。NET MVC 应用程序，这意味着我们的服务器端视图模型将被序列化并表示为我们的 Vue 应用程序数据。

我们可以通过创建以下内容(在单个 MVC 视图上)来实现这一点:

1.  Vue 数据- ASP。网络属性
2.  解析器服务视图
3.  主视图模型(示例页面)
4.  主页- Index.cshtml(示例页)
5.  App 视图

注意，我不需要页面布局(_Layout.cshtml)和基类，因为我想让这篇文章尽可能简单。例如，有了合适的架构，您将有一个基本视图模型来保存数据字典，序列化将在布局页面上或助手方法中完成。

# Vue 数据- ASP。网络属性

属性是 ASP 中非常有用和强大的工具。网络世界。他们可以负责一个简单的任务，如对象属性验证(字段是必需的吗，允许的最大长度是多少...等等。)，或者它们可以包装更复杂的逻辑，例如，用户授权。

它们可以应用于许多不同的目标，但最常用的是:

1.  班级
2.  财产
3.  方法

我们的 VueData 属性将只针对 property，并且允许在同一个类中多次使用，这意味着我们可以将该属性应用于特定对象上的任意多个属性。

该属性将继承核心属性类，并且它将只有一个字符串属性(Name ),该属性将在我们的 Vue 应用程序中表示数据属性的名称。

```
[AttributeUsage(AttributeTargets.Property, AllowMultiple = true)]
public class VueData : Attribute
{
    public VueData(string name)
    {
        Name = name;
    }

    public string Name { get; }
} 
```

# 视图解析器服务

该服务将负责解析应用了 VueData 属性的属性。它将使用反射遍历对象的所有属性，检查属性是否应用了属性，如果应用了属性，它将填充一个字典，该字典稍后将被序列化到客户端。

```
// the interface

public interface IVueParser 
{
    Dictionary<string, object> ParseData<TModel>(TModel model);
}

// the implementation

public class VueParser : IVueParser
{
    public Dictionary<string, object> ParseData<TModel>(TModel model)
    {
        var props = model.GetType().GetProperties();
        var result = new Dictionary<string, object>();

        foreach (var prop in props)
        {
            var attr = prop.GetCustomAttributes(typeof(VueData), true)?.FirstOrDefault()
                as VueData;

            if (attr == null)
            {
                continue;
            }

            result.Add(attr.Name, prop.GetValue(model));
        }

        return result;
    }
} 
```

好了，现在我们已经实现了解析机制，是时候通过创建一个示例页面和视图模型来将它付诸实践了。

# 首页视图模型(示例页面)

在这篇文章的前面，我写道，我们希望将我们的服务器端视图模型定义为 Vue 应用程序的源。通过创建上述属性和服务，我们使这成为可能。

假设我们希望在视图上有一个包含菜单项的标题和一个包含欢迎消息的正文。由于 MVC 模式的应用，我们将创建一个具有两个属性的视图模型对象。毕竟，视图模型是视图的对象表示。第一个属性是字符串类型，第二个属性是字符串列表类型。

为了使我们的视图模型属性可以被序列化访问，我们需要做的就是对它应用属性。大概如下:

```
public class HomeViewModel
{
    [VueData("message")]
    public string Message { get; set; } = "Hello from Vue!";

    [VueData("menu")]
    public List<string> MenuItems { get; set; } = new List<string>()
    {
        "Menu 1",
        "Menu 2",
    };

    public string RazorMessage { get; set; } =  "Hello from Razor!";

    // in a real app, this would be placed in the base view model class
    public Dictionary<string, object> VueData { get; set; } = new Dictionary<string, object>();
} 
```

此外，我们将添加名为 RazorMessage (string 类型)的属性，只是为了说明我们可以在视图中组合这两种数据表示方式。该属性将不会被序列化，也不能被客户端访问。

视图模型已经准备好了，是时候进入下一步了，那就是创建视图。

# 首页- Index.cshtml

在我们看来，我们希望使用服务器端视图模型作为数据源和 Vue 组件来将数据呈现给用户。我们如何做到这一点？

首先，我们需要创建实际的 MVC 控制器和 Razor 视图。这是首先导航到页面所需要的(我们没有使用 Vue 路由器)。

### 控制器

```
public class HomeController : Controller
{
    public ActionResult Index()
    {
        // create our view model and parser
        var viewModel = new HomeViewModel();
        var parser = new VueParser(); // in the real app you would use DI

        // in a real app, this would be placed somewhere in the base controller
        viewModel.VueData = parser.ParseData(viewModel);

        return View(viewModel);
    }
} 
```

### 查看

```
@model ..MyNamespace/HomeViewModel

@{
    // here we do the serialization of our dictionary into JSON using Newtonsoft.Json package
    // this object will be used in our Vue application

    // over the years of web development, I've found out that the following 
    // serialization solves the problems I've encountered so far

    // in a real app this would be created as a helper method somewhere
    // if we want to exclude the c# code from our view
    var serializationSettings = new JsonSerializerSettings()
    {
        ContractResolver = new CamelCasePropertyNamesContractResolver(),
        StringEscapeHandling = StringEscapeHandling.EscapeHtml
    };   

    var data = 
        Html.Raw(
             HttpUtility.JavaScriptStringEncode(
                JsonConvert.SerializeObject(
                    Model.VueData, Formatting.None, serializationSettings
                ), 
             false)
        );
}

<!DOCTYPE html>
<html>
<head>
    Vue and .NET synergy
</head>
<body>
    // Here we can combine the Razor with Vue without problems

    <main id="vue-app">
        <header-component v-bind:menu-items="menu"></header-component>
        <div>@Model.RazorMessage</div>
        <div>{{message}}</div>
    </main>

    <script src="https://cdn.jsdelivr.net/npm/vue"></script>
    <script src="...my-path/header-component.js"></script>
    // place for the script from the next section here
</body>
</html> 
```

下面的脚本应该放在 body 标签的结束之前:

```
new Vue({
    el: '#vue-app',
    data: function() {
        // parse the serialized data
        return JSON.parse('@data');
    }
}); 
```

header-component.js 源代码:

```
// header-component.js
// in a real app you might use TypeScript or ECMAScript
// in a real app this would be a single file component probably

Vue.component('header-component', {
    props: ['menuItems'], // pass in the menu from the app
    template: '<header><ul><li v-for="item in menuItems">{{ item  }}</li</ul></header>'
}); 
```

如果运行该页面，输出应该如下所示:

*   菜单 1
*   菜单 2

剃刀向你问好！来自 Vue 的你好！

# 结论

请注意，可以使用相同的方法来创建与 Vue 属性的连接，只是在这种情况下，解析略有不同，因为 Vue 属性可以用多种方式定义。

这里的解决方案是创建一个 VuePropertyDescriptor 类，并将其用作每个 Vue 属性的值。这个类将反映 Vue prop 接口。它将包含类型、默认、必需等属性...等等。但这是另一篇文章的主题。

有时使用完整的前端应用程序不是一个选项。在这种情况下，像这篇文章中这样的实现可以派上用场。有了合适的架构和关注点的分离，这将是非常强大的，并且可以提高开发的速度。

更多最新文章，请务必查看 devinduct.com

感谢您的阅读，下一篇文章再见！