# Chrome 开发工具的 9 个技巧和窍门

> 原文：<https://dev.to/lssweatherhead/9-tips-and-tricks-for-chrome-dev-tools-149c>

Chrome 越来越不仅仅是一个浏览器选择，它本身也是一个强大的开发工具。新功能定期推出，其中一些比其他功能更自然地进入(我的)开发周期。

以下是我最充分利用的一些功能，我认为值得一提:

# 1。条件断点

在脚本源代码中添加断点是一种非常有用的方法，可以知道哪里出错了(或者正确！)和哪里。通过指定您有兴趣进一步研究的参数标准，能够添加条件断点有助于您更快地找到问题的根源。理想！

[![Conditional breakpoints](img/4f42f30efc54627432e02b471dedb398.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nPtmPDo1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrwewe46jmx481wnvnxy.PNG)

在这里阅读更多关于[条件断点的信息。](https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints#conditional-loc)

# 2。观察变量

当使用断点时，可以在`sources`窗口询问变量。这看起来有点像这样:

[![Variable](img/548f70cae37b2019abea73be9d6dc3ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GKrew8qR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m485vscthxqsdn239c2s.PNG)

这很好，但是如果你想同时关注多个变量，或者想进行比较，或者只是提醒自己它们是什么(或者应该是什么)，那么就可以展开右边的`watch`面板，在那里添加你想要的变量:

[![Watch panel](img/07afe6befe9ef2800a3c44d685835796.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qgrVqFCI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u7klwv9h43watv8ryogn.PNG)

# 3。停止无限循环

如果你曾经不小心运行了一个 javascript 函数，意识到晚了一秒钟，不得不眼睁睁地看着你的电脑开始死机，你狂热地试图强制退出 Chrome，那么这一个就是为你准备的...

点击暂停脚本，然后...

[![Stop!](img/fc5074189a5b299dbf039e61842cd0f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gempUBeb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wzh7qn3g2jgcgi6pxiom.PNG)

在这里阅读更多关于[停止无限循环的内容。](https://developers.google.com/web/updates/2018/04/devtools#stop)

# 4。衡量互动

想知道一个网站需要多长时间吗...？

*   页面加载后多久有人会播放视频或点击按钮？
*   性能瓶颈在哪里？运行一个脚本方法需要多长时间？

您现在可以使用`window.performance`来触发测量开始和完成，从而提取数据。

```
onPageLoadOrMethodStart() {
    window.performance.mark('start');
    ...
}

onMethodCompletionOrCtaInteraction() {
    window.performance.mark('end');
    window.performance.measure('customMeasure', 'start', 'end');
}

retrieveData() {
    const customPerf = window.performance.getEntriesByName('customMeasure')[0].duration;
} 
```

Enter fullscreen mode Exit fullscreen mode

方便的是，数据也在顶部面板的`performance`窗口中打印出来，因此您可以一目了然地看到所有的测量结果。厉害！

阅读更多关于使用[用户计时 API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API) 测量真实世界交互的信息。

# 5。漂亮的打印缩小文件

生产中的缩小文件是行业标准，也是意料之中的，但有时附加断点或查看单个结构或方法也很方便。让它可读-让它漂亮！(`sources`面板的左下角):

[![Pretty print](img/99324d53e7346cd172a02cbe3bb2be57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kmaurP7A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0poyqpo2z63qi0xma1a9.PNG)

# 6。监控事件

有没有想过为什么您的事件侦听器不触发，或者在随机时间触发？

您可以使用`console`跟踪页面事件和交互。

立即尝试:

1.  右键单击并“检查”页面顶部的搜索输入栏
2.  将此复制到控制台:`monitorEvents($0, "key");`
3.  在搜索输入中键入一些内容...

点击阅读更多关于[监控事件的信息。](https://developers.google.com/web/tools/chrome-devtools/console/command-line-reference#monitorevents)

# 7。导出请求数据

你有麻烦了。有些事情看起来很奇怪，但你不知道为什么。你有一大堆网络请求要筛选，答案一定就在那里...你真的需要一只手，和别人分享不是很方便吗？

你可以！

你可以下载 HAR 文件格式的请求，然后发送给同事，同事可以将其导入到他们的 Chrome 浏览器中查看请求。只需右击任何请求并选择`Save as HAR with Content`。哒哒。

阅读更多关于[导出请求数据](https://developers.google.com/web/tools/chrome-devtools/network-performance/reference#save-as-har)以及如何[分析 HAR 文件](https://toolbox.googleapps.com/apps/har_analyzer/)的信息。

# 8。启用黑暗主题(纯美学...)

打开开发工具>转到`Settings`

打开它！

[![Dark theme](img/522b1c501b1cdc86ab3a7f1f2e284102.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bSqf-A1X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbydvc8jytswwp0nzgo4.PNG)

# 9。Chrome 扩展

还有许多非常方便的扩展，可以用于各种框架和工具。我发现特别有用的是:

*   VueJS 开发工具(真正出色的用户体验)
*   [ReactJS 开发工具](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
*   [谷歌分析](https://chrome.google.com/webstore/detail/google-analytics-debugger/jnkmfdileelhofjcijamephohjechhna)