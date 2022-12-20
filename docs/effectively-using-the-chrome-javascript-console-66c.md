# 有效地使用 Chrome Javascript 控制台

> 原文：<https://dev.to/barakplasma/effectively-using-the-chrome-javascript-console-66c>

Javascript 控制台是快速调试和数据操作的强大工具。

通过利用内置的浏览器功能，我们可以轻松地获取、探索、操作和使用来自现有来源的数据。

涉及的组件有:

1.  [“存储为全局变量”](https://mariusschulz.com/blog/chrome-developer-tools-store-as-global-variable)上下文菜单选项
2.  [动态导入](https://github.com/tc39/proposal-dynamic-import/#import)
3.  [复制()](https://developers.google.com/web/tools/chrome-devtools/console/utilities#copyobject)

## 存储为全局变量

Chrome 和 Firefox 都提供了“存储为全局变量”上下文菜单选项，用于将 javascript 对象复制到控制台。只需在调试器、网络面板或控制台中右键单击一个对象，然后选择“存储为全局变量”。该变量将被存储为 temp${N},其中 N 是它在当前控制台中被使用的次数。我经常用它来复制网络请求预览，或者调试器对象。这个特性是无价的。

## 动态导入

使用[动态导入](https://github.com/tc39/proposal-dynamic-import/#import)，我们可以导入 npm 模块，以便在调试或操作数据时使用。

例如:

```
import('https://unpkg.com/lodash@latest/lodash.min.js')

_.add(4, 6)
// 10 
```

这使我们可以选择使用 lodash、ramda 或您选择的库，按照您认为合适的方式对数据进行切片、切割和格式化。

可以从 unpkg，jsDelivr，或者自己选择的 CDN 导入。以下是一些例子:

```
// import jQuery, or another javascript project, from it's github repository.
import('https://cdn.jsdelivr.net/gh/jquery/jquery@latest/dist/jquery.min.js') 
```

```
import("https://cdn.jsdelivr.net/npm/ramda@latest/dist/ramda.min.js")
// or
import("https://cdnjs.cloudflare.com/ajax/libs/ramda/0.25.0/ramda.min.js") 
```

### 告诫

许多库很难通过这种方式导入，因为它们的格式与动态导入不兼容。寻找。mjs 文件或 UMD 模块(如果有)。大多数 CommonJS 库在这里无法工作。

## 复制()

copy()函数允许我们将给定的 Javascript 控制台变量复制到系统剪贴板。

```
const temp1 = [1,2,3]; // could be any variable
copy(temp1); // clipboard now contains "[1,2,3]" 
```

## 示例用法

### 获取给定帖子的平均文本长度

假设您正在开发一个 web 组件来显示一篇博客文章，您听到其中一些文章溢出了容器。使用下面的方法，您可以计算各种描述性统计数据，以确定您的组件应该支持什么。

```
const getSimpleStatisticsFromUnpkg = async () => await import(
    "https://unpkg.com/simple-statistics@7.0.1/dist/simple-statistics.mjs"
);
// hit enter
const simpleStatistics = await getSimpleStatisticsFromUnpkg();
// the module is now available under the 'simpleStatistics' namespace

const getPosts = async () => await fetch(
    "https://jsonplaceholder.typicode.com/posts"
)
    .then(res => res.json());

const posts = await getPosts();
// hit enter
// alternatively, use a stored global variable like temp1 in place of posts;

const average = simpleStatistics.average(posts.map(post => post.body.length))
// average = 160.64

copy(average); // paste into an email to your designer ;-) 
```

## 结论

成为 Javascript 控制台的大师将改善您的工作流程，并为快速开发开辟新的可能性。如果你有任何问题或意见，请在下面的评论中联系我。

欲了解更多信息，请查看

*   [Chrome 动态进口发货公告](https://developers.google.com/web/updates/2017/11/dynamic-import)