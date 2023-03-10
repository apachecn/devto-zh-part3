# HarpJS 入门

> 原文：<https://dev.to/megazear7/getting-started-with-harpjs-398m>

在这篇文章中，我们将看看静态站点生成器的基础知识。我希望这将为快速掌握这个简单而强大的工具提供一个快速的基础。此外，我计划发布更多深入 Harp 的博客文章，同时寻找将 Harp 与其他工具和系统集成的方法。

### 假设

1.  bash 命令行
2.  npm
3.  饭桶

### 入门

```
npm install -g harp
mkdir pizza-example
echo "Hello world" > pizza-example/index.ejs
harp server pizza-example 
```

现在打开  ，看看你的 hello world HarpJS 示例的运行情况！Harp 正在将 ejs 模板转换成 html 文件。因为我们没有在 url 中指定特定的 html 文件，所以它将在同一目录中查找索引文件。因为我们没有在 url 中提供路径，所以它会在项目的根目录中查找。

```
git init
git add .
git commit -m "Created a hello world Harpjs example." 
```

### 竖琴布局

我们需要做的第一件事是为我们的 hello world 示例做一个适当的布局。为此，在`index.ejs`旁边创建一个`_layout.ejs`文件。在此文件中提供基本的 html 标记:

```
<!DOCTYPE html>
<html>
  <head>
  </head>

  <body>
  </body>
</html> 
```

现在再次打开  。注意到我们的 hello world 不见了吗？这是因为如果一个 _layout 文件存在于目录或父目录中(一直到项目的根目录),那么它将被用来代替其他存在的文件。那么，如何将索引文件的内容提供给布局呢？我们通过使用产量函数来实现:

```
<!DOCTYPE html>
<html>
  <head>
  </head>

  <body>
    <%- yield %>
  </body>
</html> 
```

现在打开  。这次你将再次看到你的 hello world。

```
git add .
git commit -m "Added a layout to the root of the project." 
```

### 竖琴路由

现在，如果我们想在我们的网站上的其他网页呢？您所要做的就是用。ejs 扩展。例如，创建一个名为`menu.ejs`的文件，包含以下内容:

```
<p>Pizza</p>
<p>Pop</p>
<p>Salad</p> 
```

现在打开  。Harp 将在项目目录的根目录中查找名为“menu”的文件，并将自动将`menu.ejs`转换为 html 文件。文件内容将被插入到指定产量的布局中。

如果我们希望每个菜单项都有一个页面呢？让我们以比萨饼为例。继续在项目根目录下创建一个名为“menu”的目录，并在其中创建一个名为`pizza.ejs`的文件，其内容如下:

```
<p>This is the pizza page</p> 
```

现在打开，您将看到这个 html 再次插入到项目根目录中提供的布局中。然而，如果我们想为菜单项页面提供一个定制的布局呢？为此，在菜单文件夹中创建一个包含以下内容的`_layout.ejs`文件:

```
<!DOCTYPE html>
<html>
  <head>
  </head>

  <body>
    <p>Custom markup that will go on every menu page, but on no other pages</p>
    <%- yield %>
  </body>
</html> 
```

如果您再次打开 pizza 页面，您会看到现在可以为菜单页面提供自定义布局了。

```
git add .
git commit -m "Added sub pages in the menu directory with a custom layout." 
```

### 竖琴的分音

现在，网站的某个部分可能会在多个布局或多个页面中重复使用。我们可以利用偏导数来做到这一点。继续在项目根目录下创建一个名为`_header.ejs`的文件，并提供以下内容:

```
<h1>Welcome to the Pizza example</h1> 
```

现在更新根布局以添加分部函数，指定我们刚刚创建的分部:

```
<!DOCTYPE html>
<html>
  <head>
  </head>

  <body>
    <%- partial("_header") %>
    <%- yield %>
  </body>
</html> 
```

现在需要注意的是，文件名开头的下划线是不必要的，但是它告诉 Harp 不要将文件渲染为实际的网页。整个 Harp 也是如此。以下划线开头的文件不会作为站点内容的一部分呈现，而是用于其他目的，如布局、部分和数据，我们将在下一节中看到。随意重用这个头文件到其他文件，比如`menu/_layout.ejs`文件。

还要注意，我们不提供文件扩展名。这是因为 Harp 可以处理其他模板语言，并且只关心没有扩展名的文件名。在这篇博文中，我们只讨论 ejs 模板语言。

```
git add .
git commit -m "Added a header partial for reuse in multiple locations." 
```

### 竖琴元数据

现在我们所有的数据都嵌入到 html 中了。如果我们想在其他地方定义这些数据以便重用呢？为此，在菜单目录中创建一个名为`_data.json`的文件，其内容如下:

```
{  "pizza":  {  "title":  "Pizza",  "description":  "Very tasty and cheesy"  },  "pop":  {  "title":  "Soda Pop",  "description":  "A sugary beverage"  }  } 
```

此外，更新 pizza.ejs 文件以包含以下内容:

```
<h2><%- public.menu._data.pizza.title %></h2>
<p><%- public.menu._data.pizza.description %></p> 
```

现在打开，您将看到 json 数据被拉入 html。Harp 将在“公共”变量上的任何名为`_data.json`的文件中提供 json，该变量可以通过 ejs 模板中的 JavaScript 访问。这意味着我们拥有 JavaScript 的全部能力。

```
git add .
git commit -m "Added pizza as metadata instead of embedding it into the html." 
```

### 结论

Harp 是构建静态网站的有用工具，也可以作为更大的动态应用程序的一部分。在以后的博客文章中，我将介绍如何使用 Harp 的这些特性来创建网站导航、面包屑和其他网站中经常需要的上下文特性。我们可能能够涵盖的其他可能性是使用自定义 API 向静态网站添加动态元素，或者创建一个动态后端，该后端可以生成 JSON 数据，然后可以由 Harp 呈现。

查看我的博客,了解更多我对技术和其他各种话题的思考。