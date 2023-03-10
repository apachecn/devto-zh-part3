# 一个微小的项目，从开始到部署

> 原文：<https://dev.to/healeycodes/a-tiny-project-from-inception-to-deployment-784>

当我刚开始的时候，我从来不知道如何处理“项目”。很难一眼就理解项目的范围，以及步骤和注意事项。我想看看幕后。我们的工作方式各不相同，但在这篇文章中，我展示了当我对一些微小的事情产生灵感时，我是如何工作的。来看我挠痒痒。

早些时候，我在 MDN Web Docs 上寻找一些东西来引发本周文章的研究之旅，当时我发现没有随机页面链接。一个随机的页面功能对于网页技术的全面修订是很有用的，它允许你点击那些听起来有趣的东西。

[![The project's main/only page](img/3bb0932aabcf7f4257ae6a2a7816936d.png)](https://random-mdn-page.glitch.me/)

我开始了我的旅程，在 JavaScript 的[索引页面](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Index)上不停地工作。这也是我在工作中处理前端网站问题的方式，使用 DevTools 作为网站的草稿版本。

[![Looking at the table rows](img/4e7c147d145003312d054eebb5b780b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0tzkS_uu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oz17e2fm1rozxyodhhhp.png)

在 HTML 中有一个位于`document.querySelector("#wikiArticle > table > tbody")`的表体。在 Chrome 上，你可以右击一个元素，然后进入`Copy -> Copy JS Path`来找到它。每个条目使用三个表格行。我在页面链接和摘要后。让我们将行分成组，每组组成一个条目。

您可以在控制台中运行这些行。

```
// Index table
const indexTable = document.querySelector("#wikiArticle > table > tbody");

// There are three <tr>s to a row
const allRows = indexTable.querySelectorAll('tr');
const chunkRows = [];
for (var i = 0; i < allRows.length; i += 3) {
    chunkRows.push([allRows[i], allRows[i + 1], allRows[i + 2]]);
} 
```

我需要在某个地方存储索引页面数据的副本。根据每个请求抓取页面既浪费又缓慢。将它们存储为 JSON 是最有意义的，因为我将在 web 上发布它们。页面代码段应该有一个 link 属性和一个 text 属性。我也会在这里处理一些模板。

```
// Map these chunked rows into a row object
const pageEntries = [];
chunkRows.forEach(row => {
    const a = row[0].querySelector('a');
    const page = {
        link: `<a class="page-link "href="${a.href}">${a.innerText}</a>`,
        text: `<div class="page-text">${row[1].querySelector('td').innerText}</div>`
    }
    pageEntries.push(page);
}) 
```

我需要决定如何将它交付给用户。我考虑过 Chrome 扩展，但我想原型更快，所以我转向 Glitch，使用他们的 Express 模板。

我将抓取逻辑打包成一个异步函数，并使用[木偶师](https://github.com/GoogleChrome/puppeteer)在索引页面的上下文中运行这些命令。(在 Glitch 上，你必须使用`--no-sandbox`，这可能是一个安全风险。).在完成的项目中，可以用`node getNewPages.js`手动调用这个脚本来更新磁盘上的条目。(这非常适合 cron 的工作！).

从 Chrome DevTool hackery 到一些更有凝聚力的东西。

```
// getNewPages.js

const fs = require('fs');
const puppeteer = require('puppeteer');

(async () => {
    const browser = await puppeteer.launch({
      args: ['--no-sandbox'] // Required for Glitch
    });
    const page = await browser.newPage();
    await page.goto('https://developer.mozilla.org/en-US/docs/Web/JavaScript/Index');

    // Scan index table, gather links and summaries
    const pageEntries = await page.evaluate(() => {

        // Index table
        const indexTable = document.querySelector("#wikiArticle > table > tbody");

        // There are three <tr>s to a row
        const allRows = indexTable.querySelectorAll('tr');
        const chunkRows = [];
        for (var i = 0; i < allRows.length; i += 3) {
            chunkRows.push([allRows[i], allRows[i + 1], allRows[i + 2]]);
        }

        // Map these chunked rows into a row object
        const pageEntries = [];
        chunkRows.forEach(row => {
            const a = row[0].querySelector('a');
            const page = {
                link: `<a class="page-link "href="${a.href}">${a.innerText}</a>`,
                text: `<div class="page-text">${row[1].querySelector('td').innerText}</div>`
            }
            pageEntries.push(page);
        })
        return pageEntries;
    });

    // Save page objects to JSON on disk
    const pageJSON = JSON.stringify(pageEntries);
    fs.writeFile('./data/pages.json', pageJSON, function (err) {
        if (err) {
            return console.log(err);
        }
        console.log(`New pages saved! (JSON length: ${pageJSON.length})`);
    });

    await browser.close();
})(); 
```

您可以使用`require('./data/pages.json')`将 JSON 文件加载到 Node 中，它将我们所有的页面条目保存在内存中，以便更快地访问(这是可能的，因为它的大小固定在 300kb 左右)。我们 web 应用程序的其余部分是一个随机函数的包装器。

```
// server.js

// init project
const express = require('express');
const app = express();
app.use(express.static('public'));

const pages = require('./data/pages.json');
const rndPage = () => pages[Math.floor(Math.random() * pages.length)];

app.get('/', (req, res) => res.sendFile(__dirname + '/views/index.html'));

app.get('/rnd', (req, res) => res.send(rndPage()));

// listen for requests :)
const listener = app.listen(process.env.PORT, function() {
  console.log('Your app is listening on port ' + listener.address().port);
}); 
```

我们唯一需要的客户端 JavaScript 是一个用于更新页面链接和摘要的`fetch`调用。以及一个带有可爱摆动动画的按钮来翻阅条目。同一个片段出现两次的几率是 897 分之一，你会如何解决这个问题呢？请在评论中告诉我吧！

**何去何从:**MDN 文档(除了 JavaScript)上还有其他页面遵循相同的行模式，这意味着只需在我们的脚本中更改 URL 就可以抓取它们。

你可以[重新混合](https://random-mdn-page.glitch.me/)这个项目来玩代码的副本或者克隆这个回购:[Healey codes/random-MDN-page](https://github.com/healeycodes/random-mdn-page)。

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。