# 在 Angular 中伪造后端服务器

> 原文：<https://dev.to/nishugoel/faking-a-back-end-server-in-angular-2m90>

在这篇博文中，我们将看到如何在不使用真实服务器的情况下处理数据。这意味着不必使用数据所在的服务器，也不必使用$http 服务或请求 API，我们就可以使用数据并执行 CRUD 操作。

等等，什么是 CRUD 操作？在计算机应用程序中，执行 CRUD 操作意味着在服务器上或从任何地方创建、读取、更新和删除数据。任何处理数据的 Angular 应用程序都必须能够执行这四个基本操作，才能读取和写入数据。

[https://thepractical dev . S3 . Amazon AWS . com/I/o 969 xfqygu 4 tknhib 2 up . JPEG](https://thepracticaldev.s3.amazonaws.com/i/o969xfqygu4tknhib2up.jpeg)

回到这篇博文的重点，我们将看到如何使用 angular 内存 web API 来处理数据，而不必使用 http get 或 post 选项或通过一些$http 服务从真实的服务器访问数据。这意味着，我们可以伪造一个后端服务器，并使用该应用程序进行测试，就像我们在本地仿真器上所做的那样。

Angular in-memory web API 帮助我们使用数据进行测试。它发出一个 HTTP 请求，并从一个数据存储中获取数据，在这个数据存储中，我们可以通过一个远程 API 获得所有的数据，而不用去真正的服务器。为了使用它，我们需要将它作为一个依赖项安装在我们的应用程序中。

要从 CLI 安装 angular 内存 web API，请使用以下命令:

npm 安装 angular-in-memory-we b-API-save-dev

让我们来分析一下。在这个命令中，我们使用 npm install 安装名为 angular in-memory web api 的依赖项。确保您的计算机中安装了 npm 和 nodejs。接下来- -save-dev 用于保存我们刚刚为开发目的安装的依赖项。

现在，我们已经在 angular 应用程序中启动并运行了 Angular 内存中的 web api，我们可以快速地在 package.json 中检查这种依赖性在哪里得到了更新。

[https://thepractical dev . S3 . Amazon AWS . com/I/a3 gau UC 9 vzp 68284 uwck . png](https://thepracticaldev.s3.amazonaws.com/i/a3gauuc9vzp68284uwck.png)

给你！在 package.json 中的 devDependencies 下，您可以找到您安装的依赖项及其版本。devDependencies 是那些用于开发目的的依赖项。

现在，我们继续使用这个安装的 web 依赖来执行 CRUD 操作。

第一步是创建一个实体类，其中我们提到了类名及其属性。

[https://thepractical dev . S3 . Amazon AWS . com/I/bej fo 34 pw 5 kxj 5t 6a 08 . png](https://thepracticaldev.s3.amazonaws.com/i/bejfo34pw5xkxj5t6a08.png)

在我们的实体类准备好之后，我们将在一个新类中实现 InMemoryDbService 作为服务。请参见下一段代码。

[https://gist . github . com/NishuGoel/5076630 DBF 032 fc 4a 5883d 687277 fa0d](https://gist.github.com/NishuGoel/5076630dbf032fc4a5883d687277fa0d)

在这里，我们覆盖了 createDb()方法，并创建了一个实体用户的集合，该集合最终返回它的集合。我们在这里做的另一件事是导入 InMemoryDbService。要使用所有这些，我们需要在模块中注册它们，如下所示:

[https://thepractical dev . S3 . Amazon AWS . com/I/PKM 94 u 09 MP 16 gcw 9 LPO 6 . png](https://thepracticaldev.s3.amazonaws.com/i/pkm94u09mp16gcw9lpo6.png)

在我们将它注册到模块中之后，我们现在终于可以使用 angular 内存 web api 执行 CRUD 操作了。

为此，让我们创建一个服务并在其中执行 HTTP 操作。

[https://gist . github . com/NishuGoel/a 3a 01 C5 D1 ca 091 ee 5105d 293 ee 59 ceff](https://gist.github.com/NishuGoel/a3a01c5d1ca091ee5105d293ee59ceff)

现在，让我们试着理解这段代码。我们创建了这个服务 AppService。在这个服务中，我们在上面代码中第 12 行的帮助下，通过远程 API 访问数据

API URL = ' API/users '；下一步是注入服务 HttpClient，但在此之前，需要导入一些重要的文件。

从“@angular/common/http”导入{HttpClient，http headers }；
从‘rxjs’导入{Observable，throw error }；
从‘rxjs/operators’导入{ tap，catch error }；
第 14 行的代码设置变量 perfop 来执行 http 操作。现在，我们终于要读取通过这个远程调用接收到的数据了。

为此，我们采用如下所示的 getUsers()方法:

[https://gist . github . com/NishuGoel/06024 c 31888 caaa 799 fco da 3 f 595 b 13](https://gist.github.com/NishuGoel/06024c31888caaaa799fc0da3f595b13)

现在，在应用程序组件中，

[https://gist . github . com/NishuGoel/1f 3000 aa 17 f 274589 a 421d 44 b 9 c 616 f 1](https://gist.github.com/NishuGoel/1f3000aa17f274589a421d44b9c616f1)

我们调用 getUsers()方法并将数据返回给创建的变量 Users。此外，我们在 ngOnInit()内部调用 getUsers()方法，这是生命周期挂钩。

让我们最后输出我们在组件 html 上创建的内容。

[https://gist . github . com/NishuGoel/51b 9 F9 df 442d 20553d 46 c 468282796 E0](https://gist.github.com/NishuGoel/51b9f9df442d20553d46c468282796e0)

下面是我们如何使用 Angular 内存 web api 读取数据。

[https://thepractical dev . S3 . Amazon AWS . com/I/yraerxqayp 49 syx 8 kwfg . png](https://thepracticaldev.s3.amazonaws.com/i/yraerxqayp49syx8kwfg.png)