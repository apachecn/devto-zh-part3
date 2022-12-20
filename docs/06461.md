# 关于迁移到 TypeScript 和提高前端开发代码库整体质量的思考

> 原文：<https://dev.to/ben/thoughts-on-migrating-to-typescript-and-improving-the-overall-quality-of-the-frontend-dev-codebase-1121>

我认为在过去一年左右的时间里，TypeScript 的发展和日益流行意味着现在是在开发中使用它的好时机。

尼克·泰勒是最大的支持者，但这一切都与我的观点一致。

点击此处阅读更多内容:

[![nickytonline](img/6883bc3afaaae319b5553cb8ecf6b599.png)](/nickytonline) [## 使用类型脚本或流前端代码库开发？

### 尼克·泰勒 4 月 8 日 192 分钟阅读

#javascript #typescript #flow](/nickytonline/dev-to-with-a-typescript-or-flow-frontend-codebase-1n33)

我不希望我们在前端技术的前沿走得太远，这仍然是我的立场，但我认为 TypeScript 处于一个很好的位置，Rails 对“现代”JavaScript 的支持也同样处于一个很好的位置。

目前，我们在代码库的“旧”`app/assets/javascripts`区域有一些 JavaScript，在通过 Webpacker 配置的新的`app/javascript`区域有一些。

正如其他人提到的，我不认为我们需要完全重写 TypeScript，但我们应该开始重构，并以这种方式编写新功能。总的来说，我们应该尽可能地将最老的 JavaScript 移植到新的领域。

我认为这个计划可以非常有效地由社区来领导，而不是由我们的团队自上而下地领导，因为我们不需要提供很多具体的指导来改进这些领域。

任何在 README 和/或 docs 中为这种方法提供良好指导的 pull 请求都将受到欢迎。

我觉得我们现在可以开始做一些事情的部分原因是 GitPod 可能有助于帮助前端开发人员使用代码库并进行更改，而不用担心在本地安装和运行应用程序。

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 用 GitPod 在云中构建一个“本地”DEV 实例(非常简单)

### 本哈尔彭 1919 年 4 月 8 日 2 分钟阅读

#contributorswanted #gitpod #opensource #docker](/ben/spin-up-a-local-instance-of-dev-in-the-cloud-with-gitpod-it-s-incredibly-simple-pij)

只有时间能告诉我们 GitPod 是否能真正提高前端开发人员的工作效率，但我认为这是值得探索的。