# 角度 PWAs 入门-第 1 部分

> 原文：<https://dev.to/prestonjlamb/getting-started-with-angular-pwas-part-1-31o6>

我已经在 web 开发领域积极工作了大约七年，在这段时间里，情况发生了很大的变化。当我开始的时候，jQuery 统治着前端。对于希望通过手机联系客户的公司来说，本地应用是最好的选择。有很多方法可以在 Android 应用程序和 iOS 应用程序之间共享代码，但它们不一定很棒。这些年来，事情发生了很大的变化。我最兴奋的事情之一是渐进式网络应用的出现和日益流行。

渐进式 web 应用程序是一种实现最佳实践的 web 应用程序，其功能类似于本机应用程序或桌面应用程序。反过来，它允许您编写和维护一个代码库，同时允许您的用户获得最佳体验。渐进式网络应用程序中有许多移动部件，这自然会导致一定程度的复杂性。但是角度的介入使一个复杂的任务变得比平常容易得多。本文将介绍如何将 Angular 应用程序转换成 PWA，以及 PWA 附带的一些数据缓存技术。这绝不是一个全面的教程，但它应该让你开始。在下一部分中，我将介绍如何显示更新提示，以及如何手动显示一个按钮来在您的设备上安装应用程序。

让我们从使用 CLI 生成一个新的角度应用程序开始:

## PWA 生成

```
ng new pwa-test 
```

Enter fullscreen mode Exit fullscreen mode

回答 CLI 提示后，您应该有一个默认的角度应用程序。现在，让我们把它变成一个 PWA。我们将通过运行以下命令来做到这一点:

```
ng add @angular/pwa --project pwa-test 
```

Enter fullscreen mode Exit fullscreen mode

显然，用您的项目名称替换`pwa-test`。此命令对您的项目执行以下操作:

1.  将 manifest.json 添加到项目的根目录中(带有关于 PWA 的信息)
2.  将 ngsw-config.json 添加到项目的根目录中(针对服务工作人员)
3.  添加一些默认图标，应用程序将在安装时使用
4.  更新 app.module.ts 文件以便为您注册服务人员
5.  更新 angular.json 文件以将 manifest.json 和 ngsw-config.json 文件添加到资产列表中
6.  更新 index.htmlto 以包含指向 manifest.json 的链接、主题颜色的 meta 标记和 noscript 标记

现在，从这里开始，您可以继续前进，创建 PWA 应用程序。根据文档，维修人员不使用`ng serve`命令。您可以在开发过程中使用它，但是不能用它来测试服务人员。我注意到的另一件事是，在`app.module.ts`文件中，服务工作者仅被设置为在生产模式下启用。我想你可以改变它，但这是默认设置。因此，使用这些默认值，您可以运行以下命令来查看您的应用程序:

```
ng build --prod && npx http-server -p 8080 -c-1 dist/pwa-test 
```

Enter fullscreen mode Exit fullscreen mode

> 您不必在这里使用`npx`来运行 http-server。您可以全局安装 http-server，或者使用您选择的不同服务器。

该命令使用生产构建的代码在端口 8080 上启动服务器。您可以在浏览器中打开该端口，当您查看 network 选项卡时，您会看到服务人员的配置文件 ngsw-config.json 弹出几次。服务人员正在运行！稍后我会展示一个例子。

## 资产组

现在我们有了一个可以工作的应用程序，让我们回过头来看看默认的 service worker 配置给了我们什么。默认情况下，服务工作器被设置为缓存两个资产组。基本上，您可以确定应该缓存哪些静态资产以及应该如何缓存它们。它定义的第一组如下:

```
{  "name":  "app"  //  give  each  asset  group  a  unique  name  "installMode":  "prefetch",  //  valid  selections  are  prefetch  and  lazy,  default  is  prefetch  "resources":  {  "files":  ["/favicon.ico",  "/index.html",  "/*.css",  "/*.js"]  //  files  that  should  be  cached  in  this  group  }  } 
```

Enter fullscreen mode Exit fullscreen mode

