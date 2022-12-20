# 不要相信 NPM 模块中的版本控制

> 原文：<https://dev.to/henryjw/dont-trust-semversioning-in-npm-modules-3f2m>

### 问题

```
{  "dependencies":  {  "some_module":  "^0.3.8",  "some_other_module":  "~0.1.3",  "dont_do_this":  "*"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

你对这些模式熟悉吗？什么事？那么我当然希望你知道你安装的所有包是否都遵循了[语义版本](https://docs.npmjs.com/about-semantic-versioning) (semver)！否则，这些可能会成为您未来部署中等待爆炸的定时炸弹。

一些软件包比其他的更好地遵循 semver，但是，从我的经验来看，你应该最谨慎的是 1.0 以下的软件包，因为它们肯定不遵循 SEM ver；他们通常使用第二个数字(0.x)作为主版本，第三个数字(0.1.x)作为补丁或功能版本！所以下次你运行版本`0.x`中的包时，你可能会大吃一惊。

更具体的例子，让我们用`knex`，它目前在版本`0.16.3`中。现在，如果您在您的`package.json`中指定安装版本`0.x`，那么这意味着下次您运行部署时，您可能会安装版本`0.17.0`，它可能包含影响您的应用程序的重大更改！

### 解

此时，您可能想知道如何解决这个问题。幸运的是，解决方案非常简单:锁定所有的包版本！你的`package.json`应该看起来更像下图，以避免任何意外。

```
{  "dependencies":  {  "some_module":  "0.3.8",  "some_other_module":  "0.1.3",  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当然，你也可以对你知道的任何遵循 semver 的包放宽规则。例如，您可能可以放心地信任像`react` (v16.8.2)和`restify` (v7.7.0)这样的包，因为它们都是非常受欢迎的项目，有许多主要版本。

#### 编辑

正如在讨论中提到的，这个问题也可以通过更新 package-lock.json 来缓解。这是因为一旦在 package-lock.json 中指定了版本，如果它满足 package.json 中指定的所需版本，它就不会被更新。例如，0.24.0 满足了 0.x 的要求，因此即使 0.25.0 被发布，npm 也将继续安装和使用 0.24.0。

#### 参考文献

[https://docs.npmjs.com/about-semantic-versioning](https://docs.npmjs.com/about-semantic-versioning)
T3】https://docs.npmjs.com/files/package-locks