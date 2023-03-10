# 用两行代码提升你的网站速度

> 原文：<https://dev.to/gijovarghese/boost-your-website-speed-with-2-lines-of-code-27e5>

假设你正在阅读一篇博客文章。到达底部时，您可以看到**相关的帖子**。如果你的浏览器甚至在你点击之前就预加载了这些页面，那会怎么样呢？

超快的内页，对吧？

# 引入快速链接

QuickLink 是一个小 JavaScript 代码，它将检测视窗中的链接，并将链接的 URL 预取。

### 安装

`npm install --save quicklink`

```
import quicklink from "quicklink/dist/quicklink.mjs";
quicklink(); 
```

[完整的 api 文档](https://github.com/GoogleChromeLabs/quicklink#api)

### 但是会加载不必要的数据，会不会拖慢我的用户？

Quicklink 会一直等到浏览器空闲，检查用户是否处于慢速连接状态或者是否启用了数据保护程序。

结果？这些网址将立即加载！

### 会不会拖慢我的网站？

只有不到 1KB 的压缩文件

## 有帮助吗？

在 Twitter 上关注我，我在那里分享了很多很酷的东西