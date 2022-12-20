# 自动化 chrome 扩展的 UI 测试

> 原文：<https://dev.to/gokatz/automate-the-ui-testing-of-your-chrome-extension-52e1>

构建 chrome 扩展绝对是一个有趣的过程！Chrome 扩展为 web 开发者和用户打开了一扇全新的大门。然而，在某些方面，测试这些出色的扩展并不像测试任何传统的 web 应用程序那样简单。在这篇文章中，让我们一起来添加我们的第一个测试用例，确保我们的扩展最好。

## 为什么首先要自动化

手工测试过程是软件工程中令人厌烦的事情之一😆随着 Chrome 扩展的**新安装**、**扩展更新**、**权限更新**、**扩展降级/删除**等各个方面，这个过程变得更加棘手和无聊。在每个版本中，很容易忽略一些方面的测试。因此，自动化这些枯燥的东西可以确保我们的扩展在每一个版本中正常工作。

## 如何进行测试

我们将使用**木偶师**测试 chrome 扩展，并使用**摩卡**测试程序构建我们的测试。此外，我们还将看到如何使用 **CircleCI** 在您的 CI/CD 流程中自动化这个测试过程。您可以使用任何您喜欢的测试运行程序和 CI/CD 工具。

让我们先安装我们的依赖项，

```
yarn add puppeteer mocha -D 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
npm i puppeteer mocha --dev 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在木偶师的帮助下，通过模仿手工测试过程中的步骤来测试我们的 chrome 扩展。

*   打开 Chrome 浏览器
*   加载扩展的未打包版本(通过`chrome://extensions`页面开发模式)
*   打开我们的扩展弹出/索引页面
*   测试目标特性

让我们一个接一个地自动化这些步骤。为了更好地理解，请随时随地运行(`node test.js`)来测试我们在每一步构建的脚本。

## 步骤 1:以编程方式打开 Chrome

作为第一步，我们需要以编程方式控制 Chrome。这正是*木偶师*帮助我们的地方。根据文档，Puppeteer 是一个**节点库，它提供了一个高级 API 来控制无头(和完全非无头)Chrome** 。在我们的例子中，我们需要以**完整形式**启动 Chrome，因为扩展只能以完整形式加载。

```
// test.js

const puppeteer = require('puppeteer');

let browser = await puppeteer.launch({
  headless: false, // extension are allowed only in head-full mode
}); 
```

Enter fullscreen mode Exit fullscreen mode

在运行脚本(`node test.js`)时，chromium 构建将会以一个空白页面启动。杀死节点进程以关闭 Chromium 浏览器。

## 第二步:加载扩展

接下来，需要将我们的扩展加载到 chrome 中。可以使用木偶师给出的`--load-extension`标志将扩展加载到浏览器实例中。此外，我们需要禁用所有其他扩展，以防止任何不必要的噪音使用`--disable-extensions-except`标志。

```
// test.js

const extensionPath = <path-to-your-extension>; // For instance, 'dist'

const browser = await puppeteer.launch({
    headless: false, // extension are allowed only in the head-full mode
    args: [
        `--disable-extensions-except=${extensionPath}`,
        `--load-extension=${extensionPath}`
    ]
}); 
```

Enter fullscreen mode Exit fullscreen mode

运行这个脚本时，Chrome 实例将和你的扩展一起启动。您可以在工具栏菜单上找到您的扩展徽标。

## 第三步:转到扩展弹出页面

当我们点击工具栏菜单中的扩展图标时，将打开扩展弹出/索引页面。同样的页面可以使用`chrome-extension` URL 直接打开，以简化测试过程。一个正常的扩展页面 URL 会像`chrome-extension://qwertyasdfgzxcvbniuqwiugiqwdv/index.html`。这个 URL 可以被分解成，

*   扩展协议(`chrome-extension`)
*   扩展 ID ( `qwertyasdfgzxcvbniuqwiugiqwdv`)
*   弹出/索引页面路径(`index.html`)

为了访问页面，我们需要为我们的扩展构建这种 URL。这里未知的部分是**扩展 ID。**因此，我们需要知道 Chrome 生成的扩展的任意 ID。

### 了解你的分机 ID 的正确方法

