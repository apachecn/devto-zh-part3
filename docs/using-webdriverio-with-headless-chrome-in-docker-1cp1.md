# 在 Docker 中使用 webdriverio 和 headless Chrome

> 原文：<https://dev.to/intricatecloud/using-webdriverio-with-headless-chrome-in-docker-1cp1>

在你的笔记本电脑上使用无头 chrome 进行 UI 测试效果很好，但是当你试图在 Docker 中运行测试时，它就不适合了。最近的一个工作项目是让 webdriverio 测试作为 Jenkins 管道的一部分在 Docker 容器中成功运行。在本地，我们的测试运行良好，但当我们在 docker 中运行它们时，它们变得非常古怪，显示随机的 Chrome 崩溃或“Chrome 不可达”错误。然而希望并没有破灭——事实上，有一个合适的选项咒语传递给 Chrome，让它成功运行(并且没有随机崩溃)。

**TL；**博士:这里有一小段`wdio.conf.js`展示了我们的 Chrome 配置。要了解为什么需要包括其中一些，请继续阅读。

```
// wdio.conf.js
...
browserName: 'chrome',
chromeOptions: {
  args: [
    '--disable-infobars',
    '--window-size=1280,800',
  ].concat((function() {
    return process.env.HEADLESS_CHROME === '1' ? [
      '--headless',
      '--no-sandbox',
      '--disable-gpu',
      '--disable-setuid-sandbox',
      '--disable-dev-shm-usage'] : [];
  })()),
... 
```

Enter fullscreen mode Exit fullscreen mode

### -禁用-开发-使用

如果当你的测试失败(或者类似于页面崩溃的消息)或者 Chrome 不可达时，你在日志中看到错误[(就像这个人所做的)“由于标签崩溃导致页面崩溃，会话被删除”](https://github.com/elgalu/docker-selenium/issues/20)，添加这个标志。

我们为什么需要这个？

Chrome 使用的一个 tmp 文件夹与它的内部内存管理相关，默认情况下它会尝试使用/dev/shm 目录。我不太明白 Chrome 的内部结构，但这里有一些来自木偶项目的细节。无论如何，Docker 容器对于/dev/shm 有一个[默认大小 64MB(对于- shm-size 为 cmd+f)](https://docs.docker.com/engine/reference/run/)。Chrome 很快就会耗尽里面的空间，然后你的页面就会崩溃，你的测试也会随之崩溃。不太好。对此有两种解决方案:

处理这个问题最简单的方法就是不使用它。有一些小缺点，但一个好的经验法则是，除非你试图在一个容器内并行多个并发的 Chrome 会话，否则你可以通过在 Chrome 选项中添加`--disable-dev-shm-usage`来安全地禁用它。

如果你([喜欢这个工程师](https://github.com/GoogleChrome/puppeteer/issues/1834#issuecomment-381106859))，那么你需要增加/dev/shm 的大小。对于码头集装箱，

*   有一个增加共享内存大小的标志`--shm-size=1G`,可以传递给 docker run 命令。
*   您还可以通过`-v /dev/shm:/dev/shm`将/dev/shm 从您的主机挂载到 docker 容器

### -禁用-setuid-沙盒-无沙盒

如果你看到无法连接到 Chrome [的错误，比如这个](https://github.com/GoogleChrome/puppeteer/issues/370)，你可能需要这些标志。Chrome 使用沙盒来保护你的机器免受互联网上随机内容的影响([详见此处](https://chromium.googlesource.com/chromium/src/+/HEAD/docs/linux_sandboxing.md))。如果你在一个*一次性*容器中运行 Chrome，那么你的容器已经充当了 Chrome 的沙箱，所以你不太需要*Chrome 的沙箱(你的里程可能会根据你要做的事情而有所不同)。*

我通常在各种在线文章中看到这两个标志一起传递。来自 Chromium 团队的这个答案表明`--disable-setuid-sandbox`只在 chrome 的旧版本中使用。

### -禁用-gpu

官方的木偶师文档建议这个标志只在 Windows 上需要。网上的各种代码片段通常将`--disable-gpu`和`--headless`放在一起使用，因为早期版本的 headless chrome 存在缺陷。

### -禁用-信息栏

这个标志可以帮助解决一个小问题——如果你在测试中截取了浏览器的屏幕截图，那么你会在浏览器页面的顶部看到一个栏，显示 Chrome 正在由自动化进程控制。这也将元素推向页面的更下方。它通常是好的，但是如果它妨碍了你，那么尝试使用这个选项。这面旗帜在 2018 年 1 月日[左右一度被移除，然后](https://github.com/GoogleChrome/puppeteer/issues/1765#issuecomment-356761402)[又重新加上](https://bugs.chromium.org/p/chromium/issues/detail?id=820453#c6)。

## 包装完毕

是的，Chrome 可以无标题运行，也可以在容器内运行，而且很稳定(大部分情况下)。然而，您确实需要正确的参数组合来使它正常工作，但希望这能为您节省几个小时调试随机页面崩溃的时间。在你的配置中尝试这些 Chrome 选项(如果你使用的是木偶师或其他 selenium/chrome 组合，它们也可以工作)。

遇到不同的错误？参见[如何附加 VSCode 调试器来逐步完成您的 UI 测试](https://www.intricatecloud.io/2019/03/debugging-webdriverio-tests-with-vscode/)以帮助您缩小问题范围。