# 不使用 Angular、React 或 Vue 的快速 HTML 模板渲染

> 原文：<https://dev.to/jkimquickdev/quick-html-template-rendering-without-using-angular-react-or-vue-34g0>

前几天我在做一个网站，我有一个简单的 HTML 页面，上面有一堆数据要显示在一个列表中。该页面是静态的，我直接从客户端收到一个 excel 文件。

## 只需要渲染列表

现在，在现实中，客户需要我把列表放在网页上的一个表格中。这个列表永远不会改变，所以为什么要麻烦。

但是作为一个开发者，事情并不容易。我的意思是，你不能只是复制和粘贴 excel 文件中的每一行来创建 HTML 表格行，它们有 110 项。**我们是开发者，我们很懒，我们当然不能做艰苦的工作，对吧！:)**

所以，我认为要有创意。首先，我从 excel 中抓取数据并转换成 JSON。

## 大问题:用什么，React，Angular，Vue 还是普通 JavaScript

现在，我如何使用这个 JSON 来渲染实际！哦，我是 React 开发者，这不是很难。让我们开始构建组件。哦，那太麻烦了。我需要整个基础设施来创建和支持像 Web Pack，Babel 等组件。

我需要一些简单的方法来呈现 JSON 数据。我应该用 core JavaScirpt 写代码吗？嗯，我可以，但那太费事了，我当然不想做。

我想和我的同事讨论，所有人都指出了我不想使用的反应，棱角分明或 Vue。

所以，最后，我开始自己研究。我偶然发现了这个神奇的库，名字叫做: [mustache.js](https://github.com/janl/mustache.js/) 。

## 最后，我选定了 **Mustache.js**

现在，我如何使用这个库，我想创建一个样本 HTML 页面。

我从顶部的挂钩库开始:

### 来自 CDN 库:

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/mustache.js/3.0.1/mustache.min.js"></script> 
```

### HTML:

```
 <div id='sample'>
        <h4>Welcome to {{state}}!</h4>
        <p>From, {{country}}.</p>
        <p>{{content}}</p>
        <ul>
            {{#cities}}
            <li>{{city}} </li>{{/cities}}

        </ul>
    </div> 
```

### JavaScript

```
var template = document.getElementById('sample').innerHTML;
        var newHtml = Mustache.render(template, {
            state: 'Texas',
            country: 'United States',
            cities: [{
                id: 1,
                city: 'Houston'
            }, {
                id: 2,
                city: 'Dallas'
            }, {
                id: 3,
                city: 'Austin'
            }, {
                id: 4,
                city: 'San Antonio'
            }]

        });
        document.getElementById('sample').innerHTML = newHtml; 
```

### 注意 mustache.js 的简洁

我所做的只是准备好我的 HTML 模板，在我的 JavaScript 中引用模板，用 Mustache 的对象钩住 JSON，就这样！

### 瞧！我看到了，我要找的东西！

[![Mustache](img/25fb380d3f4ff57abd9a8d8d63e225fa.png "Mustache")](https://res.cloudinary.com/practicaldev/image/fetch/s--nd6623RU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/VqF4vH5/image.png)

### 结论

有时候，你只是想要一些简单的东西来解决你的问题。你没有心情用所有的东西创造一个杰作。我的问题也类似。我只需要一种简单的方式来呈现我的内容，我做到了！

### 最后，我的一个小故事！

大家好，我是 Kim，我是一名热情的 JavaScript 开发人员。

最近，我不得不处理大量的 JSON 数据。使用普通查看器检查这种复杂的 JSON 数据是一场噩梦，我的同事也有同样的感觉。

所以，有一天我们决定写一个工具，让检查和编辑 JSON 变得容易。

我们喜欢将 JSON 可视化为表格格式，这当然对您来说非常容易。所以第一步是把 JSON 转换成 Table，我们做到了。然后，我们将每个表格单元连接到实际的 JSON。

最后，我们认为每个开发人员都应该能够使用这个神奇的工具。我们将其命名为 **JsonGrid** 。游览 JsonGrid.com。

让我知道你对它的想法！

### 喜欢和追随

如果有人喜欢你的帖子，真的会鼓励你多写，所以如果你喜欢它，一定要喜欢它！

跟我来，继续获得像这样令人兴奋的新东西！