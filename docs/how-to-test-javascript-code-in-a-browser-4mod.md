# 如何在浏览器中测试 JavaScript 代码

> 原文：<https://dev.to/jaswantkaur/how-to-test-javascript-code-in-a-browser-4mod>

测试 JavaScript 可能会很痛苦。已经开发了许多工具、技术和框架来使这个过程尽可能无痛。虽然其中一些工具如 Mocha、Jasmine 和 Jest 提供了测试结构，但其他一些工具如伊斯坦布尔和 Blanket 也会生成代码覆盖报告。在浏览器中测试 JavaScript 代码的不同方法有不同的前景和结果。很难将所有解决方案的范围缩小到一种工具或技术！

让我们快速浏览一下在浏览器中测试 JavaScript 代码的一些最流行的方法。

## 1。JSFiddle

无论你是使用 JavaScript 还是 React 和 Vue 这样的框架， [JSFiddle](http://jsfiddle.net/) 都是适合你的工具。它是一个在浏览器中编写和测试 JavaScript 代码的在线工具。它于 2009 年以“Mooshell”的名字推出。如果您正在开发一个 web 应用程序，并且使用任何种类的 JavaScript 库，那么 JSFiddle 是值得一看的。界面非常简单，你只需要输入一些 JavaScript，甚至添加一些 HTML 和 CSS 就可以立即看到结果。在使用 JSFiddle 将 JavaScript 代码片段添加到您的项目之前，请测试它们！

## 2。跨浏览器测试工具

虽然你可以很容易地使用 JSFiddle 和 CodePen 这样的工具来测试你的 JavaScript 代码，但是这些工具不会向你展示你的代码在不同的浏览器或者移动浏览器中是如何输出的。如果你想测试你的代码的跨浏览器兼容性，你需要使用在线跨浏览器测试工具，比如 [LambdaTest](https://goo.gl/CvaBNC) 。它支持实时交互式浏览器测试、自动屏幕截图测试、响应式布局测试和智能视觉 UI 测试。该工具将大大加快您的测试周期，并帮助您解决代码中特定于浏览器的问题。

## 3。卡玛+茉莉+谷歌 Chrome

Karma 是一个工具，可以让你在浏览器中测试 JavaScript 代码，用于很多测试目的。然而，它并不测试代码本身。它执行代码，但依赖 Jasmine 和 Mocha 等第三方库进行测试。除此之外，它需要一个真正的浏览器。因此，Google Chrome 必须安装在您的本地机器上，这种 JavaScript 方法才能工作。它以无头模式运行谷歌浏览器。

## 4。密码笔

CodePen 是在线测试你的 HTML、CSS 和 JavaScript 代码的最好的在线工具之一。这个开发者社区有很多东西要教！这个开源学习环境可能拥有最大的开发者社区，拥有多达 330000 名注册用户，他们一直致力于开发令人惊叹的前端应用程序。它是建立和部署网站、向世界展示你的工作和构建测试用例的最佳平台之一。

## 5\. JSBin

JSBin 是 JSFiddle 的一个干净整洁的替代品。如果你想要一个更容易理解的界面，那么 JSBin 就是你要的工具。该平台有一个免费的和亲访问。对于高级功能，如私人垃圾箱和无限制的 dropbox 同步，您需要使用 pro，但是，您可以使用 JSBin general access 轻松测试 HTML、CSS 和 JavaScript 的任何组合。

## 6。活织

Liveweave 是另一个可以测试 JavaScript 代码的编码平台。它具有实时预览功能，并配有一把尺子来帮助您进行响应性设计。它为 HTML、CSS 和 JavaScript 提供的代码提示功能使得初学者可以轻松地输入代码。除此之外，你可以使用 Liveweave 下载一个. zip 文件，也可以添加和使用外部库，如 jQuery、AngularJS、Bootstrap 等。在你的代码中非常容易。

这些是在浏览器中测试 JavaScript 代码的一些最流行的方法。除此之外，你甚至可以使用 CSSDeck 和 Dabblet 这样的工具。这些同样不错，也很好用。除此之外，Firebug 和 Chrome 开发工具都有 JavaScript 控制台，你可以在那里输入 Javascript 代码来执行。这同样适用于 Internet Explorer 8+，Opera，Safari 和其他许多现代浏览器。

来源:dzone.com