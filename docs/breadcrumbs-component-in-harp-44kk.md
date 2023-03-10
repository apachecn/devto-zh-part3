# Harp 中的面包屑组件

> 原文：<https://dev.to/megazear7/breadcrumbs-component-in-harp-44kk>

到目前为止，我已经写了两篇关于 Harp 静态站点生成器的博文。第一个是[Harp](https://www.alexlockhart.me/2018/12/getting-started-with-harpjs.html)入门，它提供了 Harp 所有主要特性的快速概述，会让你很快上手。然后我在 Github 页面上写了[托管 Harp，让你的静态网站向全世界展示。现在我已经完成了设置、开发和部署，我想开始讨论一些您可以用 Harp static site generator 做的更有趣的事情。](https://www.alexlockhart.me/2018/12/hosting-harp-on-github-pages.html)

在这篇博文中，我们将创建一个面包屑组件。我们可以从两个角度来看这个问题。首先，我们可以考虑面包屑组件将呈现的内容，其次，我们可以查看将生成标记的实现。内容将以 _data.json 和 ejs 文件的形式出现在目录层次结构中。该实现将以上下文部分的形式出现，它将基于层次结构中的当前位置呈现面包屑。

# 内容

### 示例页面

在 Harp 中，您可以拥有一个嵌套的 ejs 模板目录，然后这些模板被编译成 html 页面。在每个目录中，您都可以拥有与该目录相关联的元数据，其格式为名为 _data.json 的 json 文件。想象一下下面的目录和文件结构:

`<project-root>/story/history/origins.ejs`

在这个例子中，我们有一个项目根目录，子目录名为“story ”,子目录名为“history”。在这个最低级别的子目录中，我们有一个名为“origins.ejs”的文件。这是一个 html 模板，由 harp 呈现后，可从以下 url 获得:

`localhost:9000/story/history/origins.ejs`

### 元数据

每个目录都可以在 _data.json 文件中包含 json 格式的元数据。在每个目录中添加这样一个 json 文件，内容如下:

```
{  "title":  "provide a title here"  } 
```

您将希望用每个目录的特定内容来替换标题。在根 level _data.json 文件中，将该文件更新为如下所示:

```
{  "title":  "provide a title here",  "navigationTitle":  "Home"  } 
```

这将让我们都为整个网站提供一个标题，同时在面包屑中使用单词“Home”。最后，更新“history”目录中的 _data.json 文件，如下所示:

```
{  "title":  "provide a title here",  "origins":  {  "title":  "provide a title for the origins page"  }  } 
```

在这里您可以看到，我们将在 breadcrumbs 中使用的标题可以在相应目录的 _data.json 中找到，或者在与页面同名的 json 的子属性中的父目录中找到。通过这种方式，您不需要为每个页面创建 _data.json 文件。叶节点页面(如“origins.html ”)可以在包含目录的元数据中定义其标题。

# 实现

现在我们有了一些示例内容，继续用以下内容更新 origins.ejs 文件:

```
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <%- partial("../../breadcrumbs") %>
  </body>
</html> 
```

### 面包屑偏多

这将包括来自项目根的名为“_breadcrumbs.ejs”的分部。让我们继续创建这个文件。我将提供这个面包屑部分的完整内容，然后解释它是如何工作的:

```
<div class="breadcrumbs">
  <a href="/"><%- public._data.home %></a>
  <% current.path.forEach((pathSegment, index, array) => { %>
    <a href="/<%- array.slice(0, index+1).join("/") %>.html">
      <%
        var currentData = array
          .slice(0, index + 1)
          .reduce((oldVal, newVal) => oldVal && oldVal[newVal] ? oldVal[newVal] : {}, public)
          ._data;
        var title = currentData ? currentData.title : false;
        var parentData = array
          .slice(0, index)
          .reduce((oldVal, newVal) => oldVal && oldVal[newVal] ? oldVal[newVal] : {}, public)
          ._data;
        let parentDefinedTitle = parentData && parentData[pathSegment]
          ? parentData[pathSegment].title
          : "";
      %>
      <%- title ? title : parentDefinedTitle %>
    </a>
  <% }); %>
</div> 
```

现在让我们来看看这个面包屑实现的每个和平。

### 首页链接

这是最简单的部分。这只是在根目录的 _data.json 中查找标题属性。

```
<div class="breadcrumbs">
  <a href="/"><%- public._data.home %></a>
  ... 
</div> 
```

### 为路径中的每一段

这一部分也相当简单。这里我们基本上要为路径中的每一段渲染一个链接。因此，当用户访问“localhost:9000/story/history/origins . ejs”时，我们将呈现“story”、“history”和“origins”的链接。

```
<div class="breadcrumbs">
  <a href="/"><%- public._data.home %></a>
  <% current.path.forEach((pathSegment, index, array) => { %>
    <a href="/<%- array.slice(0, index+1).join("/") %>.html">
      <%- ... %>
    </a>
  <% }); %>
</div> 
```

### 寻找标题

这部分变得更加复杂。为了简单起见，我将 JavaScript 从 ejs 模板中分离出来，但是要看完整的文件是什么样子，请看这篇博文的“面包屑部分”部分。

```
// 1\. Get the json of the current links _data.json file.
let currentData = array
  .slice(0, index + 1)
  .reduce((oldVal, newVal) =>
    oldVal && oldVal[newVal] ? oldVal[newVal] : {}, public)
  ._data;

// 2\. Get the title from this _data.json file if it exists.
let title = currentData ? currentData.title : false;

// 3\. Get the json of the parent _data.json file.
let parentData = array
  .slice(0, index)
  .reduce((oldVal, newVal) =>
    oldVal && oldVal[newVal] ? oldVal[newVal] : {}, public)
  ._data;

// 4\. Get the title of the current page as defined in the parents _data.json file.
let parentDefinedTitle = parentData && parentData[pathSegment]
  ? parentData[pathSegment].title
  : ""; 
```

这有四个步骤:

1.  获取当前 links _data.json 文件的 json。
2.  从 this _data.json 文件中获取标题(如果存在的话)。
3.  获取 parent _data.json 文件的 json。
4.  获取 parents _data.json 文件中定义的当前页面的标题。

在步骤 1 和 3 中，我们将当前页面的数组切割到索引的长度。在这个 we 中，我们采用完整的[“story”、“history”、“origins”]数组，并对其操作三次，如下所示:

1.  `["story"]`
2.  `["story", "history"]`
3.  `["story", "history", "origins"]`

在每次循环中，我们都使用 slice 方法来获取正确的数组，然后使用 reduce 方法来遍历我们在这篇博文前面创建的元数据。该元数据存储在“public”变量中。尽管我们从未在 json 的单个块中看到过这种元数据，但在呈现时它看起来像这样:

```
{  "title":  "Your website title",  "navigationTitle":  "Home",  "story":  {  "_data":  {  "title":  "Story"  },  "history":  {  "_data":  {  "title":  "History",  "origins":  {  "title":  "Origins"  }  }  }  }  } 
```

“story”和“history”下面有一个“_data”节点，而“origins”下面没有，这是因为我们没有为“origins”提供 _data.json 文件，而是将此页面的标题放在父目录 _data.json 文件中(即“history”)。

因此，这个 JavaScript 搜索使用路径段数组来查找指定页面的标题。现在我们需要做的就是在链接中添加标题，如下所示。同样，要查看整个文件，请参考上面这篇博文的“面包屑部分”一节。

```
<div class="breadcrumbs">
  <a href="/"><%- public._data.home %></a>
  <% current.path.forEach((pathSegment, index, array) => { %>
    <a href="/<%- array.slice(0, index+1).join("/") %>.html">
      <%
        // The JavaScript from above goes here.
        ...
      %>
      <%- title ? title : parentDefinedTitle %>
    </a>
  <% }); %>
</div> 
```

现在我们需要做的就是添加一些 css 来将链接彼此分开。继续将以下 css 添加到页面中:

```
.breadcrumbs a:not(:last-child):after {
  margin: 0 0.5rem;
  content: ">"
} 
```

就是这样！继续打开下面的 url，您应该会看到下面的面包屑:

`localhost:9000/story/history/origins.ejs`

[![example breadcrumbs component](img/a422d99721b39637aa698e2bc291a274.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAx8oQSu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/42l95an56mz7605kzwiy.png)

这个 breadcrumbs 组件将在 Harp 中任何位置的任何页面上工作。它将根据上下文生成直至站点根目录的面包屑，并将为每个页面指定标题。它可以添加到一个布局，包括从另一个部分，或添加到您的 Harp 网站的任何页面！

感谢您的阅读，敬请关注更多关于 Harp 静态站点生成器的博文。

查看我的博客,了解更多我对技术和其他各种话题的思考。