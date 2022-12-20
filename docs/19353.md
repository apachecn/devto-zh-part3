# selenium 上的 Chromedriver 与 phantomjs

> 原文：<https://dev.to/manas1v/chromedriver-vs-phantomjs-on-selenium-7n1>

在为其中一个项目实现 selenium automation 时，我们评估了 casperjs、phantomjs、selenium，现在又用 chromedriver 评估了 selenium。在所有这些工具中，chromedriver 似乎是最强大的，也是目前最受欢迎的。以下是我使用 chromedriver 的原因:

1.  易于安装
2.  phantomjs 留下了太多陈旧的进程，导致内存泄漏。chromedriver 没有遇到这个问题
3.  需要 centos 7。它与 centos 6 存在兼容性问题
4.  多个 ChromeOptions()用于更精细的控制
5.  允许通过点击按钮和“另存为”对话框自动下载浏览器。基于硒的幻影不能做到这一点
6.  所有 selenium WebDriver 方法都是可用的
7.  持续支持。由谷歌-[http://chromedriver.chromium.org/help](http://chromedriver.chromium.org/help)支持和管理，这允许创建强大的网络爬虫和机器人！