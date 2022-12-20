# 铸造🍃:路由

> 原文：<https://dev.to/gdotdesign/mint-routing-2h69>

*这是展示 Mint 特性的系列文章中的第七篇，你可以在这里找到之前的文章:*

*   *[薄荷🍃:入门](https://dev.to/gdotdesign/getting-started-with-mint-3k2o)T3】*
*   *[薄荷🍃:组件](https://dev.to/gdotdesign/components-in-mint-4a4n)T3】*
*   *[薄荷🍃:组件的事件和状态](https://dev.to/gdotdesign/mint-events-and-state-of-components-3j3a)*
*   *[薄荷🍃:处理 HTTP 请求](https://dev.to/gdotdesign/mint-handling-http-requests-2ep3)T3】*
*   *[薄荷🍃:造型元素](https://dev.to/gdotdesign/mint-styling-elements-295o)*
*   *[薄荷🍃:创建包](https://dev.to/gdotdesign/mint-creating-a-packages-5e9b/)*

在这篇文章中，我将向你展示如何为你的应用程序设置路线。

* * *

在 JavaScript 领域，你可以使用第三方路由库，如 [react-router](https://github.com/ReactTraining/react-router) 、 [vue-router](https://github.com/vuejs/vue-router) 或类似的库(取决于框架)来处理 URL 的变化。

在 Mint 中，路由内置于语言本身🎉所以少了一个需要担心的依赖。

### 定义路线

您可以使用`routes`块定义路线:

```
routes {
  /path/to/match {
    /* Do something here... */
  }

  /an/other/path/to/match {
    /* Do something here... */
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上，你只需要定义你想要匹配的路径，在**中，当路径匹配时，应该发生什么。**

有两件事要记住:

*   路线从上到下匹配
*   每个导航事件只有一条路线匹配

### 匹配参数

您可以使用标准冒号参数(`:param`)作为路径变量:

```
routes {
  /posts/:id (id : String) {
    /* Do something with the id here... */
  }

  /posts/:id/comments/:comment (id : String, comment : String) {
    /* Do something with the id and comment here... */
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> ℹ️变量的顺序并不重要，但是名字必须匹配。

甚至可以匹配哈希和查询参数:

```
routes {
  /posts?page=:page (page : String) {
    /* Do something with the page here... */
  }

  /#:hash (hash : String) {
    /* Do something with the hash here... */
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> ⚠️目前在路由参数中没有实现类型强制，所以一切都是一个`String`。

### 匹配所有(未定义)路径

使用`*`块匹配所有(未定义的)路径:

```
routes {
  * {
    /* Matches anything. Useful for displaying a 404 page. */
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> ℹ️:不管这个积木在哪里，它总是最后一个匹配。

### 导航到新的网址

在 Mint 中，语言负责导航，你所要做的就是渲染普通的锚标签`<a href="/path/to/navigate"></a>`，运行时会完成剩下的工作:

*   如果`href`属性匹配一个路径，它将导航到该路径并调用该块，而不需要重新加载页面，否则它将正常导航到该 URL
*   保留浏览器特定的行为:
    *   如果默认行为被 [`perventDefault`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault) 阻止，则不导航
    *   `ctrl`单击在新选项卡中打开 URL(如果适用)

> ℹ️:如果你对实际的实现感兴趣，你可以在这里找到它。

### 实际用法及举例

在一个实际的应用程序中，我通常做的是用一个`enum`来表示所有可能出现的页面，就像这样:

```
enum Page {
  NotFound
  Register
  Initial
  Login
  Home
} 
```

Enter fullscreen mode Exit fullscreen mode

我还有一个名为`Application`的商店，它的页面状态为:

```
store Application {
  state page : Page = Page::Initial

  fun setPage(page : Page) : Promise(Never, Void) {
    next { page = page }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在`Main`组件中，我使用该状态来呈现不同的页面:

```
component Main {
  connect Application exposing { page }

  fun render : Html {
    <div>
      <div>
        <a href="/register">"Register"</a>
        <a href="/login">"Login"</a>
        <a href="/asdf">"404"</a>
        <a href="/">"Home"</a>
      </div>

      <hr/>

      case (page) {
        Page::Register => <div>"Register"</div>
        Page::NotFound => <div>"404"</div>
        Page::Login => <div>"Login"</div>
        Page::Home => <div>"Home"</div>
        Page::Initial => <div></div>
      }
    </div>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

并在路线中相应设置页面:

```
routes {
  /register {
    Application.setPage(Page::Register)
  }

  /login {
    Application.setPage(Page::Login)
  }

  / {
    Application.setPage(Page::Home)
  }

  * {
    Application.setPage(Page::NotFound)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样了🎉在这里，您可以找到包含实际代码的存储库:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [薄荷-郎](https://github.com/mint-lang) / [薄荷-举例-路由](https://github.com/mint-lang/mint-example-routing)

<article class="markdown-body entry-content container-lg" itemprop="text">

# mint-示例-路由

这是一个展示 Mint 路由的资源库。

</article>

[View on GitHub](https://github.com/mint-lang/mint-example-routing)

今天就到这里，谢谢你的阅读🙏

* * *

如果你想了解更多关于 Mint 的知识，请查看[指南](https://guide.mint-lang.com)📖

在下一部分，我将展示如何使用环境变量😉在那里见👋