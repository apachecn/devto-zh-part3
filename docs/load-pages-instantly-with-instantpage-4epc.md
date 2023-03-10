# 使用 instant.page 立即加载页面

> 原文：<https://dev.to/lauragift21/load-pages-instantly-with-instantpage-4epc>

我发现了一个很酷的工具来提高网站的性能。 [Instant.page](https://instant.page/) 让你的站点页面在 1 分钟内即时化，提高你 1%的转化率。

您所需要做的就是在`<body>`标签之前添加这个脚本。

```
<script src="//instant.page/1.1.0" type="module" integrity="sha384-EwBObn5QAxP8f09iemwAJljc+sU+eUXeL9vSBw1eNmVarwhKk2F9vBEpaN9rsrtp"></script> 
```

Enter fullscreen mode Exit fullscreen mode

感谢 Alexandre Dieulot 创造了如此有用的工具！你可以在这里找到 GitHub Repo:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [即时页面](https://github.com/instantpage) / [即时页面](https://github.com/instantpage/instant.page)

### 让你的网站页面在 1 分钟内变得即时，并提高 1%的转化率

<article class="markdown-body entry-content container-lg" itemprop="text">

# 即时页面

1 分钟内让你的网站页面瞬间化，提高你 1%的转化率。

<g-emoji class="g-emoji" alias="information_source" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2139.png">ℹ️</g-emoji> 的信息在[网站](https://instant.page)上。

<g-emoji class="g-emoji" alias="scroll" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dc.png">📜</g-emoji>出处在 [instantpage.js](https://github.com/instantpage/instant.page/blob/master/instantpage.js) 。

<g-emoji class="g-emoji" alias="star2" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f31f.png">🌟</g-emoji>启动该存储库以跟踪其发展。

## 试验

使用[节点](https://nodejs.org/)，运行:

`node test/app.js`

并访问 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 。或者用文件名后的参数指定另一个端口。

## 使变小

要将 instantpage.js 缩小为 instantpage.min.js，请全局安装 uglify-es(`npm i uglify-es -g`)，然后运行`npm run minify`。

</article>

[View on GitHub](https://github.com/instantpage/instant.page)