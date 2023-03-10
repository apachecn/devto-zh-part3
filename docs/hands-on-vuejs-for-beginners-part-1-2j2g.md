# 初学者实用 Vue.js(第 1 部分)

> 原文：<https://dev.to/marinamosti/hands-on-vuejs-for-beginners-part-1-2j2g>

对于任何开发人员来说，学习一个新的框架都是一个令人望而生畏的过程，尤其是对于一个仍在学习基础语言(在这里是 JavaScript)的人来说。这就是我决定创作这个系列的原因，在这个系列中，我将努力使学习 Vue.js 变得尽可能简单易懂🙂

我不喜欢做冗长的介绍，所以我假设如果你还在阅读:

1.  你有一些基本的 HTML/CSS/JS 知识。你不需要成为一个有经验的前端开发人员来承担 Vue 作为一个开发框架，但至少你需要能够编写自己的 HTML 标记，理解 CSS 如何工作的基本原理，是的，还有如何编写 javascript。最后，这就是这一切。

2.  就是这样。不，真的。

# Vue 为库

有几种方法可以将 **Vue** 合并到您的 web 项目中。让我们从最简单的开始(你可能不会经常用到)。

大多数教程/文章将假设您对如何建立一个开发环境有所了解，在这个环境中您将使用像`npm`、`webpack`这样的东西来建立您的项目——虽然这是理想的，因为您可以从盒子中得到什么——我们可以从一个简单得多的初学者友好的方法开始。可靠的老`<script>`标记。

继续运行您最喜欢的代码编辑器，并创建一个名为`index.html`的新文件。(如果你还没有的话， [VS 代码](https://code.visualstudio.com/)是一个热门的免费选择。

```
<html>
  <head>
    Vue 101
  </head>

  <body>
    <h1>Hello!</h1>
    <div id="app"></div>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

没有什么花哨的，我们只是为一个简单的网站设置框架。现在让我们把`Vue`库放在那里。在关闭`</body>`之前粘贴这个脚本标签。

```
[...]
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

现在`Vue`已经被加载到我们的页面中，我们可以开始使用它了。
让我们继续创建一个新的 Vue 实例，通过`new`将它放在`<script>`标签中。我们将通过将`#app`传递给选项对象的`el`属性来给它一个*选择器*，这样`Vue`将知道我们的应用程序应该呈现在哪里。(还记得那个 ID 为 **app** 的空`<div>`吗？)

将这段代码放在最后一个脚本标记之后。

```
<script>
    const app = new Vue({
        el: '#app', // 1
        data: { // 2
            myLocalProperty: 'Im a local property value' // 3
        }
    });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么？
我们创建了我们的`new Vue`实例，并传递给它一个配置对象。把`{}`当参数看？

1.  正如我之前提到的，这里我们告诉 Vue 我们希望我们的应用程序显示在 HTML 的哪个位置。在本例中，是 id 为`app`的 div。
2.  `data`对象。每个 Vue **实例**都有一个本地存储，就像一个变量和属性的盒子，它会为我们保存，我们可以在编写应用程序时使用。数据保存一个 JavaScript `object`，所以我们用`{ }`语法给它赋值。在里面，我们放置一个属性。
3.  `myLocalProperty`。这个属性是在我们的实例的`data`对象中定义的，它的名称是 myLocalProperty，右边的值是 value——在本例中是一个字符串。

# 在我们的应用程序上显示属性

现在，如果你在浏览器中打开`index.html`,不会有太多变化。

[![](img/7326862b0d982a427d65a452c07e3202.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cvNnsr03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aamvag00ek2aux3e2vwl.png)

让我们添加一些代码来显示 HTML 中的属性。您的文件应该如下所示:

```
<html>
    <head>
        Vue 101
    </head>

    <body>
        <h1>Hello!</h1>
        <div id="app">
          <p>My local property: {{ myLocalProperty }}</p>
        </div>

        <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

        <script>
          const app = new Vue({
            el: '#app',
            data: {
              myLocalProperty: 'Im a local property value'
            }
          });
        </script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

请密切注意这一行:

```
<p>My local property: {{ myLocalProperty }}</p> 
```

Enter fullscreen mode Exit fullscreen mode

这里发生的事情被称为*变量插值*，这是一个有趣的术语，“我将在我的`{{ }}`现在所在的占位符中显示我的`myLocalProperty`变量的内容。

重新加载页面，现在您将看到字符串更新以反映我们的变量。

继续尝试将`myLocalProperty`中的字符串更改为其他文本，然后重新加载页面，您应该会看到文本相应地更新。

# 反应性

最后，这节课，我们来说说`reactivity`。你可能听说过 **Vue** 是一个**反应式**框架。但是这到底是什么意思呢？在 chrome 开发者工具中打开你的控制台，用你的 index.html 加载类型:

```
app.myLocalProperty = 'Vue is reactive'; 
```

Enter fullscreen mode Exit fullscreen mode

你会看到页面**对这个变量的变化做出反应**！

* * *

敬请关注第二部分！