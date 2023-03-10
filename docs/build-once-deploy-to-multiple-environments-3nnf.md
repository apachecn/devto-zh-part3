# 构建一次部署到多个环境🚀

> 原文：<https://dev.to/angular/build-once-deploy-to-multiple-environments-3nnf>

### [十二要素 app](https://12factor.net/)

如果我们看一下这样一个应用程序的描述，它说:

在现代，软件通常作为服务交付:称为 web 应用程序，或软件即服务。十二要素应用程序是一种用于构建软件即服务应用程序的方法，它可以:

*   为设置自动化使用声明式格式，以最小化新开发人员加入项目的时间和成本；
*   与底层操作系统有一个清晰的契约，在执行环境之间提供最大的可移植性；
*   适合在现代云平台上部署，消除了对服务器和系统管理的需求；
*   最大限度地减少开发和生产之间的差异，实现最大灵活性的连续部署；
*   并且可以在不对工具、架构或开发实践进行重大更改的情况下进行扩展。十二要素方法可以应用于任何编程语言编写的应用程序，并且使用任何支持服务(数据库、队列、内存缓存等)的组合。

顾名思义，一个有 12 个因素的应用程序有 12 个因素，在这篇文章中，我们将探讨第五个主题[构建、发布、运行](https://12factor.net/build-release-run)。如果我们看看这个阶段的描述，它说:

代码库通过三个阶段转换为(非开发)部署:

*   *构建阶段*是一个转换，它将代码 repo 转换成一个可执行的包，称为构建。在部署过程指定的提交时使用代码版本，构建阶段获取供应商依赖项并编译二进制文件和资产。
*   *发布阶段*获取构建阶段生成的构建，并将其与部署的当前配置相结合。最终的发布包含了构建和配置，并且可以在执行环境中立即执行。
*   *运行阶段*(也称为“运行时”)在执行环境中运行应用程序，针对选定的版本启动一些应用程序流程。代码成为一个构建，它与配置一起创建一个发布。

十二要素应用程序在构建、发布和运行阶段之间使用了严格的分离。例如，不可能在运行时对代码进行更改，因为没有办法将这些更改传播回构建阶段。

### 环境. ts

我相信这个文件在大多数情况下都被误用了，甚至可能是 Angular 应用程序中最被误用的文件。在我看来，Angular 对于这个文件的使用应该更加明确。可悲的是，我们不是例外，直到不久前我们才知道。我们的环境文件中有一些因环境而异的配置变量，例如 API 端点或使用第三方服务的令牌。这导致我们为每个环境 dev、test、staging、production1 和 production2 创建一个单独的`environment`文件。通过将我们的配置变量存储在`environment`文件中，当我们需要部署应用程序的新版本时，它迫使我们为每个环境构建应用程序。这既费时又容易出错。肖特说，这被认为是一种不好的做法。

作为探索的开始，我们将所有特定于环境的变量提取到一个配置文件中，这只是一个简单的 JSON 文件。这给我们留下了 2 个`environment`文件来定义它是否是生产版本。这些将是熟悉的，因为这些也是 Angular 提供的默认文件。

```
// environment.ts
export const environment = {
  production: false,
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// environment.prod.ts
export const environment = {
  production: true,
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们开发应用程序时，我们使用`environment.ts`文件，当我们在 CI 服务器上创建构建时，我们使用`environment.prod.ts`文件。Angular CLI 通过`angular.json`文件选择正确的文件。

### 部署应用程序

现在，我们应该如何使用正确的配置变量部署到环境中呢？为此，我们必须在部署期间覆盖配置文件。根据您使用的基础设施和工具，您可以用正确的配置文件替换配置文件，也可以在部署步骤中定义配置变量并用 set 变量覆盖每个配置变量。

我们现在有了一个构建和多个部署，但是我们仍然需要在应用程序中加载配置文件。这是我们努力争取的部分。

### 使用配置文件

我们认为我们不是第一个进行这种探索的人，所以我们上网来解决我们的问题。我们遇到的所有解决方案都是使用`APP_INITIALIZER` provider 来推迟 Angular 组件的引导过程，直到加载配置文件。

```
export function initConfig(configService: AppConfigService) {
  return () => configService.init() // in this function the config file is being loaded
}

@NgModule({
  providers: [
    {
      provide: APP_INITIALIZER,
      useFactory: initConfig,
      deps: [AppConfigService],
      multi: true,
    },
  ],
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

> 要了解更多信息和完整的实现示例，我强烈推荐[朱里·斯特鲁普弗洛纳](https://twitter.com/@juristr)的博客[对 Angular 应用的编译时和运行时配置](https://juristr.com/blog/2018/01/ng-app-runtime-config/)

起初，这似乎是一个很好的解决方案，在实施之后，它做了它应该做的事情，并且工作正常。我们很开心。

直到我们需要从外部组件和服务访问我们的配置的特定时刻，我们在启动时有依赖于配置文件的其他配置对象。我们需要创建一个配置对象来加载一个模块，例如我们需要在启动时加载`ApplicationInsightsModule`。我们试图连锁多个`APP_INITIALIZER`提供商，希望我们不必重新开始。不幸的是，我们发现配置在我们需要的时候没有被加载。我们尝试了多种方法来绕过它，而不改变我们与`APP_INITIALIZER`提供商的解决方案，但是没有任何成功。

所以我们回到绘图板，向 [AngularInDepth](https://twitter.com/AngularInDepth) 小组寻求帮助，在那里 [Joost Koehoorn](https://twitter.com/JoostK) 和 [Lars Gyrup Brink Nielsen](https://twitter.com/LayZeeDK) 提供了一些很好的指导。在一些帮助下，我们发现`platformBrowserDynamic`也接受提供者。

### platformBrowserDynamic

我们需要在应用程序启动之前加载配置变量，为了做到这一点，我们将启动过程向前推进了一步。在加载应用程序之前，我们必须首先加载配置文件。Angular 在`main.ts`文件中启动应用模块。为了解决我们的问题，我们不得不推迟这个过程，直到我们加载我们的配置变量。因为 [`platformBrowserDynamic`](https://angular.io/api/platform-browser-dynamic) 接受提供者，我们看到了在这里定义配置的机会。

下面的代码通过 [`fetch API`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 加载配置文件。这是一个承诺，所以我们可以肯定地知道，在我们引导应用程序模块之前，配置文件将被加载。这里使用了自我声明的`APP_CONFIG`标记来存储配置变量。

```
fetch('/assets/config.json')
  .then(response => response.json())
  .then(config => {
    if (environment.production) {
      enableProdMode()
    }

    platformBrowserDynamic([{ provide: APP_CONFIG, useValue: config }])
      .bootstrapModule(AppModule)
      .catch(err => console.error(err))
  }) 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 APP_CONFIG

现在我们更进了一步，我们已经在引导应用程序之前加载了配置，但是我们仍然需要使用它。作为我们任务中的最后一个障碍，我们必须创建`ApplicationInsightsModule`的配置。因为我们知道配置文件是在加载`AppModule`时加载的，所以我们现在可以访问配置文件并提供应用洞察配置。重用`APP_CONFIG`配置也很好，但是为了使配置对模块来说更有范围和可维护性，我们将大的配置分割成更小的块。

```
@NgModule({
  providers: [
    {
      provide: APP_INSIGHTS_CONFIG,
      useFactory: (config: AppConfig) => config.applicationInsights,
      deps: [APP_CONFIG],
    },
  ],
  bootstrap: [AppComponent],
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

### 包装完毕

我们现在完全符合十二因素应用程序的第五个因素，即构建、发布和运行。这意味着我们现在可以一次性构建我们的应用程序，并将其部署到我们拥有的每个环境中。在部署到环境的过程中，我们必须部署正确版本的配置文件。

拥有这种设置的好处是可以很容易地创建和使用切换功能。

TLDR:

*   不要将`environment.ts`文件用于特定环境的配置变量，创建一个定制的配置文件(如 JSON)
*   大多数时候使用`APP_INITIALIZER`方法将是这个问题的解决方案
*   使用`platformBrowserDynamic`方法以不同的方式解决了这个问题(当你不熟悉 bootstrap 过程时也更容易理解？)