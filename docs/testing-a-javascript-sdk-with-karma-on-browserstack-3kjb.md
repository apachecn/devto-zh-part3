# 在 BrowserStack 上用 Karma 测试 JavaScript SDK

> 原文：<https://dev.to/dmerejkowsky/testing-a-javascript-sdk-with-karma-on-browserstack-3kjb>

从我作为 QA 工程师的六年经验开始，在不同的 web 浏览器中自动测试应用程序就一直是一个痛点。它需要处理多个操作系统和多个浏览器，占用了我们实际执行测试或其他 QA 任务的宝贵时间。

### 当地农业的问题

在 [Tanker](https://tanker.io) 我们开发了一个[开源](https://github.com/TankerHQ/sdk-js)隐私 SDK。我们为我们的持续集成基础设施感到自豪，但它也不是没有警告。我们开始使用 Karma 和 Chai 测试框架进行浏览器内和节点测试。浏览器内测试分别在 Edge 和 IE(Windows)、Safari(MAC OS)以及 Firefox 和 Chrome(Linux)上进行。你已经可以看到一些非常常见的配置——比如 Windows 上的 Chrome 没有被自动测试。我们也只使用了最新的操作系统版本，这不是真实世界的用户所做的。

我们还遇到了一些其他问题，这使我们考虑另一种解决方案:

*   我们遇到了 Edge 和 IE 的问题，迫使我们在每次构建之前运行一个脚本来删除缓存文件

*   Windows 上的另一个问题是您不能从服务中启动图形应用程序，因此我们使用手动启动的 Python 服务器作为解决方法

*   macOS 倾向于降低 Safari 的优先级，当浏览器上没有明显的活动(比如鼠标点击)时，它会非常慢，所以我们有一个脚本定期将 Safari 带到前台

*   当并行处理多个浏览器时，Karma 有点不稳定。对于开发者来说，在可接受的时间内运行多种浏览器需要一个大农场

有了这些信息，我们想尝试一下著名的 BrowserStack。

### 试用浏览器堆栈

第一步是创建一个 BrowserStack 帐户，允许您进行自动化操作。对于像我们这样的开源项目，这是免费的。您现在应该有一个用户名和一个访问令牌。之后，您可以将 karma-browser track-launcher 添加到您的项目中，作为一个开发依赖项。我们用纱线来做这个:

```
$ yarn add --dev karma-browserstrack-launcher 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候配置 karma 来使用这个启动器了。通常在 karma.conf.js 文件中完成:

```
 config.set({
      …
      customLaunchers: {
        ChromeWindows: {
          base: ‘BrowserStack’,
          browser: ‘Chrome’,
          browser_version: ‘71.0’,
          os: ‘Windows’,
          os_version: ‘10’,
        },
      },
    }) 
```

Enter fullscreen mode Exit fullscreen mode

这将添加一个自定义启动器来在 BrowserStack 服务器上运行您的测试！但为此你需要被认证。您可能不想在源代码中保留您的凭证，但是您可以配置您最喜欢的 CI/CD 工具，将用户名/访问键作为环境变量。在本地您可以做:

```
 $ export BROWSER_STACK_USERNAME=<YOUR_USER_NAME>
    $ export BROWSER_STACK_ACCESS_KEY=<YOUR_ACCESS_KEY> 
```

Enter fullscreen mode Exit fullscreen mode

您现在使用:
运行您的测试套件

```
 $ yarn karma --browsers ChromeWindows 
```

Enter fullscreen mode Exit fullscreen mode

你可以通过视频记录在[浏览器](https://automate.browserstack.com/)中看到正在发生的事情。

从现在开始，由您来添加与您的情况相关的任何配置。BrowserStack 有一个[工具](https://www.browserstack.com/automate/capabilities)来帮助你。

### 微调

如果您有多个项目，为了在 BrowserStack web 界面中对您的构建进行排序，您可以在 Karma 配置中添加一个项目名称。另一个有用的方法是:在远离办公室的数据中心，在移动设备上运行一个大的测试套件，很容易就要花 5 分钟以上，所以你可能想增加超时:

```
 config.set({
      …
      browserStack: {
        project: ‘<YOUR_PROJECT_NAME>’,
        timeout: 600,
      },
    }) 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 GitLab/GitHub 项目页面中添加 BrowserStack。设置它需要几个命令:

```
$ curl -u "<YOUR_USER_NAME>:<YOUR_ACCESS_KEY>" https://api.browserstack.com/automate/projects.json" 
```

Enter fullscreen mode Exit fullscreen mode

找到您的项目 id，然后:

```
$ curl -u "<YOUR_USER_NAME>:<YOUR_ACCESS_KEY> "https://api.browserstack.com/automate/projects/<YOUR_PROJECT_ID>/badge_key" 
```

Enter fullscreen mode Exit fullscreen mode

你会得到一把徽章钥匙。现在在你的自述文件中:

```
 [![BrowserStack Status](https://www.browserstack.com/automate/badge.svg?badge_key=<YOUR_BADGE_KEY>) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，为了在 BrowserStack web 界面中获得您的测试结果，您可以添加 BrowserStack 测试结果报告程序。例如，如果你已经使用了摩卡记者:

```
 config.set({
      …
      reporters: [‘mocha’, ‘BrowserStack’],
    }) 
```

Enter fullscreen mode Exit fullscreen mode

最终，我们遇到了一些浏览器无法连接到本地主机的问题(例如 Windows 上的 Firefox 65)。这个问题可以用
来解决

```
 config.set({
      …
      hostname: ‘bs-local.com’,
    }) 
```

Enter fullscreen mode Exit fullscreen mode

这其实是 iOS 上 Safari 测试需要的[。](https://www.browserstack.com/question/759)

现在，您可以将这些浏览器添加到您最喜欢的 CI/CD 工具中。我们将它添加到 [travis](https://github.com/TankerHQ/sdk-js/blob/master/.travis.yml) 中，它将在每个浏览器的独立容器中运行并行构建。

### BrowserStack 易于设置和使用…

正如你所看到的，我们没有为这个教程写任何实际的代码。我们只添加了几行配置。BrowserStack 非常容易设置和使用。

### ……但并不能解决我们所有的问题

然而，它并没有解决我在本文开头列出的所有问题。测试在 Safari 上运行顺利，只是有点慢，但这对我们没有影响，因为我们可以比在本地服务器场中运行更多的并行构建，并且不会影响我们的其他项目。

在 BrowserStack 中，Edge 真的很稳定。我们不必像在本地服务器场中那样执行任何清理，因为我们始终拥有干净的 Windows 实例。然而，这给我们带来了一个问题:我们使用本地存储，并且对于 Internet Explorer 使用了太多的本地存储。在我们的本地服务器场中，我们对机器进行了配置，以接受比我们的测试套件的默认存储量更多的存储量(这对我们的用户来说在现实生活中不是问题)。根据支持，这是不可能的。所以，事实上，我们不能在 IE 上使用自动化。

### 结论

BrowserStack 是一个很好的工具，它可以让我们快速地为更广泛的配置(手机、旧版本的浏览器/操作系统……)配置自动化测试。但不幸的是，对我们来说，这不是一个游戏规则的改变者。我们最初计划放弃我们当地的农场，但在某些问题得到解决之前，我们不能这样做:

*   我们不能在 IE 上运行自动化测试

*   测试速度较慢，因为…这不是我们的本地网络

也就是说，BrowserStack 是对我们本地服务器场测试的一个很好的补充，为我们提供了很大的灵活性。作为一个额外的收获，它向外界表明，我们测试各种配置，我们关心所有用户，而不仅仅是那些拥有最新设备的用户。到目前为止，我们测试了 [11 种不同的组合](https://github.com/TankerHQ/sdk-js#readme)。

* * *

PS:本文由 [Jérémy Tellaa](https://github.com/jeremad/) 原创，发表在[油轮的媒介](https://medium.com/tanker-blog/testing-a-javascript-sdk-with-karma-on-browserstack-a845d9e0ea8d)上。由于您自己可能不在 Medium 上，我们在这里复制了它，以便您有机会在您的通知源中看到它。