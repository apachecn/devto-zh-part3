# htmlunitjars 已更新至 2.34.0

> 原文：<https://dev.to/hrbrmstr/htmlunitjars-updated-to-2340-4l8j>

不需要 Selenium、Splash 或 headless Chrome 的 javascript“启用”网络抓取的 in-dev [`htmlunit`包](https://git.rud.is/hrbrmstr/htmlunit)依赖于 [HtmlUnit](http://htmlunit.sourceforge.net/) 库，该库刚刚发布了[版本 2.34.0](http://htmlunit.sourceforge.net/changes-report.html#a2.34.0) ，进行了一系列广泛的更改，应该可以抓取更粗糙的 javascript“启用”网站。Chrome emulation 现在也与 Chrome 72 系列不相上下(我的 Chrome beta 是 73.0.3683.56，所以它非常接近最新版本)。

实际上，更新是对 [`htmlunitjars`包](https://git.rud.is/hrbrmstr/htmlunitjars)的更新，其中主项目 JAR 和从属 JAR 都接受了刷新。

自述文件和测试都在两个包中重新运行，Travis 很高兴。

如果你有一个可用的`rJava`装置(是的，现在是 2019 年，那仍然是“一件事”)，那么你可以做:

```
install.packages(c("htmlunitjars", "htmlunit"), repos = "https://cinc.rud.is/") 
```

安装它们并开始使用 DSL 或者直接使用 Java 类。

### 鳍

像往常一样，使用您喜欢的社交编码网站来记录功能请求或问题。