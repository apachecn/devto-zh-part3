# 如何应对事件流漏洞

> 原文：<https://dev.to/alex_barashkov/how-to-deal-with-the-event-stream-vulnerability-5g2k>

在过去几天的互联网上，你可能已经看到了许多关于**事件流 npm 包漏洞**和该包窃取比特币的新闻。令我失望的是，在实际得到以下问题的答案之前，我必须阅读大量的文章:

*   它会影响我的项目吗？
*   **我该怎么办？**

所以我决定把我今天的研究和一个简短的概述放在这里，尽可能地帮助你找到并解决你项目中的问题。

*   现有信息表明，仅受事件流包漏洞影响的目标包是 copay-dash。**如果你没有共付线，放松下来，平静地继续阅读**；
*   [event-stream@3.3.6](mailto:event-stream@3.3.6) 依赖包 [flatmap-stream@0.1.1](mailto:flatmap-stream@0.1.1) 存在漏洞；
*   已从 npm 注册表中删除该软件包的易受攻击版本；
*   从 npm 注册表中删除软件包会导致在 npm 安装(全新安装，无缓存)和 npm 审核时出现错误。

```
npm ERR! code ENOAUDIT 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
npm ERR! code E404
npm ERR! 404 Not Found: flatmap-stream@0.1.1 
```

Enter fullscreen mode Exit fullscreen mode

*   如果您有那个错误，您的项目有易受攻击的包；
*   很多流行的包都受到了影响，比如**nodemon**；
*   搜索 yarn.lock 或 package-lock.json，找到安装 [flatmap-stream@0.1.1](mailto:flatmap-stream@0.1.1) 的根包；
*   访问受影响的包的 github 页面，也许已经有新版本的补丁了。所以只要用一个类似`npm install nodemon@latest -D`的命令安装新版本即可；
*   有关这种情况的更多信息，请阅读文章