# 我挑战你写出一个更好的解析器！

> 原文：<https://dev.to/ahmedmusallam/i-challenge-you-to-write-a-better-parser-446e>

事情是这样的，我写了这个非常愚蠢的 [Sling URL](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) 解析器:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [艾哈迈德-穆萨拉姆](https://github.com/ahmed-musallam) / [笨笨-吊索-URL-解析器](https://github.com/ahmed-musallam/stupid-sling-url-parser)

### 一个非常愚蠢的 JS 来解析 Apache Sling URL

<article class="markdown-body entry-content container-lg" itemprop="text">

# 一个非常愚蠢的 Apache Sling URL 解析器

这是一个在 JS 中解析 [Apache Sling Url](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) 的愚蠢实现。**你真的应该用[思乐力](https://github.com/nateyolles/sluri)T5。**

我做这个是为了好玩。只支持现代浏览器(抱歉不抱歉 IE)和 NodeJs 10+。而且，它极其小！约 0.8KB 缩小。Sluri 缩小了 6.5 KB，但是有更多的功能。

## 使用

在`/dist`中获取缩小版本

```
const slingUrl = new SlingUrl('/a/b.s1.html/c/d');

console.log(slingUrl.resourcePath) // returns: "/a/b"
console.log(slingUrl.selectors)    // returns ["s1"]
console.log(slingUrl.extension)    // returns "html"
console.log(slingUrl.suffix)       // returns "/c/d"

// if any of the properties above does not exist, null is returned.
```

## 来源

参见 [/src/SlingUrl.js](https://raw.githubusercontent.com/ahmed-musallam/stupid-sling-url-parser/master//src/SlingUrl.js)

## 运行测试

```
npm test 
```

</article>

[View on GitHub](https://github.com/ahmed-musallam/stupid-sling-url-parser)

整个源代码都在这个文件里:[https://github . com/Ahmed-musallam/stupid-sling-URL-parser/blob/master/src/slingurl . js](https://github.com/ahmed-musallam/stupid-sling-url-parser/blob/master/src/SlingUrl.js)

我在这里设置了一个相当低的门槛，因为我写这个非常快，唯一的目的是节省字节。

现在，我想看到你试着写一些更好的东西！如果你愿意的话，这将是一项很好的锻炼。此外，你还可以在网上向一些随机的人展示他们是多么的错误。

要重新构建它，你只需要知道 Apache Sling 的 [URL 分解文档](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html)

文档中有例子，我已经为我的解析器编写了[测试](https://github.com/ahmed-musallam/stupid-sling-url-parser/blob/master/test/test.js)

编码快乐！