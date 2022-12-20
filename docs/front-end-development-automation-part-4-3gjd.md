# 前端开发自动化。第四部分

> 原文：<https://dev.to/papaponmx/front-end-development-automation-part-4-3gjd>

## [T1】简介](#intro)

在本帖**中，我们将创建一个大系统**的一部分。我们将只关注运行性能测试。

**注意**:在这篇文章中，我假设你非常熟悉 Git、 [Github Webhooks](https://dev.to/papaponmx/a-gentle-explanation-of-github-webhooks-----d3e) 、 [NodeJS](https://nodejs.org) 和 Docker。

**NodeJS** :这里是[的回购，全部代码](https://github.com/papaponmx/performance-tests)。

## 问题

> ABC 公司有一个业务核心。它需要在移动设备上表现出色(我们将在后面定义什么是出色)。随着时间的推移，发布变得越来越频繁，迫切需要**确保所有的新发布都符合分配给它的`performance budget`**

## 解

作为开发人员，我们必须确保每个版本都处于可部署状态。对我们来说，可部署意味着所有的测试都通过了，并且新的特性在类似生产的环境中按预期工作。最重要的是，我们需要/希望有一种方法来获得关于我们下一个版本性能的即时反馈，因此建议的解决方案是:

> 创建一个每次发布新版本时都运行性能测试的系统。

## 依赖关系

*   [dotenv](https://www.npmjs.com/package/dotenv) 。一个零依赖模块，将环境变量从一个`.env`文件加载到`process.env`
*   [NodeJS](https://nodejs.org) 。
*   [木偶师](https://github.com/GoogleChrome/puppeteer)。无头 Chrome 节点 API。<！- * [ShellJS](https://github.com/shelljs/shelljs) 。Node.js API 之上的 Unix shell 命令- >
*   灯塔客户端。审核我们的应用程序并获取相关数据。

## 1。开发我们的分析工具

理想情况下，我们的下一个版本应该总是在上一个版本的基础上进行改进，或者保持在一个可接受的范围内。如果事情开始出现偏差，我们希望尽快获得相关数据。

我们将创建一个收集这些指标的函数:

*   JS 覆盖率& CSS 覆盖率。这个指标告诉我们使用了多少代码，加载了多少代码。
*   网络请求。列出页面加载过程中发出的网络请求。
*   速度指数。速度指数显示了网页内容可视填充的速度。

我们将运行所有 Lighthouse 可用的测试。事情应该以这样的顺序开始发生:

1.  **获取在测试/试运行/本地环境中运行的应用 URL**。这应该从配置文件中读取。
2.  **生成代码覆盖率**。现在，我们只是把它放在主页上。
3.  **运行灯塔审计**。
4.  过滤信息，并将其发送到可以保存的地方。这可能是使用文件系统或数据库保存的 JSON。随着应用程序的成熟，我们将在其他地方定义它

```
/**
 * puppeteerPerformance.js
*/

'use strict';
require('dotenv').config();

const chromeLauncher = require( 'lighthouse/chrome-launcher/chrome-launcher' );
const fs = require( 'fs' );
const lighthouse = require( 'lighthouse' );

const { APP_URL } = proccess.env;
// 1.

const flags = {
  output: 'json'
};

const launchChromeAndRunLighthouse = ( url, flags, config = null ) => {
  return chromeLauncher.launch()
    .then( chrome => {
      flags.port = chrome.port;
      return lighthouse( url, flags, config )
        .then( results =>
          chrome.kill()
          .then( () => results )
        );
    } );
}

// 2.
launchChromeAndRunLighthouse( APP_URL, flags )
// 3.
  .then( results => {
     fs.writeFile( './results.json', JSON.stringify(results), function ( err ) {
        if ( err ) {
          return console.log( err );
        }
        console.log( 'The file was saved!' );
      } );
}); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`results`变量可以被发送到数据库，并被存储用于历史记录比较。

在这种情况下，我们将把它保存为一个 JSON 文件

## 2。没有创建我们的 Github 动作

**Bummer** : *目前，Github 的行动是公开的，测试版，我在等待名单上。所以我们将使用 Github WebHook 来代替。如果你有资格批准我的请求，我会很乐意把它写在博客上。*

**NodeJS** :这里是[的回购，全部代码](https://github.com/papaponmx/performance-tests)。

我已经写了一篇关于如何设置 Github Webhooks 的博文。在这种情况下，我将在中创建一个 Heroku 应用程序，并使用该 url 进行 URL 设置。

现在我们需要创建一个简单的 Express 应用程序，在每次收到请求时运行测试。

我们将安装 express

```
yarn add express 
```

Enter fullscreen mode Exit fullscreen mode

```
 require('dotenv').config();

const { APP_STAGING_URL, APP_PRODUCTION_URL } = process.env;

const express = require('express');
const bodyParser = require('body-parser');
const fs = require('fs');
const { lighthousePerfAudits } = require('./audits/lighthousePerfAudits');

const app = express();
const flags = {
  output: 'json',
};

app.use(bodyParser.json());

app.post('/run', (req, res) => {
  // 1\. Check secret
  if (req.headers['x-hub-signature']) {
    /**
     * Secret is valid,
     * run tests and write json data
     * on both environments
     */
    lighthousePerfAudits(APP_PRODUCTION_URL, flags)
      .then((results) => {
        fs.writeFile('/production_results.json', JSON.stringify(results), (err) => {
          if (err) {
            return console.log(err);
          }
          console.log('The file was saved!');
        });
      });

    lighthousePerfAudits(APP_STAGING_URL, flags)
      .then((results) => {
        fs.writeFile('/staging_results.json', JSON.stringify(results), (err) => {
          if (err) {
            return console.log(err);
          }
          console.log('The file was saved!');
        });
      });

    // send response with 200
    res.status(200).send();
  } else {
    res.status(500).send();
  }
  // secret is not valid, return 500
});

app.listen(process.env.PORT || 8080, () => {
  console.log('App linstening on ', process.env.PORT || 8080);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

有了这个实例，我们就可以在每次将推送部署到存储库时运行脚本，并按顺序获取相关的指标。

该应用程序有一些失败之处，如:

*   这些数据应该保存在哪里？
*   有没有办法直接插到 Github 里？
*   我们如何可视化我们发布性能的历史数据？
*   狐狸说了什么？

我将在以后的文章中解决这些问题。我也在研究解决这个问题的开源 SaaS。如果你有更多的问题，可以在推特上联系我。

**NodeJS** :这里是[的回购，全部代码](https://github.com/papaponmx/performance-tests)。

干杯。

更多资源:

*   [之前关于 Gihub Webhooks 的帖子](https://dev.to/papaponmx/a-gentle-explanation-of-github-webhooks-----d3e)。
*   [本系列第三部](https://dev.to/papaponmx/front-end-development-automation-with-puppeteer-part-3-3pl6)。
*   [灯塔文档](https://developers.google.com/web/tools/lighthouse/)。