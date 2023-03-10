# 黑掉我们的 e2e 测试，让它们更有用

> 原文：<https://dev.to/kylessg/hacking-our-e2e-tests-to-make-them-more-useful-2gaj>

在这篇文章中，我们将概述一下我对[特色旗帜平台，子弹头列车](https://bullet-train.io/)进行 e2e 测试的经历。我将讨论在您的项目中添加 e2e 覆盖率的利与弊，以及我如何最终用一点聪明的 JavaScript 来提高它们的有用性。我还将提供一个在生产中使用的代码示例，供大家使用。

# 什么是端到端测试

与验证单个代码段正常工作的单元测试不同，端到端(e2e)测试是一种更高层次的项目运行方法，它测试针对 UI 的自动化交互是否按预期工作。

在 web 项目的情况下，我们编写代码来启动浏览器并测试实际的应用程序，就像我们是一个真实的用户一样，与元素进行交互，并确保应用程序在有好数据和坏数据的情况下都能像 e 一样工作。

有很多框架可以让这变得相当容易实现，在我的例子中，我发现 [Nightwatch](http://nightwatchjs.org/) 提供了一个非常简洁的 API，并且很容易使用。在这方面有很多选择，所以这主要取决于个人喜好。

## 好处

*   增加对应用程序的信心。我们可以编写世界上所有的单元测试，但是没有什么可以代替点击并验证所有的单元测试一起工作。
*   用最少的努力一次性测试许多组件。
*   非常适合回归。实际上，在我们的测试中运行应用程序会触及一切:UI、前端业务逻辑、与 API 的契约，甚至 API 本身。如果这些东西中的任何一个坏了，它就会被 E2E 感染。

## 无往不利

*   会引入恼人的误报。如果测试是以一种脆弱的方式编写的(例如寻找 li>span >。myClass>input)很容易通过改变 UI 来打破它们。
*   如果项目的 UI 不断变化，维护测试的成本会很高。单元测试通常可以不被触及，因为它们是孤立的，然而持续的 UI 变化可能需要维护和定期更新 e2e 测试。
*   有时测试失败提供了错误的根本原因是什么的模糊可见性。

最后这一点把我们带到了当前的主题。

# 更好的根本原因识别

为了实现容错和冗余，我们最近将[子弹头列车 API](https://bullet-train.io) 迁移到 AWS。迁移进行得很顺利，但是我们遇到了一个短暂的问题，用户无法创建组织。E2E 的测试人员马上开始对我们大喊大叫:

[![](img/c36420b7c91e776afe8986eeb5c7e909.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iO06blSa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/mg2OhA/download.png)

好的，很好。但这实际上意味着什么呢？谢天谢地，在这种情况下，我有一个很好的预感。由于我们无法注册，我们从未到达项目选择页面。我点击了一下网站，发现有一个 API 问题。这花了一点时间，但最终，我们修复了 API，我们的测试又开始通过了。虽然我们的测试发现了这个错误，但是我们花了相当长的时间来收集我们需要的所有信息来修复它。很明显，目前的错误报告水平还不够好，所以我们开始着手改进。

[![](img/153db25e90c37e9b51b5d8786b5acc04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JV6nDlvC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/niMG2A/download-1.png)

# 1。向 slack 发送 e2e 测试的截图

这部分很简单。任何 selenium 框架(即使它使用了 headless PhantomJS)都有能力截取浏览器当前状态的屏幕截图。幸运的是 Slack 有一个很好的 API 来上传图片到频道。

```
 const Slack = require('node-slack-upload');
const slack = new Slack(process.env.SLACK_TOKEN); 
const uri = path.join(__dirname, 'screenshot.png');
...
        browser.saveScreenshot(uri, ()=> {
            slack.uploadFile({
                file: fs.createReadStream(uri),
                filetype: 'auto',
                title: "Screenshot",
                channels: Process.env.E2E_SLACK_CHANNEL},
            }, function (err, data) {
              ...
            });
        }); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的基本出发点。Nightwatch 在之后提供了一个名为**的钩子，它在我们的测试完成后被调用(无论是由于错误还是成功完成)。我们只需要确保测试结束时浏览器不会自动关闭，这样我们就可以检查测试运行后还剩下什么。**

因为我们在 GitHub 上公开托管这个，所以我们确保总是将我们的敏感令牌隐藏在 env 变量后面！

```
 //nightwatch.conf:

"test_settings": {
        "default": {
            "end_session_on_fail": false,
            ...
        }
        ...
} 
```

Enter fullscreen mode Exit fullscreen mode

```
 //index.test.js:
module.exports = Object.assign(
    require('./test1.js'),
    require('./test2.js'),
    {
        after: (browser, done) => {
           uploadScreenshot(browser)
           ...

           server.kill('SIGINT');         
           browser.end();                 
           done();
       }
   }
) 
```

Enter fullscreen mode Exit fullscreen mode

瞧，当我们的测试结束时，我们的截图被发送到 slack！

[![](img/d9703447bc7632564a27f49f41fc8ea7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tgkszgwU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/nNkZNA/download-2.png)

# 2。报告 API 错误

这是事情变得有点聪明的地方。端到端测试的一个常见问题是对实际情况的可见性。毕竟，我们只是真正检查 DOM 元素的状态。API 或数据库级别的错误就完全不同了。

因此，为了报告“更深层次”的应用程序错误，我们的解决方案是让我们的站点将任何相关的日志记录信息写入 DOM，以便我们以后使用。

我们希望确保这仅在端到端测试运行时发生，否则，我们可能会意外地将敏感信息泄露给普通用户。

*告诉前端我们正在运行 e2e*T2】

```
 //package.json:
    "test": "cross-env E2E=true nightwatch ./tests/index.test.js",

``

We set the environment variable E2E to true so we can tell WebPack to build the application in E2E mode.

``

plugins: [
    new webpack.DefinePlugin({
        E2E: !!process.env.E2E
    }),
...
] 
```

Enter fullscreen mode Exit fullscreen mode

Webpack 的 DefinePlugin 允许我们为我们的站点设置要访问的全局变量。在这种情况下，窗口。E2E 现在将匹配我们的环境变量。

*将调试信息写入 DOM*

```
 //Handle all requests
if (E2E) {
    const payload = {
        url,
        options,
    };
    document.getElementById('e2e-request').innerText = JSON.stringify(payload);
}

fetch(url, options)

...
//Handle all responses
(response, err) => { // handling api errors
    req = fetch(url, options);
    if (E2E) {
        const error = {
            url: response.url,
            status: response.status,
            error: err,
        };
        document.getElementById('e2e-error').innerText = JSON.stringify(error);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们使用这个 E2E 变量将调试信息写入 DOM 元素。我们把它送到 slack。

[![](img/3147164adb817ff1f0baf10375b71997.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2LaQcEgW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.ibb.co/mpWaCA/download-3.png)

# 真实世界的例子

如果你想知道这在生产中实际上是如何使用的，这里是现在正在 gitlab 管道中运行的提交[https://github . com/SolidStateGroup/bullet-train-frontend/commit/4a 1d 41 B3 ea 103 a3 c2b 823803 D3 fa 273 ea E8 BD 49 f](https://github.com/SolidStateGroup/bullet-train-frontend/commit/4a1d41b3ea103a3c2b823803d3fa273eae8bd49f)。

黑客快乐！