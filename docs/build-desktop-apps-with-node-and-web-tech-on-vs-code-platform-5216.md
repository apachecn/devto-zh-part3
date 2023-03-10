# 在 VS 代码平台上使用节点和 Web 技术构建桌面应用

> 原文：<https://dev.to/sneezry/build-desktop-apps-with-node-and-web-tech-on-vs-code-platform-5216>

用 Node 和 web 构建桌面应用程序很酷，然而，electronic 有点重。VS Code，最流行的编辑器之一，是我每天使用的程序。它是用电子建造的。我能在它的平台上运行用 Node 和 web 编写的应用程序而不用重新安装运行时吗？经过一番尝试，是的！我做到了！

我做了一个 VS 代码扩展，提供了一个图库来显示和启动这样一个应用程序。你可以从 https://marketplace.visualstudio.com/items?安装它 itemName = sneez ry . vs code-toolkit。

这个扩展还处于非常早期的阶段，它只是为了验证我的想法。

安装扩展后，你可以在 VS 代码窗口的左下角找到一个绿色的烧杯，点击它打开应用程序库。

[![](img/0b4e969f41b9d1cbcbb71eaba40ee968.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qF5dwe61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/my4x9j1hiv36ij5djytc.png)

在图库中，你可以看到已经安装了一个名为 Installer 的应用程序。单击它并选择任意。tka(工具包应用程序)安装。

这里有一个样例工具包应用程序，你可以下载并尝试一下:[https://github.com/Sneezry/cpu.tka/releases](https://github.com/Sneezry/cpu.tka/releases)。

[![](img/3f8771eefe0f5b2f3732886426876423.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mOcaxP_R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1yj31pvt50kq9a713leg.png)

这个示例应用程序显示了你机器上的 CPU 使用情况。

[![](img/a56fe06a022bca63470fc48ed20fc10b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gSIMTCgz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6tmlp71pfdiormn9u7fe.png)

如何编写自己的 app？很简单！

首先是创建 package.json，在文件中定义`displayName`、`icon`、`main`和`view`。`displayName`是图库中显示的文字，`icon`是应用图标的相对路径。`main`是 app 的入口文件，比如`index.js`。您需要始终声明一个`main`函数，并将其导出到入口文件:

```
exports.main = function(webview) {
  // do something
} 
```

启动应用程序时会调用`main`函数。`webview`是 web 界面的上下文。你可以调用`webview.send(data)`发送数据到网络。

`view`是 web 的根路径。应用程序启动时，web 根路径中的`index.html`将会打开。

在 web 端，可以用内置的`NodeJS`对象调用任何导出节点函数。

例如，在条目文件中，我们导出了一个名为`foo` :
的函数

```
exports.main = function(webview) {}

exports.foo = function() {
  // do something
  return data;
} 
```

你可以这样在 web 中调用它:

```
const data = await NodeJS.foo(); 
```

所有 NodeJS 函数在 web 中都是异步的，不管你在节点端如何声明它。

如果要接收 web 端节点发送的数据，声明一个函数调用`messager`，由`webview.send`发送的数据将传递给这个函数。

```
function messager(data) {
  // do something
} 
```

下面是节点和 web 之间的数据流:

```
+--------------+                     +--------------------+
| Node         |                     | Web                |
|              |                     |                    |
| webview.send ----------------------> messager(data)     |
|              |                     |                    |
| exports.foo  <---------------------- await NodeJS.foo() |
|              |                     |                    |
+--------------+                     +--------------------+ 
```

此外，您可以在应用程序中安装任何节点模块。

毕竟，压缩你的应用，并将其重命名为`.tka`。

制作起来还有困难吗？参见示例 app 代码:[https://github.com/Sneezry/cpu.tka](https://github.com/Sneezry/cpu.tka)。

尽情享受吧！