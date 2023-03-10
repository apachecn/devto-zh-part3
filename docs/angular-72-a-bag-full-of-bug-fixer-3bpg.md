# Angular 7.2:一个装满 bug 修复剂的包

> 原文：<https://dev.to/algoworks/angular-72-a-bag-full-of-bug-fixer-3bpg>

AngularJS 可以使用标准 HTML5 属性来验证输入，或者可以创建自己的验证函数。对于静态文档，HTML 一直扮演着声明性语言的角色。

讨论 AngularJS 和 HTML 的下一个关系:

1.  最小化以文档为中心的 HTML 之间的阻抗不匹配。
2.  明智地组织 HTML。
3.  它对于同时使用 HTML 组织应用程序的数据服务非常有用。

Angular v7.2.0 推出了各种类型的测试版本:

**角度 7 . 2 . 0-β1**

1.  角度 7.2 的第一个β小于第二个β。最突出的区别是 Angular beta 的最新版本已经可以下载了。
2.  它提供了许多错误修复功能。
3.  为此决定的主题是巴泽尔。该工具用于构建和测试。这个版本将对 AngularJS 开发者有所帮助。
4.  您可以通过 CLI 中的 ng-new 命令将 Bazel 添加到项目中。

Bazel 将受到这一突破性变化的影响，它是 Angular 7.2 的第二个测试版的附件:

[![second beta of Angular 7.2](img/fec6364b1a16086b925b6cb4fb9fadee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UasNIrTm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gno6k4vgmk9vlq6pqnmf.png)

此版本还为路由器提供了一些附加功能。有五个创新功能:

1.  历史.州
2.  状态
3.  导航附加
4.  runGuardsAndResolvers
5.  pathParamsOrQueryParamsChange

上述特征用于:

1.  为 runGuardsAndResolvers 添加谓词函数模式。
2.  添加将在导航期间可用的导航类型。
3.  当导航回由 Angular 路由器管理的页面时，处理恢复整个对象。
4.  使用 runGuardsAndResolvers 添加 pathParamsOrQueryParamsChange 模式。
5.  允许将状态传递给 routerLink 指令。
6.  允许将状态传递给 NavigationExtras。

在这个版本 Angular 中有一个庞大的错误修复列表。最棒的是，它还包括对 7.1.4 版的修复。

1.  动画:实际的后代节点被标记为禁用，并且不会因延迟而截断小数。
2.  Bazel:在 schematics/bazel-workspace 文件中，它修复 TS 错误，考虑现有的角度安装，如果它的编译操作没有创建 esm5 输出，并且在编写 tsickle externs 时，不会抛出错误。
3.  Common: InjectableDecorator 提供了更精确的返回类型，为 InjectFlags 导出一个值。
4.  升级:从不同的降级模块，它允许嵌套组件，用降级模块()正确处理嵌套的降级组件，不依赖运行时来解析前向引用。

**Angular 7 . 2 . 0-beta 0**
这个版本的 Angular 7.2 比最新的 beta 版要小。
它只有两个错误修复，这是 Angular 7 . 2 . 0-beta 1 的缺点。

**Angular 7 . 2 . 0-beta 2**
这个版本不包括很多 bug 修复:
1。Bazel:在 schematics/bazel-workspace 文件中，它修复 TS 错误，可以从 latest-versions.ts 中读取最新版本，并使用 semver check。
2。Tsickle 依赖项不支持 typescript 3.1.x.
3。forms:form builder 组的类型化参数。