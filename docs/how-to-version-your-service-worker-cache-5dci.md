# 让我们来解决这个问题:如何对您的服务工作人员缓存进行版本控制

> 原文：<https://dev.to/carmalou/how-to-version-your-service-worker-cache-5dci>

*[本帖最初出现在 carmalou.com](https://carmalou.com/lets-take-this-offline/2019/03/03/version-your-service-worker-cache.html)T3 上*

许多服务人员示例显示了一个类似如下的安装示例:

```
self.oninstall = function(event) {
    caches.open('hard-coded-value')
    .then(function(cache) {
        cache.addAll([ /.../ ])
        .catch( /.../ )
    })
    .catch( /.../ )
} 
```

让我们快速浏览一下上面的代码。当浏览器检测到服务人员时，会触发一些事件，其中第一个是 install 事件。这就是我们的代码出现的地方。

我们的函数创建一个名为`hard-coded-value`的缓存，然后在其中存储一些文件。这有一个小问题...我们的缓存是硬编码的！

这是有问题的。浏览器将每 24 小时与服务人员联系一次，并重新启动这个过程，但只有在有变化的情况下。你可以改变你的应用的 CSS 或 JavaScript，但是如果不改变服务人员，浏览器永远不会更新你的服务人员。如果服务人员从来没有得到更新，改变的文件将永远不会让您的用户的浏览器！

幸运的是，有一个非常简单的修复方法——我们将对缓存进行版本化。我们可以在服务人员中硬编码一个版本号，但是我们的应用程序实际上已经有了一个版本号。太方便了！

我们将使用来自`package.json`文件的应用版本号来提供帮助。这种方法也要求我们使用[网络包](https://webpack.js.org/)。

在我们的服务人员中，我们需要我们的`package.json`文件。我们将从`package.json`中获取版本号，并将其连接到我们的缓存名称。

```
self.oninstall = function(event) {
    var version = require(packagedotjson).version;
    caches.open('hard-coded-valuev' + version)
    .then(function(cache) {
        cache.addAll([ /.../ ])
        .catch( /.../ )
    })
    .catch( /.../ )
} 
```

事实证明，使用 webpack 的一些内置工具，实际上有一种比上面更好的方法。上述代码的一个问题是，您的`package.json`文件将被捆绑到您的服务人员中。这是非常不必要的，而且会增加你的包的大小。

我们将使用 [`DefinePlugin`](https://webpack.js.org/plugins/define-plugin/) 来使它更加清晰。

让我们给 webpack 文件中的`DefinePlugin`函数添加一个属性。我们就叫它`process.env.PACKAGEVERSION`。

它可能看起来像这样:

```
var version = require(packagedotjson).version;
new webpack.DefinePlugin({
  'process.env.PACKAGEVERSION': JSON.stringify(version)
});
</code> </pre> 
<sub>Source: [webpack `DefinePlugin`](https://webpack.js.org/plugins/define-plugin/)</sub>

And then in our service worker instead of referencing `version` directly, we'll use `process.env.PACKAGEVERSION`. It'll look like this:

<pre>
<code class="language-javascript">
self.oninstall = function(event) {
    caches.open('hard-coded-valuev' + process.env.PACKAGEVERSION)
    .then(function(cache) {
        cache.addAll([ /.../ ])
        .catch( /.../ )
    })
    .catch( /.../ )
} 
```

webpack 将在幕后为您工作，并将'`process.env.PACKAGEVERSION`'替换为合适的数字。这解决了需要更新我们的服务人员的问题，并且它以一种干净、简单的方式处理它。此外，当我们需要清理以前的缓存时，它会帮助我们。我接下来会写这方面的内容，敬请关注！

*如果你想了解更多关于服务人员的信息，我希望你能前往[serviceworkerbook.com](https://www.serviceworkerbook.com/)注册我的邮件列表，并在[推特](https://twitter.com/carmalou)上关注我！你会是第一个知道我的书《让我们离线吧》何时出版的人！*