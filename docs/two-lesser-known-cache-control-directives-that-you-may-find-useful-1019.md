# 两个不太为人所知的缓存控制指令可能对您有用

> 原文：<https://dev.to/simevidas/two-lesser-known-cache-control-directives-that-you-may-find-useful-1019>

当谈到 HTTP `Cache-Control`响应头时，我试图遵循[杰克·阿奇博尔德](https://jakearchibald.com/2016/caching-best-practices/)的建议，它围绕着两个主要模式: **(1)** 使用长的`max-age`(例如，1 年)用于*从不*改变的内容(并且其 URL 是版本化的)，以及 **(2)** 使用`no-cache`用于*可以*改变的内容(浏览器将*总是*检查服务器，如果

除了`max-age`和`no-cache`之外，还有一些其他的`Cache-Control`指令在某些场景中也很有用。

*   **`no-store`指令**指示浏览器*而不是*将响应存储在缓存中。这对于包含敏感或时间关键信息的响应非常有用。

    ```
    Cache-Control: no-store 
    ```

* * *

*   **`immutable`指令**告诉浏览器响应不会随时间而改变，这实际上意味着浏览器*永远不会*向服务器确认是否有更新的版本。

    ```
    Cache-Control: max-age=31536000, immutable 
    ```

    当用户手动刷新页面时，即使缓存的内容仍然“新鲜”，浏览器也会检查服务器在这种情况下，`immutable`有助于防止对从不改变的内容进行不必要的检查。

* * *

参见 Harry Roberts 的文章“[平民缓存控制](https://mobile.twitter.com/csswizardry/status/1102547248083292161)”，了解不同`Cache-Control`指令的更多细节。

* * *

*此内容最初发布于 [https://webplatform.news](https://webplatform.news) 。*