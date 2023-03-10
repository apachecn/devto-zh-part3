# 解决永恒的 SEO 问题，并为现代 JavaScript 网站提供 SSR，而无需编写一行代码

> 原文：<https://dev.to/gophobic/solving-the-eternal-seo-problem-and-providing-ssr-for-modern-javascript-websites-without-writing-a-single-line-of-code-1ll7>

## 到底是什么问题？

每当你用 React.js、Vue.js、Angular.js 等现代前端 javascript 框架开发网站时...迟早你要处理痛苦的永恒的搜索引擎优化问题。由于大多数搜索引擎甚至不执行 javascript 来生成包含大多数有价值的页面内容的最终 DOM，您的网站在 SEO 排名中肯定会受到伤害，因为搜索引擎在您的 HTML 正文中几乎看不到任何有价值的内容。原生框架 SSR(服务器端渲染)和/或将您的网站开发为[同构](https://en.wikipedia.org/wiki/Isomorphic_JavaScript)可能是理想的解决方案，但它需要在您的第一行代码中就得到处理，其复杂性会随着您的 webapp 的增长而增长，并且也会随着一个不一致的依赖项而立即变得无效。更简单的网站(小型商业网站、技术文档网站等)..)可能只是用一个静态的站点生成框架比如 [gatsby.js](https://github.com/gatsbyjs/gatsby) 或者 [Docusaurus](https://github.com/facebook/docusaurus) 来解决这个问题。但是如果你正在处理一个更复杂的 webapp，这样的框架永远不会是一个好的选择。此外，如果你有一个已经投入生产的大项目，原生框架 SSR 可能会太复杂和太晚。这就是为什么搜索引擎优化成为现代网络应用的永恒问题。

然而，一年前发生了一件事，谷歌宣布从版本 59 开始发售“无头”Chrome。随着 [Chrome Devtools 协议](https://chromedevtools.github.io/devtools-protocol/)，这为开发者远程控制 Chrome 打开了一个新的世界。无头 Chrome 主要用于自动化测试。但最有趣的是，headless Chrome 成为了一个不太可能解决永恒的 SEO 问题的完整解决方案，一个完全独立于您可能使用的任何前端框架、堆栈、版本、依赖性或后端堆栈的解决方案！听起来好得难以置信，对吧？

## Rendora？

Rendora 是一个新的 FOSS golang 项目，在过去的几天里一直在 GitHub 上流行，值得一些关注。Rendora 是一个动态渲染器，它使用 headless Chrome 毫不费力地为网络爬虫提供 SSR，从而提高 SEO。动态呈现仅仅意味着服务器向诸如 GoogleBot 和 BingBot 之类的网络爬虫提供服务器端呈现的 HTML，同时向普通用户提供典型的初始 HTML，以便在客户端呈现。最近，[谷歌](https://developers.google.com/search/docs/guides/dynamic-rendering)和[必应](https://blogs.bing.com/webmaster/october-2018/bingbot-Series-JavaScript,-Dynamic-Rendering,-and-Cloaking-Oh-My)都推荐了动态渲染，并且在[谷歌 I/O’18](https://www.youtube.com/watch?v=PFwUbgvpdaQ)中也有讨论。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [伦多拉](https://github.com/rendora) / [伦多拉](https://github.com/rendora/rendora)

### 使用 headless Chrome 的动态服务器端渲染轻松解决了现代 javascript 网站的 SEO 问题

<article class="markdown-body entry-content container-lg" itemprop="text">

# 伦多拉

[![Rendora](img/6b56ae32ef0b45a1f9197bacf56c3f7c.png "Rendora")](https://raw.githubusercontent.com/rendora/rendora/master/docs/pics/logo_200.png)

[![Go Report Card](img/c642e18bfe73411a9ddd333fd76439fb.png)](http://goreportcard.com/report/rendora/rendora)[![CircleCI](img/3981b5b90a8e6d9e7763d0448f409a63.png)](https://circleci.com/gh/rendora/rendora/tree/master)[![GoDoc](img/5b5c91069f6206c270359cfa09ee94a6.png)](https://godoc.org/github.com/rendora/rendora)[![License](img/7ecef471fe245c1d2adc417c5be4065b.png)](https://github.com/rendora/rendora/blob/master/LICENSE)[![Join the chat at https://discord.gg/6yyErk8](img/22b2d3027f083b172d9f1dac6dafc109.png)](https://discord.gg/6yyErk8)

Rendora 是一个动态渲染器，主要向网络爬虫提供零配置服务器端渲染，以便毫不费力地改善在 React.js、Vue.js、Angular.js 等现代 Javascript 框架中开发的网站的 SEO...Rendora 的工作完全独立于你的前端和后端栈

[![Rendora's Diagram](img/3a46781d399bcb7c7d1169e5c8c78814.png "Rendora's Diagram")](https://raw.githubusercontent.com/rendora/rendora/master/docs/pics/diagram.png)

## 主要特征

*   前端和后端代码无需任何更改
*   基于用户代理和路径的过滤器
*   用 Golang 编写的单个快速二进制文件
*   多种缓存策略
*   支持异步页面
*   普罗米修斯矩阵
*   选择您的配置系统(YAML、TOML 或 JSON)
*   容器就绪

## Rendora 是什么？

Rendora 可以被看作是一个反向 HTTP 代理服务器，位于你的后端服务器(例如 Node.js/Express.js, Python/Django 等...)和潜在的前端代理服务器(例如 nginx、traefik、apache 等...)或者甚至直接发送到外部世界，当它根据配置检测到白名单中的请求时，除了之外，它实际上什么也不做，只是传输请求和响应。因为…

</article>

[View on GitHub](https://github.com/rendora/rendora)

[![rendora in action](img/3a46781d399bcb7c7d1169e5c8c78814.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4fs7Jhyg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rendora/rendora/master/docs/pics/diagram.png)

Rendora 的工作原理是充当后端服务器(例如 Node.js、Golang、Django 等)前面的反向 HTTP 代理...)并根据配置文件检查传入的请求；如果它检测到服务器端呈现的“白名单”请求，它会命令 headless Chrome 请求并呈现相应的页面，然后将最终的 SSR 标记的 HTML 响应返回给客户端。如果请求被列入黑名单，Rendora 只是充当一个无用的反向 HTTP 代理，并原样返回来自后端的响应。Rendora 与同一领域的另一个伟大项目 [rendertron](https://github.com/GoogleChrome/rendertron) 的不同之处在于，它不仅通过使用 golang 而不是 Node.js 提供了更好的性能，使用缓存来存储 SSR 页面并跳过提取不必要的资产，如字体和图像，这会降低无头 Chrome 的渲染速度，而且它根本不需要对后端和前端代码进行任何更改！让我们看看 Rendora 的运行情况，以了解它是如何工作的。

## Rendora 在行动

让我们编写最简单的 React.js 应用程序

```
import * as React from "react"
import * as ReactDOM from "react-dom"

class App extends React.Component {
    render() {
        return (
            <div>
                <h1>Hello World!</h1>
            </div>
        )
    }
}

ReactDOM.render(
    <App />,
    document.getElementById("app")
) 
```

现在让我们使用 webpack 和 babel 将它构建成规范的 javascript。这将产生最终的 javascript 文件`bundle.js`。那么我们来写一个简单的`index.html`文件。

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
</head>

<body>
    <div id="app"></div>
    <script src="/bundle.js"></script>
</body>

</html> 
```

现在让我们使用任何简单的 HTTP 服务器来服务`index.html`，我用 golang 写了一个监听端口`8000`的服务器。现在，无论你何时使用浏览器访问服务器`http://127.0.0.1:8000`并查看页面源代码，你都会看到与上面的 HTML 代码完全相同的内容。这是意料之中的，因为在浏览器 javascript 引擎执行了`bundle.js`之后，React 应用程序的`Hello World`头被生成并添加到 DOM 中。现在让我们使用 Rendora 并在`YAML`
中编写一个简单的配置文件

```
listen:
    port: 3001

backend:
    url: http://127.0.0.1:8000

target:
    url: http://127.0.0.1:8000

filters:
    userAgent:
        defaultPolicy: whitelist 
```

这个配置文件是什么意思？我们告诉 rendora 监听端口`3001`，我们的后端可以在`http://127.0.0.1:8000`上被寻址，以便 rendora 代理来自和去往它的请求，并且我们的 headless Chrome 实例应该使用它作为白名单请求的目标 url，但是由于我们为了本教程而将所有用户代理列入白名单，因此所有请求对于服务器端呈现都是有效的。现在让我们运行无头 Chrome 和 Rendora。我将使用 Rendora 提供的码头图片:

```
docker run --tmpfs /tmp --net=host rendora/chrome-headless
docker run --net=host -v ~/config.yaml:/etc/rendora/config.yaml rendora/rendora 
```

现在重要的时刻来了，让我们再次尝试寻址我们的服务器，但是这次通过 rendora 使用地址`http://127.0.0.1:3001`。如果我们这次检查页面来源，会是:

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
</head>

<body>
    <div id="app"><div><h1>Hello World!</h1></div></div>
    <script src="/bundle.js"></script>
</body>

</html> 
```

你看出区别了吗？`<div id="app"></div>`中的内容现在是服务器发送的 HTML 的一部分。就这么简单！无论您使用 React、Vue、Angular、Preact 的任何版本和依赖项，也无论您的后端堆栈是什么(例如 Node.js、Golang、Django 等...)，无论你是有一个组件非常复杂的网站，还是只是一个“Hello World”app，编写那个`YAML`配置文件就是你向搜索引擎提供 SSR 所需要的全部。值得一提的是，您通常不想将所有请求列入白名单，您只是想将与网络爬虫相对应的某些用户代理关键词(如`googlebot`、`bingbot`等)列入白名单...)同时保持默认策略为`blacklist`。

Rendora 还提供 Prometheus 指标，以便您可以获得 SSR 延迟和其他重要计数器的直方图，如请求总数、SSR 请求总数和缓存 SSR 请求总数。

你需要在你的后端服务器前使用 Rendora 作为反向 HTTP 代理来让它工作吗？答案幸好是否定的！Rendora 提供了另一个可选的 HTTP API 服务器，默认监听端口`9242`来提供渲染端点。所以你可以实现你自己的过滤逻辑，让 Rendora 给你一个 SSR 页面。让我们尝试一下，让 Rendora 再次呈现上面的页面，但是这次使用带有 curl 的 API 呈现端点:

```
curl --header "Content-Type: application/json" --data '{"uri": "/"}' -X POST 127.0.0.1:9242/render 
```

您只需得到一个 JSON 响应

```
{  "status":200,  "content":"<!DOCTYPE html><html lang=\"en\"><head>\n <meta charset=\"UTF-8\">\n</head>\n\n<body>\n <div id=\"app\"><div><h1>Hello World!</h1></div></div>\n <script src=\"/bundle.js\"></script>\n\n\n</body></html>",  "headers":{"Content-Length":"173","Content-Type":"text/html; charset=utf-8","Date":"Sun, 16 Dec 2018 20:28:23 GMT"},  "latency":15.307418  } 
```

你可能已经注意到渲染这个“Hello World”React 应用程序**的延迟，在我非常繁忙的旧机器上，在没有使用缓存的情况下，只花了大约 15 毫秒**！所以无头 Chrome 和 Rendora 真的那么快。

## 其他用途

虽然 rendora 主要用于服务器端渲染或 SSR，但您可以轻松地使用它的 API 来抓取 DOM 主要由 javascript 生成的网站。