Chrome 会在加载时给每个扩展分配一个唯一的扩展 ID。每次我们在新的 Chrome 实例上启动扩展时，这将是随机的。但是，我们可以按照[中提到的步骤设置一个特定于我们的扩展的稳定的扩展 ID，所以请回答](https://stackoverflow.com/questions/23873623/obtaining-chrome-extension-id-for-development/23877974#23877974)。这将是一个有点长的过程，但傻瓜证明。我们可以放心地依靠稳定的 ID 来测试我们的扩展，因为当使用 Puppeteer 在各种 Chrome 实例中启动时，ID 不会改变。

### 知道你的扩展 ID:后台脚本方式

然而，如果我们的扩展有后台脚本，那么这个过程就有点简单了。我们可以通过编程来检测扩展 ID。

当使用后台脚本时，一旦加载了扩展，Chrome 就会为后台脚本创建一个目标(*步骤 2* )。Chrome 管理的所有页面目标都可以通过启动的浏览器实例的`targets`方法访问。使用这些目标，我们可以在`title`属性(这将是我们在`manifest.json`中给出的扩展标题)的帮助下拉出我们特定的扩展目标。这个目标将包含 Chrome 在当前启动时分配的随机扩展 ID。

```
// test.js

// This wait time is for background script to boot.
// This is completely an arbitrary one.
const dummyPage = await browser.newPage();
await dummyPage.waitFor(2000); // arbitrary wait time.

const extensionName = <name-of-your-extension> // For instance, 'GreetMe'

const targets = await browser.targets();
const extensionTarget = targets.find(({ _targetInfo }) => {
    return _targetInfo.title === extensionName && _targetInfo.type === 'background_page';
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦您获取了您的扩展目标，我们就可以从目标 URL 中提取 ID。背景目标 url 示例如下所示，`chrome-extension://qwertyasdfgzxcvbniuqwiugiqwdv/background.html`。因此，提取将是这样的:

```
const extensionUrl = extensionTarget._targetInfo.url || '';
const [,, extensionID] = extensionUrl.split('/'); 
```

Enter fullscreen mode Exit fullscreen mode

我们成功地获得了我们的扩展 ID(通过任何一种方式)💪

### 途中扩展页面🚌

现在，让我们转到我们的扩展页面。为此，我们需要创建一个新的浏览器页面并加载适当的扩展弹出 URL。

```
 // test.js

// This is the page mentioned in `default_popup` key of `manifest.json`
const extensionPopupHtml = 'index.html'

const extensionPage = await browser.newPage();
await extensionPage.goto(`chrome-extension://${extensionID}/${extensionPopupHtml}`); 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，运行测试脚本将启动一个新的 Chrome 实例，并打开一个新页面，其中包含您的扩展 popup HTML 页面内容，就像一个普通的 web 页面一样。

## 第四步:测试目标特性

我们已经成功启动了我们的扩展页面。该是🖐的时候了

现在，我们在这里倒一下我们的 web app 测试知识。与每个 web 应用程序一样，端到端测试可以使用 DOM 查询和断言来完成。同样的道理也适用于此。我们扩展页面的 DOM 可以使用木偶师提供的 [`$` ( `querySelector` )](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pageselector) 和[`$$`(`querySelectorAll`)](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md#pageselector)API 进行查询。您可以使用您喜欢的断言库。在这个例子中，我使用了节点的本地 [`assert`](https://nodejs.org/api/assert.html) 包。

```
// test.js

const assert = require('assert');

const inputElement = await extensionPage.$('[data-test-input]');
assert.ok(inputElement, 'Input is not rendered'); 
```

Enter fullscreen mode Exit fullscreen mode

可以使用由木偶师提供的各种事件 API 在扩展页面上触发事件。

```
await extensionPage.type('[data-test-input]', 'Gokul Kathirvel');
await extensionPage.click('[data-test-greet-button]');
const greetMessage  = await extensionPage.$eval('#greetMsg', element => element.textContent)
assert.equal(greetMessage, 'Hello, Gokul Kathirvel!', 'Greeting message is not shown'); 
```

Enter fullscreen mode Exit fullscreen mode

**注:**木偶师得到了很多有用的[API](https://github.com/GoogleChrome/puppeteer/blob/master/docs/api.md)来控制和提取 Chrome 中有用的信息。

### 使用测试跑步者

为了以有意义的方式修补测试并获得良好的视觉反馈，我们可以使用测试运行程序。在这个例子中，我将演示如何使用 [`mocha`](https://mochajs.org/) 来构建我们的测试。

```
// test.js

describe('Home Page', async function() {
  it('Greet Message', async function() {
    const inputElement = await extensionPage.$('[data-test-input]');
    assert.ok(inputElement, 'Input is not rendered');

    await extensionPage.type('[data-test-input]', 'Gokul Kathirvel');
    await extensionPage.click('[data-test-greet-button]');

    const greetMessage  = await extensionPage.$eval('#greetMsg', element => element.textContent)
    assert.equal(greetMessage, 'Hello, Gokul Kathirvel!', 'Greeting message is not shown');
  })
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 将所有的碎片拼接起来

让我们将所有部分结合起来，为您的扩展创建一个完全自动化的测试套件。

```
// test.js

const puppeteer = require('puppeteer');
const assert = require('assert');

const extensionPath = 'src';
let extensionPage = null;
let browser = null;

describe('Extension UI Testing', function() {
  this.timeout(20000); // default is 2 seconds and that may not be enough to boot browsers and pages.
  before(async function() {
    await boot();
  });

  describe('Home Page', async function() {
    it('Greet Message', async function() {
      const inputElement = await extensionPage.$('[data-test-input]');
      assert.ok(inputElement, 'Input is not rendered');

      await extensionPage.type('[data-test-input]', 'Gokul Kathirvel');
      await extensionPage.click('[data-test-greet-button]');

      const greetMessage  = await extensionPage.$eval('#greetMsg', element => element.textContent)
      assert.equal(greetMessage, 'Hello, Gokul Kathirvel!', 'Greeting message is not shown');
    })
  });

  after(async function() {
    await browser.close();
  });
});

async function boot() {
  browser = await puppeteer.launch({
    headless: false, // extension are allowed only in head-full mode
    args: [
      `--disable-extensions-except=${extensionPath}`,
      `--load-extension=${extensionPath}`
    ]
  });

  const dummyPage = await browser.newPage();
  await dummyPage.waitFor(2000); // arbitrary wait time.

  const targets = await browser.targets();
  const extensionTarget = targets.find(({ _targetInfo }) => {
    return _targetInfo.title === 'GreetMe';
  });

  const extensionUrl = extensionTarget._targetInfo.url || '';
  const [,, extensionID] = extensionUrl.split('/');
  const extensionPopupHtml = 'index.html'

  extensionPage = await browser.newPage();
  await extensionPage.goto(`chrome-extension://${extensionID}/${extensionPopupHtml}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过调用`mocha`命令来运行这个脚本。

```
mocha test.js 
```

Enter fullscreen mode Exit fullscreen mode

让我们在`package.json`中创建一个 npm 脚本来映射`mocha`命令、

```
"scripts": {
  "test": "mocha test.js"
} 
```

Enter fullscreen mode Exit fullscreen mode

它将调用测试并在终端中输出测试用例状态。

```
$ yarn test
$ mocha test.js

  Extension UI Testing
    Home Page
      ✓ Greet Message (142ms)

  1 passing (5s) 
```

Enter fullscreen mode Exit fullscreen mode

恭喜，你走到了最后🤝

我们已经创建了测试扩展页面的第一个测试套件。是时候用 CI 溢出来弥补了。我在这个演示中使用了 **CircleCI** 。我们可以使用任何此类服务，如 **TravisCI** 、 **AppVeyor** 等。,

### 用 CI 接线

为 **CircleCI** ，`.circleci/config.yml`创建一个配置文件，并加载一些样板步骤。我们将使用一个名为`circleci/node:8.12.0-browsers`的图像，因为这个图像已经预装了 chrome，我们不需要安装任何进一步的依赖项。如果您正在使用任何其他服务，请使用预建的浏览器找到合适的图像。

```
version: 2
jobs:
  build:
    docker:
      - image: circleci/node:8.12.0-browsers

    working_directory: ~/repo

    steps:
      - checkout
      - restore_cache:
          keys:
          - v1-dependencies-{{ checksum "package.json" }}

          # fall back to using the latest cache if no exact match is found
          - v1-dependencies-

      # Install your dependencies
      - run: yarn install

      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}

      # build the extension if required
      # Run our test suite 
      - run: yarn test 
```

Enter fullscreen mode Exit fullscreen mode

呜呼...再次祝贺。我们刚刚成功地自动化了测试过程🔥🔥尝试自动化您现有的和未来的扩展的测试过程，并对您未来的版本保持冷静。示例扩展以及它们的(工作)测试已经托管在 [GitHub](https://github.com/gokatz/greet-me-extension) 中。如果需要帮助，可以参考源代码。

希望这篇文章对你有用。如果是这样，我在这篇[博客文章](https://dev.to/gokatz/automate-your-chrome-extension-deployment-in-minutes-48gb/)中写了关于在 CI/CD 流程中自动化 chrome 扩展部署的内容。检查您是否正在手动部署您的扩展。这可能也是实现自动化的时候了😉

[![gokatz](img/3a657c05e5c585bdb46e9cb6a6d3ec8b.png)](/gokatz) [## 几分钟内自动完成 chrome 扩展部署！

### gokul Kathirvel 3 月 27 日 18 时 5 分读取

#chrome #extensions #webdev #javascript](/gokatz/automate-your-chrome-extension-deployment-in-minutes-48gb)

今天到此为止。让我们下次带着其他令人兴奋的东西见面吧。再见了。如果你有任何反馈或建议，请在评论中发表。我很乐意为此努力。