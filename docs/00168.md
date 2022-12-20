# 管理相关性版本

> 原文：<https://dev.to/featurist/manage-dependency-versions-17on>

*原帖[https://featurist . co . uk/blog/keeping-dependencies-update/](https://featurist.co.uk/blog/keeping-dependencies-up-to-date/)*

在前`yarn.lock` / `package-lock.json`时代，只有一种方法可以确保你的项目不会因为某个依赖库引入了突破性的改变而中断:在`package.json` :
中设置*精确的*包版本

```
 "dependencies":  {  "express":  "4.17.1",  "sqlite3":  "4.0.8",  "left-pad":  "1.2.0"  } 
```

然而，这里有一个明显的问题:没有任何东西可以保证依赖关系不会转移。每一个新的`npm install`——例如当项目被部署到生产时——都有可能中断。对于勇敢的人来说，那是黑暗的时代。

现在有了 yarn 和`package-lock.json`之后，所有版本——不仅仅是明确的依赖关系——最终都解决了。作为一个副作用，保留`package.json`中指定的版本没有多大意义。事实上，一旦你添加了一个依赖项，它的版本只在锁文件中查找。我们不妨将所有依赖版本都设置为`latest` :

```
 "dependencies":  {  "express":  "latest",  "sqlite3":  "latest",  "left-pad":  "latest"  } 
```

随着时间的推移，你不断更新依赖关系。无论如何你都应该做。将所有东西升级到最新版本可能会损坏东西，有时这些东西很容易修复。其他时候-没那么多。有时最新版本会引入一些你无能为力的错误。

所以你可能会选择不升级*的一些*依赖项。现在是时候返回并交换那些软件包上的`latest`-记住我们已经将所有版本设置为`latest` -到任何已知有效的版本。

上面的方法，如果被例行应用，将留给你`package.json`，其中大多数版本被设置为`latest`，可能有少数更严格的版本:

```
 "dependencies":  {  "express":  "latest",  "sqlite3":  "latest",  "left-pad":  "1.2.0"  } 
```

这有好处:

*   `yarn upgrade`不升级严格版。所以可以经常运行，不碰那些麻烦的。
*   在`package.json`中，违规包一目了然
*   `git blame`在特定版本的行上显示了一个 commit，其中包含了为什么升级这个包会有问题的任何细节(例如，github 问题的链接)。对于`package.json`中缺少注释，这是一个很好的解决方法。