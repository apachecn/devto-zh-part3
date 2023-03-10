# 关于 AMP，web 开发人员可能需要注意的一些事情

> 原文：<https://dev.to/simevidas/a-few-things-about-amp-that-web-developers-may-want-to-be-aware-of-3nbj>

说到 AMP，我有一些问题，但我也发现它的一些技术方面很有趣。不管你对 AMP 有什么想法，我认为保持对它和它的持续发展的了解是有用的。以下是一些可能与您相关的关于 AMP 的信息:

*   AMP 页面的尺寸**比**小，因为 [AMP 不允许自定义 JavaScript](https://www.ampproject.org/docs/fundamentals/spec) 。

    根据 Tim Kadlec 的[测试结果](https://timkadlec.com/remembers/2018-03-19-how-fast-is-amp-really/)(从 2018 年 3 月开始)，AMP 页面的大小与它们的规范页面相比要小得多(905 KB 对 2，762 KB)，并且它们执行的请求也少得多(61 对 318)。

    由于这个原因，移动连接速度慢或费用高的用户可能更喜欢 AMP。

* * *

*   AMP 库定期更新**新功能**。

    [最近的更新](https://amphtml.wordpress.com/2019/02/27/whats-new-in-amp-q1-2019-improvements-consent-videos-forms-and-lists/)包括一个`<amp-video dock>`属性，允许 AMP 页面在用户滚动时将视频最小化到视窗的角落，输入屏蔽帮助用户更有效地填写表单(见示例)，以及改进所有图像进出 lightbox 模式的过渡，等等。

    [![An example of input masking](img/d5bab77b65d796e029eac2ffca0a3203.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mbu92Qtm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yt2lqdnjwqng0juzue3q.png)

* * *

*   AMP 广告提供**安全保障**。

    不管你怎么想，AMP，ads 不能运行任意的 JavaScript，并且拥有一种可以在不信任来源的情况下被验证为安全的格式，这对网络的健康发展是一个巨大的进步。

* * *

*此内容最初发布于 [https://webplatform.news](https://webplatform.news) 。*