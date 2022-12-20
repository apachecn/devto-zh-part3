# quick link——一个小型 JS 库，用于快速加载后续页面并改善用户体验

> 原文：<https://dev.to/paramharrison/quicklink-a-small-js-library-to-fast-load-subsequent-pages-and-improve-user-experience-13e9>

大多数现代 web 框架通过不同的技术，如代码分割、前端缓存、服务工人等，来更快地处理后续页面的加载。

其他一些提高网站速度的常用技术有，

*   在空闲时间预取下一页的资产
*   预加载下一个 URL 并缓存它

像`medium`、`wikipedia`、`dev.to`、`quora`这样的公司使用类似的技术来加快页面加载速度。

这些技术需要大量基于用户设置、设备和网络速度等的智能。

## 引入快速链接

一个小的 javascript 库(< 1Kb ),它使得后续的页面加载更快，还提供了一些智能加载技术。

### 工作原理

*   当链接在视窗中时，它加载页面上的所有链接(使用`Intersection Observer`)
*   利用它的聪明，它不会多次加载这些链接
*   如果用户处于慢速连接(使用`navigator.connection.effectiveType`)并且用户选择在移动设备中保存数据(使用`navigator.connection.saveData`)，它不会加载这些技术
*   它不会中断网站内容，它只在空闲时间开始运行(使用`requestIdleCallback`)

查看这里的文档以获得更多关于这个库的信息。它是由谷歌 chrome 实验室开源的

*   [https://github.com/GoogleChromeLabs/quicklink](https://github.com/GoogleChromeLabs/quicklink)

### 如何在你的网站上使用它

```
// ES6 way
import quicklink from 'quicklink/dist/quicklink.mjs';
quicklink();

<!-- Include as script -->
<script src="dist/quicklink.umd.js"></script> <script>
  /*
        you can load the script DOMContentLoaded, or Window load callbacks,
        it won't cause any issues, since quicklink start executing only in browser idle time.
    */
  quicklink();
</script> 
```

quicklink 接受很多选项，你可以在 [github link](https://github.com/GoogleChromeLabs/quicklink) 的文档中找到。

忽略少数 URL 是广泛使用的选项，大多数其他选项都有默认值。

例如，忽略所有带有扩展名`.pdf`的 URL、带有子字符串`/api`的 URL 以及明确要求链接到`noprefetch`T3 的链接

```
quicklink({
  ignores: [
    // ignore all /api/* urls
    /\/api\/?/,
    // ignore .pdf files
    uri => uri.includes('.pdf'),
    // ignore all links, scripts which has explicit noprefetch
    (uri, elem) => elem.hasAttribute('noprefetch'),
  ],
}); 
```

Quicklink 易于设置，在大多数情况下足够智能，可以优化后续的页面加载体验。尽管它没有解决第一次加载的体验。

希望它提高你网站的性能，改善用户体验。试试看，这是一个令人兴奋的测试用户 web 性能的库。