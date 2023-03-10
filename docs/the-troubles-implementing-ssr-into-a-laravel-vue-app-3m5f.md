# 在 Laravel/Vue 应用中实现 SSR 的问题是

> 原文：<https://dev.to/michi/the-troubles-implementing-ssr-into-a-laravel-vue-app-3m5f>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/implementing-ssr-in-laravel-vue-app)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

TLDR:有可能！

这是为了那些想要将服务器端渲染集成到现有 Laravel Vue 应用程序中的人。如果你计划创建一个新的应用程序，考虑使用 [Nuxt.js](https://nuxtjs.org/) 作为服务器端渲染的 Vue 应用程序，Laravel 只作为一个 API。如果你想使用完整的 Node.js，也可以考虑使用 [Adonis.js](https://adonisjs.com/) 而不是 Laravel。

* * *

PHP 不懂 JavaScript。因此，为了实现 SSR，我们必须生成一个 Node.js 实例，在那里呈现我们的 Vue 应用程序，并将输出返回给客户端。

实际上，已经有一个作曲家依赖来完成任务:[https://github.com/spatie/laravel-server-side-rendering](https://github.com/spatie/laravel-server-side-rendering)。您可以按照那里的步骤来实现它。这篇文章将仅仅处理我遇到的问题。一路上我也会给一些提示。

我用的是 Laravel 5.5 和 Node 8.11。让我们先检查一些简单的东西。

* * *

### 刀刃视图

存储库中的文档有点不完整。我对`app.$mount('#app')`感到困惑，因为在自述文件的刀片文件中，没有与选择器`#app`匹配的元素。

实际上，根据[示例](https://github.com/spatie/laravel-server-side-rendering-examples)的完整叶片视图应该是这样的

第一步。叶片

```
<html>
    <head>
        <script defer src="{{ mix('app-client.js') }}">
    </head>
    <body>
        {!! ssr('js/app-server.js')->fallback('<div id="app"></div>')->render() !!}
        <script defer src="{{ mix('app-client.js') }}">
    </body> </html> 
```

Enter fullscreen mode Exit fullscreen mode

步骤 2 .视图(view)

根组件也获得分配的 id `app`。

```
<template
  <div id="app">
    <!-- ... --!> </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

因此，当 SSR 由于某种原因失败时，它会退回到`<div id="app"></div>`，客户端渲染会处理好一切。

否则，在应用程序已经呈现在服务器上之后，客户端挂载`app.$mount('#app')`仍然会因为步骤`2`而正常工作。

所以这是可行的，但是在多个地方拥有相同的 ID 有点令人困惑。一个更简单的解决方案是只在刀片视图中将`#app`放在一个包装类中。

```
<html>
    <head>
        <script defer src="{{ mix('app-client.js') }}">
    </head>
    <body>
        <div id="app">
            {!! ssr('js/app-server.js')->render() !!}
        </div>
    </body> </html> 
```

Enter fullscreen mode Exit fullscreen mode

是的，即使有了 SSR，我们仍然需要一个客户端挂载来让 Vue 添加事件监听器，处理所有的反应和生命周期挂钩。一个例子是只在客户端执行的`mounted`方法。SSR 将只执行初始渲染所需的内容。

### 我的节点路径在哪里。包封/包围（动词 envelop 的简写）

在许多情况下，这可能只是

```
NODE_PATH=node 
```

Enter fullscreen mode Exit fullscreen mode

你知道，就像你可以全局访问 Node 来访问`node some-file.js`或`node -v`一样。

### 它根本不执行 SSR

默认情况下，它仅在生产时激活。您可以通过首先发布配置文件
来改变这一点

```
php artisan vendor:publish --provider="Spatie\Ssr\SsrServiceProvider" --tag="config" 
```

Enter fullscreen mode Exit fullscreen mode

然后将`'enabled' => env('APP_ENV') === 'production'`改为`'enabled' => true`。

* * *

到目前为止，它至少应该尝试进行 SSR。这意味着你离完成它又近了一步。但是现在，当 Node 试图呈现 Vue 应用程序时，您可能会遇到如下问题。

### 异步 await 崩溃

我们正在讨论将它集成到现有的应用程序中。所以一定要检查你的 Laravel-mix 版本是不是太过时了。对我来说，它甚至不在 2.0 上。对`laravel-mix@2.1.14`的更新足以修复这些问题。你可能会考虑更新到更高的版本，但是一定要检查关于重大变化的发行说明。

### 所有属性在子组件中未定义

另一个错误是版本错误。从 2.5 更新到最新的`vue@2.6.10`修复了这个错误。事后看来，这个问题也可能是由于 Vue 和`vue-server-renderer`的版本不同造成的。

### 窗口未在`return window && document && document.all && !window.atob`中定义

现在它变得更有趣了。一旦在 Vue 组件中有了样式，您就会遇到这个错误。这样做的原因是因为`vue-loder`使用了头罩下的`style-loader`，它负责在运行时将样式动态添加到头部。但是有一个问题，**只在浏览器**中有效。由于 SSR 是在 Node 中呈现的，因此既没有`window`也没有`document`可用。这让我想到，Nuxt.js 是怎么做到的？毕竟他们也在使用 vue-loader。解决方案很简单:在节点渲染之前提取样式。这实际上是一个很好的做法，所以让我们在 laravel-mix 中设置它。

我们唯一要做的就是将下面的内容添加到`webpack-mix.js`中的`options`中。

```
mix.options({
    extractVueStyles: 'public/css/app.css',
}) 
```

Enter fullscreen mode Exit fullscreen mode

所有样式都被提取到一个文件`app.css`中。如果你有使用 Vue 的单独的页面，并且你想为每个页面有一个单独的 CSS 文件，使用下面的:

```
mix.options({
    extractVueStyles: 'public/css/[name].css',
}) 
```

Enter fullscreen mode Exit fullscreen mode

这将创建以下文件，例如

```
> /public/css/js/login.css
> /public/css/js/registration.css
> /public/css/js/search.css 
```

Enter fullscreen mode Exit fullscreen mode

* * *

除了提取 Vue 样式，你还必须删除 JavaScript 中的 CSS 导入文件。

```
import "some-library/some-style.css" 
```

Enter fullscreen mode Exit fullscreen mode

相反，将它们移动到某个全局样式表中。您可能已经有了一些合适的合并技术。同样，无论如何这样做也是一个好习惯；)

### webpackJsonp 未定义

如果发生这种情况，您可能会将节点模块提取到供应商文件中。这具有多种性能优势。

```
mix.extract(['vue']); 
```

Enter fullscreen mode Exit fullscreen mode

为什么会崩溃？如果你看一下`manifest.js`的输出，它会创建一个全局变量`webpackJsonp`，每个 JavaScript 文件都会访问这个全局变量来解析依赖关系。然而，Node.js 不会得到`manifest.js`和`vendor.js`，因此在试图呈现你的应用时会丢失全局变量和崩溃。

仍然利用该特性的一种方法是，只为服务器端脚本创建一个 webpack.mix.js 文件，为客户端脚本创建另一个 web pack . mix . js 文件。[这个评论](https://github.com/spatie/laravel-server-side-rendering/issues/26#issuecomment-452348326)展示了如何做到这一点。不幸的是，这是我目前知道的唯一一种提取依赖项的方法。

### 窗口/文档/ $ /本地存储/等。未定义

到目前为止，您的页面可能已经正确呈现了，但是还会遇到一些陷阱。

想象下面的

```
data() {
    name: localStorage.getItem('name')
} 
```

Enter fullscreen mode Exit fullscreen mode

和...崩溃！

在这一点上，这与插件或 Laravel 无关，只是在使用 SSR 时需要注意的一些事情。window/document/localStorage 等等只存在于客户端，不在节点内。

有两种解决方法可以修复崩溃。

1.  在访问这些类型的对象之前，请检查变量是否存在

```
data() {
    name: typeof localStorage !== 'undefined' ? localStorage.getItem('name') : null
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  将逻辑移至`mounted`方法。

```
data() {
    name: null
},
mounted() {
    // client only
    this.name = localStorage.getItem('name')
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Nuxt.js 中，你也可以使用全局的`process.client`布尔值来检查代码是在服务器上执行还是在客户端上执行。

### 结论

不得不或多或少地手动设置 SSR 确实让人欣赏像 Nuxt.js 这样的框架。

如果还有其他问题，可以在下面留言评论，但是先想想:Nuxt.js 是怎么做的？因为肯定有办法做到。

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。