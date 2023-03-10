# 我使用 Netlify Dev 几个小时后的体验

> 原文：<https://dev.to/owenconti/my-experience-with-netlify-dev-after-a-couple-of-hours-of-using-it-459k>

注:这是我博客上的一篇交叉文章。在这里找到原文:[https://Owen conti . com/devo PS/my-experience-with-netlify-dev-after-a-course-through-that-use-it/](https://owenconti.com/devops/my-experience-with-netlify-dev-after-a-couple-hours-using-it/)

[Netlify Dev](https://www.netlify.com/products/dev/) 于 2019 年 4 月 9 日上午公布。

简而言之，它允许您在本地机器上运行 Netlify 的环境(构建、静态托管、函数、重定向等)。一旦运行，它还允许你打开一个通往公共互联网的隧道来访问你的项目*(想想 [ngrok](https://ngrok.com/) )* 。

关于它的特性和工作原理的更多信息，我推荐阅读 GitHub 知识库自述:[https://github.com/netlify/netlify-dev-plugin](https://github.com/netlify/netlify-dev-plugin)。

Netlify Dev 试图自动检测您拥有哪种类型的前端项目，并运行相应的命令来为您启动 Dev 服务器。你可以在这里找到它可以自动检测的项目列表:[https://github . com/netlify/netlify-dev-plugin/tree/master/src/detectors](https://github.com/netlify/netlify-dev-plugin/tree/master/src/detectors)。

## 自定义命令

如果您正在运行一个没有在上面的链接中列出的项目，您可以通过`netlify.toml`配置文件告诉 Netlify 运行什么命令。例如，这个博客是用 NextJS 构建的，但是我在 NextJS 周围运行了一个叫做 [Nextein](https://github.com/elmasse/nextein) 的包装器。因为我必须为我的开发服务器运行 Nextein 而不是 NextJS，所以我需要告诉 Netlify Dev:

```
[dev]
  publish = "out"
  port = 3000
  command = "npm start" 
```

Enter fullscreen mode Exit fullscreen mode

*   `[dev]`块告诉 Netlify 通过`netlify dev`命令使用这些设置
*   `publish`值告诉 Netlify 哪个目录有我们的`_redirects`文件
*   `port`值告诉 Netlify 我们的开发服务器在哪个端口上运行
*   `command`值告诉 Netlify 运行哪个命令来启动我们的开发服务器*(仅在运行 Netlify 尚未检测到的项目时需要)*

## 环境变量

这是我在尝试使用 Netlify Dev 时遇到最多麻烦的地方。

在我的一个项目中，我有几个环境变量需要在我的生产部署和我的本地环境之间有所不同。例如，我在 OAuth 认证流程中传递给 Twitter 的回调 URL。

在生产中，我通过 Netlify UI 中的“构建环境变量”部分来设置这些变量。在开发中，我使用 [dotenv](https://github.com/motdotla/dotenv) 包和一个`.env`文件(被 Git 忽略)来本地设置环境变量。

在 Netlify Dev 发布之前，我使用 [netlify-lambda](https://github.com/netlify/netlify-lambda) 包在本地运行我的函数。有了这个设置，我的环境变量工作得非常好。

然而，在切换到`netlify dev`并运行`netlify link`命令将我的本地项目链接到 Netlify 中的项目*(链接项目允许 Netlify 将“生产”环境变量从 Netlify 下载到您的本地机器以与`netlify dev`*一起使用)之后，我的本地`.env`覆盖不再工作。似乎 Netlify Dev 正在阻止`dotenv`包覆盖`process.env`值。

我[在他们的 GitHub 库的一个相关问题上留下了一个评论](https://github.com/netlify/netlify-dev-plugin/issues/60#issuecomment-481497474),所以希望他们能给我回复。

## 总结

总之，我认为 Netlify Dev 是一个伟大的产品，当开发人员利用 Netlify 的完整产品套件时，它将简化开发人员的体验。如果开发者只是使用 Netlify 的静态托管部分，我觉得 Netlify Dev 提供的价值很小。但是一旦你开始使用 Netlify 的功能或特性，Netlify Dev 就完全有意义了。

然而，我确实希望他们尽快解决环境变量的问题。