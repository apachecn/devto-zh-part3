# 我需要浏览器自动化来进行测试，所以我显然编写了自己的工具

> 原文：<https://dev.to/angrykoala/i-needed-browser-automation-for-testing-so-i-obviously-wrote-my-own-tool-1939>

大约一年前，我决定开始使用浏览器自动化工具为我的项目前端编写一些测试。虽然某种程度的测试可以通过编写单元测试、模拟和 jsdom 来实现，但最终，这些测试无法反映真实的交互并正确地检测错误。

因为这些测试将由我和其他开发人员编写(没有 QA 团队可用！).我需要测试尽可能简单地编写和维护，以避免浪费太多的开发时间，并且(更重要的是)避免开发人员(和我自己)由于懒惰而停止编写测试。

考虑到这一点，我开始为我的案例寻找正确的浏览器自动化工具，我优先考虑的是编写测试的简单性。在这个过程中，我尝试了几种工具，有些情况下用了几个星期:

*   [硒](https://www.seleniumhq.org)
*   [Zombie.js](http://zombie.js.org) -已弃用
*   [幻像](http://phantomjs.org) -已弃用
*   [nightman . js](http://www.nightmarejs.org)-不主动维护
*   [木偶师](https://github.com/GoogleChrome/puppeteer)

对我来说，所有这些工具(以及其他我一直在尝试的工具)要么太难设置，要么很少支持编写测试，要么依赖于外部(通常是付费的)服务。

因此，正如任何正常的开发人员都会做的那样，我决定开发自己的工具。当然，我不打算从头开始构建一个无头浏览器，在测试了上面提到的几个之后，我决定使用 Puppeteer 作为我自己工具的基础。它可靠、强大、易于安装和维护。因此，我开始构建 [wendigo](https://github.com/angrykoala/wendigo) ，一个基于 Puppeteer 的完整包装器，以使测试更容易，并根据需要添加功能。

[![Wendigo Logo](img/c34132ac648ba4981adc182e18c1ae25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j-6LS-T4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/02ftg5n082s4911jmw4q.png)

在我空闲时间进行了几周的开发后，努力开始有了回报。在我公司的几个项目中，集成和 E2E 测试开始变得更加清晰。添加内置断言方法大大简化了测试。从这样一个用木偶师写的测试开始:

```
// Test a modal text with Puppeteer
await page.click(".my-btn");
await page.waitForSelector("#my-modal")
const modalText = await page.evaluate(() => {
    const modalElement = document.querySelector("#my-modal");
    return modalElement.textContent;
})
assert.strictEqual(modalText, "Button Clicked"); 
```

对此:

```
// Test a modal text with Wendigo
await browser.click(".my-btn");
await browser.waitFor("#my-modal");
await browser.assert.text("#my-modal", "Button Clicked"); 
```

测试越复杂，差异越大。

经过一年多的开发。温迪哥不仅已经提供了简单的内置断言，还提供了大量的特性:

*   简单设置(`npm install --save-dev wendigo`)
*   复杂查询如`elementFromPoint(x,y)`、`findByTextContaining(text)`。
*   大多数常见场景的单行断言。
*   本地存储和 Cookies 接口和断言。
*   请求模拟。允许模拟服务器错误、第三方请求等。
*   插件系统可以根据需要添加额外的模块或你自己的方法。
*   它可以在任何基于 Node.js 的框架、测试工具链或 Docker 和 CI 中工作。
*   完全访问底层的木偶方法。

这些已被证明是快速可靠测试的有效特征。当然，用温迪哥代替其他替代品也有一些缺点:

*   仅支持 Chrome。
*   温迪哥提供的抽象层在某些情况下可能会导致意想不到的行为。
*   运行和编写测试所需的 Node.js。

温迪哥用[摩卡](https://mochajs.org)进行测试的例子如下:

```
 const assert = require('assert');
const Wendigo = require('wendigo');

describe("My Tests", function() {
    this.timeout(5000); // Recommended for CI or slower machines
    let browser;

    beforeEach(async() => {
        browser = await Wendigo.createBrowser();
    });

    afterEach(async() => {
        await browser.close();
    });

    after(async() => {
        // After all tests finished, makes sure all browsers are closed
        await Wendigo.stop(); 
    });

    it("Page Title", async() => {
        await browser.open("http://localhost");
        await browser.assert.text("h1#main-title", "My Webpage");
        await browser.assert.title("My Webpage");
    });

    it("Open Menu", async() => {
        await browser.open("http://localhost");
        await browser.assert.not.visible(".menu");
        await browser.click(".btn.open-menu");
        await browser.assert.visible(".menu");
    });

    it("Initial Request To API Fails Gracefully", async() => {
        browser.requests.mock("http://localhost/api", {
          status: 500
        });
        await browser.open("http://localhost", {
            clearRequestMocks: false // Keeps the mock created before
        });

        // Show red text with error message
        await browser.assert.text(".error-message", "Error loading the API");
        await browser.assert.style(".error-message", "color", "red");
    });
}); 
```

这个工具在 GitHub 上是完全开源的，经过测试和记录，目前仍在积极开发中。目前，我正试图获得关于改进、错误或功能的反馈，以帮助其他开发人员进行前端和 E2E 测试。

## ![](img/375dfcc32199b4dedf2b526645c27ff7.png) [树袋熊](https://github.com/angrykoala) / [雪怪](https://github.com/angrykoala/wendigo)

### 前端自动化测试的合适怪物

<article class="markdown-body entry-content p-5" itemprop="text">

# 温迪哥

[![](img/87dab57baf5e3ab851cfdffd87537441.png)](https://user-images.githubusercontent.com/5960567/54274028-6641ca80-4587-11e9-9e92-f27daa2e3910.png)

*by @ angrykoala*
[![npm](img/402ed11dfbc33d63fbb087c525006ec0.png)](https://www.npmjs.com/package/wendigo)[![Travis (.org)](img/017657976a8678d21ce2a12ba4a60162.png)](https://travis-ci.org/angrykoala/wendigo)[![Gitlab pipeline status](img/9747e4b53a4d827a0b9e764e335026e1.png)](https://gitlab.com/angrykoala/wendigo/pipelines)

> 前端自动化测试的合适怪物

**wendigo**(*/wɛndɪɡo/*)是[木偶师](https://github.com/GoogleChrome/puppeteer)的包装器，目的是让自动化测试更容易。用`npm install --save-dev wendigo`安装。

考虑以下使用木偶师的测试示例:

```
await page.click(".my-btn")
await page.waitForSelector("#my-modal")
const modalText = await page.evaluate(() => {
    const modalElement = document.querySelector("#my-modal")
    return modalElement.textContent
})
assert.strictEqual(modalText, "Button Clicked");
```

使用温迪哥，同样的测试可以写成这样:

```
await browser.click(".my-btn");
await browser.waitFor("#my-modal");
await browser.assert.text("#my-modal", "Button Clicked");
```

> 推荐节点 10.15.1 LTS 或更高版本

**内容**

*   [得到](https://raw.githubusercontent.com/angrykoala/wendigo/master/#getting-started) …

</article>

[View on GitHub](https://github.com/angrykoala/wendigo)