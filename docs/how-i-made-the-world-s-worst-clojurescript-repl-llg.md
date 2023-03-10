# 我是如何创造出世界上最糟糕的喜剧剧本《REPL》

> 原文：<https://dev.to/swlkr/how-i-made-the-world-s-worst-clojurescript-repl-llg>

**TL；博士**
我尝试运行两个 clojure web 服务器并调用一个 JSON 端点，JVM 在我的[廉价 512 MB VPS 服务器](https://www.vultr.com/?ref=7614094)上崩溃了，所以我切换到 clojurescript 并为 atom 编写了自己的 prepl 客户端

## 概括地说我的 clojure 部署工作流程

我在 vim 模式下使用 atom 进行 clojure 开发，我做了一些很酷的东西，让所有 clo jure 人都喜欢！这个想法是抓取会议网站，从 HN api、clojureverse api 和 reddit api 获取 json 以获取 clojure 相关新闻，并将其全部放在一个网站上。我并不是 JVM 方面的专家，但是我知道使用 clojure 是很危险的:

*   构建类路径
*   编译成字节码
*   确保你有一个`-main`功能

…诸如此类的事情。不幸的是，我做了我通常用 twist 做的事情，而不是构建一个完整的 uberjar，我只是在这个帖子的帮助下简单地编译成字节码。然后我把它放在我的 512 MB VPS 服务器上，它甚至不能下载 json。它运行了一会儿，然后毫不客气地报告……**杀死了**。

## 我的廉价 VPS 梦想被*扼杀*

就这样，流程就说这么多，没有例外，什么都没有，就杀了。我将 JVM 设置为只使用 100 MB 的 RAM，但在运行另外两个 JVM(除了两个 clojure REPL 服务器之外)的 VPS 上，这还不够。我当时就决定我已经受够了，我决定在服务器上切换到 clojurescript。毫无疑问，v8 比运行 clojure 的 JVM 使用更少的资源，而且我的全栈框架会消耗更多的资源🙄

在我的兼职项目中，我不寻找*解决方案*，我寻找*借口*。

## node . js 中的 clojurescript 到底是怎么工作的？

现在有了将项目切换到 clojurescript 的完美借口，问题是我如何让我当前的 atom repl 客户端[cloin](https://github.com/mauricioszabo/atom-chlorine)使用 clojurescript？等等，等一下…我怎么才能让 clojurescript 在 node 上运行呢？

[DDG](https://duckduckgo.com) 来救援了！

这是我所理解的 clojurescript 工具环境的分解。有三种主要方法可以将 clojurescript 文件编译成 js，以便 node 可以理解它们:

*   [影子银行](http://shadow-cljs.org)
*   [Lumo](http://lumo-cljs.org)
*   [clj](https://clojurescript.org/guides/quick-start#running-clojurescript-on-node.js)

我不应该发表我的意见，但是…

*   shadow-cljs 看起来像 lein 和 after tools.deps，我并不热衷于我必须理解的 edn 的总量，以使项目工作，特别是因为 node 应该像`node index.js`一样简单
*   lumo 看起来很有趣，但是我不确定我从使用自托管 clojurescript 和 clojure 引导版本中获得了什么
*   clj 对我来说很熟悉，看起来也很简单，只要写一个`build.clj`文件就可以了，就像这样:

```
(ns  build  (:require  [cljs.build.api  :as  b]))  (b/build  "src"  {:output-to  "main.js"  :output-dir  "target"  :optimizations  :simple  :target  :nodejs  :main  'your-project.core}) 
```

Enter fullscreen mode Exit fullscreen mode

并用`clj build.clj`运行它。没有比这更简单的了，除了 clojure，我甚至不用安装任何东西！

所以，现在我已经决定了`clj`我可以使用氯和……哦，等等，[它只支持 lumo 和 shadow-cljs](https://github.com/mauricioszabo/atom-chlorine#how-to-work-with-clojurescript) 。在这一点上，一个理智的人会说，好吧，我珍惜我的空闲时间，我将只使用其中的一个，我可以继续我的真正目标，推出这个 clojure 网站，在一个地方聚合 clojure 信息。如果你已经走到这一步，你知道我不是一个理智的人。

## 膜中发了疯

我想在我的终端上运行这个:

```
clj -J-Dclojure.server.node="{:port 5555 :accept cljs.server.node/prepl}" -m cljs.main --repl-env node 
```

Enter fullscreen mode Exit fullscreen mode

我希望 atom 做正确的事情:连接到它并允许我向它发送 clojurescript 代码。我不知道为什么这如此复杂，clojure 应该是简单的。伊芙。

…请继续关注我的下一篇帖子，在那里我将讲述 atom 包开发的所有事情，以及我如何不情愿地从普通的旧 javascript 切换到 clojurescript，只是为了从 prepl 服务器读取 EDN。