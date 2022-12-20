# VS 代码中的渐进式 Web 应用程序

> 原文：<https://dev.to/mayeedwin/progressive-web-apps-in-vs-code-646>

像专业人士一样随时随地用 VS 代码构建渐进式 Web 应用程序。我们的[工作箱支架](https://developers.google.com/web/tools/workbox/)也完全堆叠在一起。

### 关于 pwafire.org[的](https://pwafire.org)

我们的使命是让您的 **webdev** 环境完全符合渐进式 web 应用的最佳 Web 开发实践。

我们有一个官方的开发者指南[发布在这里](https://pwafire.org/developer/docs/how-to-use-vscode-pwa-in-vscode/)并且更新了这个扩展的最新更新。

### 设置 PWA 开发环境

我们有一个 **pwadev 提示** [回购在这里可用](https://github.com/mayeedwin/pwadev-tips)帮助指导你，如果你刚刚开始或想让你的设置清单做对。

### 设置应用清单并注册服务人员

编写**服务人员**和**应用清单**真的很快很专业！

| 命令 | 目的和键入位置 |
| --- | --- |
| `register service worker` | 在你的登陆页面注册服务人员说`index.html` |
| `add web manifest to pages` | 将`app.webmanifest`链接到`head tag`中您的页面 |
| `theme color` | 在你的页面 head 标签中添加主题颜色 |
| `manifest` | 在空的`app.webmanifest`文件中添加清单元数据 |

### 简单的基础服务工作者

| 命令 | 目的和键入位置 |
| --- | --- |
| `basic service worker` | 为简单的静态站点创建简单的服务工作者 |

### 在你的编辑器中键入命令用于 CDN 工作箱

| 命令 | 目的和键入位置 |
| --- | --- |
| `import workbox` | 在您的服务人员中导入工具箱 |
| `workbox.strategies` | 在服务人员中添加缓存策略 |
| `cache assets` | 通过从缓存中返回资源，使您的 JS 和 CSS ⚡更快，同时确保它们在后台更新，以便下次在您的工具箱`service-worker.js`文件中使用 |
| `cache images` | 从缓存中快速提供图像，同时确保不会无限期地缓存图像，从而消耗用户在 workbox `service-worker.js`文件中的存储空间 |
| `cache fonts` | 依靠谷歌字体或其他在用户访问你的网站后可以离线使用的字体 |
| `cache external orgins` | 缓存来自外部来源的外部资源，例如 boostrap css |
| `workbox set config` | 工具箱为调试或生产版本设置配置 |
| `workbox window` | 在您的登录页面中添加工具箱窗口，以便新服务人员更快地安装和更新 |
| `workbox page reload` | 为用户提供页面重新加载 |
| `workbox.cacheableResponse` | 基于数字状态代码、具有特定值的报头的存在或两者的组合的高速缓存；[在此了解更多信息](https://developers.google.com/web/tools/workbox/modules/workbox-cacheable-response) |

### 设置工具箱预缓存

| 命令 | 目的和键入位置 |
| --- | --- |
| `precache for npm` | say `sw-config.js`文件中的工具箱预缓存配置代码片段 |
| `workbox-cli devDependencies` | 在您的`package.json`文件中添加 workbox 客户端依赖项 |
| `workbox injectManifest` | 在工具箱`service-worker.js`文件中添加预缓存版本化的 URL 配置 |

**摘录:**当使用 bundler 创建你的服务工人时，你从 **npm** 安装你想要使用的 Workbox 包，然后
使用 import 语句直接引用你想要使用的 Workbox 模块。

在这里阅读更多

| 命令 | 目的和键入位置 |
| --- | --- |
| `import workbox npm modules` | 在您的工作箱`service-worker.js`文件中导入所有工作箱 npm 模块 |
| `workbox register route for npm` | 将路由与字符串匹配，字符串基本上是资源的完整缓存路由的块 |
| `workbox google analytics for npm` | 在底部的工具箱`service-worker.js`文件中添加离线谷歌分析。 |

### 高级渐进式网络应用功能

添加背景同步——在你的 javascript 源文件夹中，创建一个空的 **backgroundSync.js** 文件，并在其中键入下面的
关键字来添加[背景同步](https://pwafire.org/developer/docs/background-sync/)

| 命令 | 目的和键入位置 |
| --- | --- |
| `background sync` | 在新的`backgroundSync.js`文件中添加后台同步 |

### 要求

```
 "vscode": "^1.8.0" 
```

Enter fullscreen mode Exit fullscreen mode

### 安装

安装 vscode-pwa 。它在 Visual Studio Marketplace 中可用！运筹学

启动 VS 代码快速打开(Ctrl + P)，粘贴下面的命令，按回车键。

```
 ext install mayeedwin.vscode-pwa 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 PWA VS 代码进行演示

在您的浏览器中加载[预览](//.githimg/vscode-pwa-vid.gif)

### 已知问题

贝塔一切顺利！

### 发布说明

> 这个测试版允许你导入 Workbox 并像专业人士一样使用缓存策略！

最新发布的 vscode-pwa beta！

**享受😊 🐥**感谢您的关注。我们在推特上连线 [@mayeedwin1](https://twitter.com/mayeedwin1) :)