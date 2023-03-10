# 所以你认为你只是要“npm 安装”?重新考虑

> 原文：<https://dev.to/lirantal/so-you-think-youre-just-gonna-npm-install-think-again-lh4>

我们张开双臂拥抱了包锁文件的诞生，它引入了:跨不同环境的确定性安装，以及跨团队协作的强制依赖期望。

生活真好！或者我是这样想的…
如果我将一个变更放入项目的`package.json`文件，但是忘记提交它旁边的锁文件，会发生什么？

在依赖项安装期间，Yarn 和 npm 的行为相同。当他们检测到项目的`package.json`和锁文件之间的不一致时，他们通过安装与锁文件中记录的版本不同的版本来补偿基于`package.json`清单的这种变化。

这种情况对于构建和生产环境可能是危险的，因为它们可能会引入非预期的包版本，并使锁定文件的全部好处变得无效。

幸运的是，有一种方法可以告诉 Yarn 和 npm 遵守一组指定的依赖项及其版本，方法是从 lockfile 中引用它们。任何不一致都将中止安装。命令行应如下所示:

*   如果你正在使用 Yarn，运行`yarn install --frozen-lockfile`
*   如果您使用 npm 运行`npm ci`

-

我还写了一个完整的 [10 个 npm 安全最佳实践](https://snyk.io/blog/ten-npm-security-best-practices/)你应该在一篇文章中采用，其中包括一个高分辨率的可打印 PDF，如下图所示。

感谢阅读，也感谢 Verdaccio 团队的 Juan Picado 和我一起工作。[看看这个](https://snyk.io/blog/ten-npm-security-best-practices/)

[![Node Version](img/7581a36bdb7e2700216f6b77152f52fb.png)](https://snyk.io/blog/ten-npm-security-best-practices/)