该资产组定义了应该缓存的第一组文件。基本上，它告诉服务人员获取 favicon、index.html 文件(因为这确实是 Angular 应用程序的 HTML 文件所需要的)、所有 CSS 文件和所有 JS 文件，并预取它们。预取意味着它们被立即下载和缓存。另一个没有在这里明确设置的选项是 updateMode。它决定了当新版本可用时如何更新这些缓存文件。它默认为安装模式。

默认的配置文件定义了第二个资产组，它获取属于应用程序一部分的其他静态资产。它们被延迟加载和缓存。

所有这些默认信息意味着，当您对代码进行更改、重新构建并重新加载应用程序时，您不会立即看到更改。我发现最好是在一个隐名标签中进行，但是至少要打开一个标签，这样你才不会丢失你的缓存。不管怎样，对应用程序中的 HTML 进行更改，并重新运行前面的构建和服务命令。当 http 服务器准备就绪时，打开网络选项卡并刷新页面。你会注意到旧版本的应用程序立即加载。然后，在 network 选项卡中，您会看到服务人员请求获取新文件。更新在后台进行！然后，当您刷新页面时，您将看到更新的应用程序。

我不知道你是怎么想的，但是当我第一次这么做的时候，我感到惊讶和兴奋。我简直不敢相信让服务人员设置缓存 HTML 文件是如此容易。如果你的应用是一个静态应用，比如一个博客或者类似的东西，那么你基本上就完成了。人们可以把你的应用程序安装到他们的主屏幕上，然后离线访问它。这所需的工作是如此之少...老实说，太不可思议了。但是大多数应用程序需要的不仅仅是静态文件。我们一直都在进行 HTTP 调用，所以让我们看看我们可以做些什么来缓存来自 HTTP 调用的数据。

## 数据组

默认情况下，配置为应用程序一部分的服务工作者不会缓存任何从 API 或类似的东西获取数据的调用。如果它是一个 JS 或 CSS 文件，或 index.html，或资产文件夹中的文件，或图像或字体文件或类似的东西，它将被缓存。但是为了让数据被缓存，我们需要创建一个数据组。 [Angular 文档对数据组](https://angular.io/guide/service-worker-config#datagroups)做了如下描述:

> 与资产资源不同，数据请求不随应用程序一起版本化。它们根据手动配置的策略进行缓存，这些策略对于 API 请求和其他数据依赖等情况更有用。

您可以定义一个数据组，以及如何缓存 API 请求等，方法是将如下所示的对象放入一个名为`dataGroups` :
的数组属性中

```
{  "name":  "api-name",  "urls":  ["https://my-api-url.com/**"],  "cacheConfig":  {  "strategy":  "freshness",  //  performance  (cache  first  only)  or  freshness  (network  first,  then  cache)  "maxSize":  100,  //  number  of  entries  or  responses  in  a  cache;  required  "maxAge":  "3d",  //  how  long  until  entries  are  considered  invalid  and  evicted  "timeout":  "10s"  //  how  long  to  wait  for  the  network  until  cache  is  used  }  } 
```

Enter fullscreen mode Exit fullscreen mode

使用这个配置对象，您可以确定如何缓存项目的 API。这样，当用户离线或连接不良时，他们仍然可以使用该应用程序。令人难以置信的是，这是多么容易再次设置和使用。在测试时，我决定使用星球大战 API(swapi.co)来获得一个星球大战人物的列表，我可以在应用程序中列出。我为它设置了一个数据组，当我有网络连接时，它调用 API。然后，在 Chrome 的网络标签中，我勾选了“离线”按钮，刷新了应用程序。HTTP 调用失败了，但是星球大战人员仍然出现，因为服务人员缓存了数据。

## 结论

此时，您应该有一个工作的 PWA。(如果您部署到 Netlify，您实际上可以在您的设备上安装应用程序。)文件和 API 调用将被缓存。该应用程序将离线工作。我们只需做很少的工作或配置，PWA 就准备好了。这是我为什么喜欢与 Angular 一起工作的另一个例子，也是 Angular 团队如何给了我们如此棒的工具。他们为我们做了很多繁重的工作，让我们可以轻松启动、运行并提高效率。

在即将到来的第二部分中，我将介绍如何显示更新提示，以及如何手动显示一个按钮来在您的手机上安装应用程序。请密切关注，并让我知道你对这篇文章的看法！