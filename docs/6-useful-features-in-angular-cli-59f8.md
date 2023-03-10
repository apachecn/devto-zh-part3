# Angular CLI 的 6 个有用功能

> 原文：<https://dev.to/bnevilleoneill/6-useful-features-in-angular-cli-59f8>

[![](img/6d15787d7663d24f7d3081ca7c716d9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--95s7Emvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AewHb_AIVCkLvrI8pDxo37Q.png)

随着谷歌 web 框架 Angular 的第二次迭代和 AngularJS 的完全重写，Angular 社区在 Angular 命令行界面(CLI)的开发效率上有了显著的提升。

CLI 使新老开发人员能够轻松创建只需一个命令就能工作的 Angular 应用程序。CLI 还管理项目配置、文件生成和构建过程，使开发工作流在 Angular 应用程序中更加简化和一致。

在本帖中，我们将看看 Angular CLI 提供的一些最有用的功能。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 安装 CLI

使用 CLI 提供的任何功能的第一步是安装 CLI。您可以使用以下命令轻松做到这一点:

`npm install -g @angular/cli`

如有必要，您可以随时参考[官方网站](https://cli.angular.io/)上 CLI 的安装步骤。

在这里，您可以使用以下 CLI 命令创建新的 Angular 项目:

`ng new my-angular-app`

一旦完成，您就可以 cd 到您的新项目并运行您的应用程序:

`cd my-angular-app`

`ng serve`

与 AngularJS 时代相比，Angular CLI 减少了我们在设置项目时需要做的大量工作，这使我们可以将更多的时间放在为应用程序创建新功能上。

### 代和别名

生成应用程序后，首先要做的事情之一就是开始为应用程序生成新的原理图，比如组件、服务和模块。

您可以通过利用 CLI 的 generate 命令来实现这一点:

`ng generate component my-component`

CLI 中的许多命令还包括别名，以减少运行这些命令所需的键入量。使用上面的例子，我们可以通过对生成命令使用别名`g`和对元件原理图使用别名`c`来缩短它:

`ng g c my-component`

您可以在 CLI 的 [wiki](https://github.com/angular/angular-cli/wiki/generate#available-schematics) 中查看使用 generate 命令创建的可用原理图的完整列表。

### 路由

如果您使用 CLI 生成一个新项目，您将很快启动并运行，但是默认设置非常简单。这些默认设置的一个副作用是，没有为具有独立视图路由的应用程序提供适当的结构，而这是现代 web 应用程序的常见要求。

在创建应用程序时，您可以通过添加一个简单的标志来手动将路由添加到您的项目中，CLI 可以为您完成所有这些工作。

创建新项目时，只需添加— routing 标志，CLI 将在`src/app/app-routing.module.ts`中为您的项目生成一个路由模块:

`ng new my-project --routing`

稍后，当您为您的应用程序开发模块时，您还可以生成单独的路由模块，这在您希望避免混淆根应用程序路由模块时非常有用。同样，在生成模块时使用相同的路由标志。

`ng generate module my-module --routing`

### 造型预处理程序

生成 Angular 项目时，可以选择对样式文件使用 CSS 预处理器，这样就可以使用 Sass、Less 或 Stylus 编写 CSS 样式。

幸运的是，CLI 通过在 ng new 命令中添加`-—`样式标志，使这一点变得非常容易。

`ng new my-project --style=scss`

如果您想使用不同的预处理器，只需用`less`或`styl`替换`scss`。

### 试运行

在使用 CLI 提供的任何命令时，您可能会发现自己希望可以对该命令进行一次测试，看看 CLI 将为您生成和更新什么，而不必在发生意外情况时实际创建文件并删除它们。

CLI 再次提供了一个标志来帮助您，该标志允许您检查命令的输出，而无需实际执行命令并修改您的项目。这个标志是—预演标志。

`ng g c my-test-component --dry-run`

如果你喜欢别名，你也可以使用这个旗帜的别名`-d`。

`ng g c my-test-component -d`

### 跳过测试

到目前为止，您可能已经注意到，这些生成命令中的许多会在您的应用程序中自动生成文件扩展名为`.spec.ts`的测试文件。这是一个很好的默认设置，显示了 Angular 如何将您推向最佳实践的方向，但是有时您希望覆盖默认设置并减少应用程序的额外膨胀。

使用 generate 命令生成逻辑示意图时，可以使用 spec 标志:

`ng g c my-testless-component --spec=false`

在使用新命令创建应用程序时，也可以这样做:

`ng new my-testless-application --skip-tests`

`ng new my-testless-application -S`

### 文档

当您在进行角度应用时，不可避免地会遇到需要参考[角度文档](https://angular.io/docs)获取更多信息的情况。

您可能已经在浏览器中将文档加入了书签，但是您总是可以使用 CLI 提供的 doc 命令来保存一些点击。

只需使用 doc 命令添加您的搜索词，CLI 将自动打开一个新的浏览器窗口，其中包含您在文档中指定的搜索词。

`ng doc viewchild`

### 结论

现在，您已经了解了 Angular CLI 为开发人员提供的附加命令和选项，我希望您在处理项目时能够找到将它们合并到开发工作流中的方法。如果您发现自己需要手动创建文件或进行项目配置，那么有必要检查一下 CLI 中是否有一个功能可以让这个过程变得更加简单。

如果您对 CLI 有任何疑问，并需要更多信息，请务必查看官方的 [CLI 命令参考](https://angular.io/cli)或 GitHub 上 CLI 的 [wiki 页面](https://github.com/angular/angular-cli/wiki)。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

Angular CLI 中的 6 个有用特性的帖子[首先出现在](https://blog.logrocket.com/6-useful-features-in-angular-cli-eb502bd95874/) [LogRocket 博客](https://blog.logrocket.com)上。