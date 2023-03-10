# 奥雷利亚文字

> 原文：<https://dev.to/tunaxor/aurelia-script--4i19>

去年 [Aurelia](https://aurelia.io/) 推出了一个可以放在脚本标签中的版本，你已经准备好了！它被命名为`aurelia-script`

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [奥雷利亚](https://github.com/aurelia) / [剧本](https://github.com/aurelia/script)

### Aurelia 的串联脚本标记就绪构建的家。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 奥雷利亚脚本

[![Join the chat at https://gitter.im/aurelia/discuss](img/80064cbae208bbac11d2f49a393a38ef.png)](https://gitter.im/aurelia/discuss?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

这个 repo 是 Aurelia 的串联脚本标记就绪构建的主页。

> 要了解关于 [Aurelia](http://www.aurelia.io/) 的最新消息，请访问并订阅[官方博客](http://blog.aurelia.io/)和[我们的电子邮件列表](http://eepurl.com/ces50j)。我们还邀请您[在 twitter 上关注我们](https://twitter.com/aureliaeffect)。如果您有问题，请[加入我们在 Gitter](https://gitter.im/aurelia/discuss) 上的社区，或者使用[堆栈溢出](http://stackoverflow.com/search?q=aurelia)。文档可以在我们的开发者中心找到[。](http://aurelia.io)

## Aurelia 脚本入门

### 简单的例子

在过去的好日子里，你在 html 中加入一个脚本标签，然后开始编写应用程序。奥雷利亚脚本是一种帮助你回到那个的方式，和奥雷利亚一起。简单补充一下:

```
  <script src='https://unpkg.com/aurelia-script@1.5.2'></script>
```

Enter fullscreen mode Exit fullscreen mode

进入您的主 html，您就可以开始了，如下所示:

```
   <script>
    au
      .start({
        debug: true,
        // root: 'app.js', // can be ommitted, default is
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/aurelia/script)

[https://codesandbox.io/embed/wnr6zxv6vl](https://codesandbox.io/embed/wnr6zxv6vl)

Aurelia 是基于约定的，你需要一个带`template tag`的`<name>.html`文件和一个带类的`<name>.js`文件，这样你就有了一个可以在任何地方使用的组件。事实上，Aurelia 是基于约定的，这意味着你可以使用与开始时相同的文件从*概念*到*原型*再到*特性*。

### 用电子设置

在玩这个的时候，我发现路由器使用动态导入在服务器的根目录中查找这些基于约定的文件，(`import()`)，所以这带来了两个问题，如果你想使用`aurelia-script`，你需要考虑它是为支持动态导入的浏览器准备的，另一个...从`file://`装货根本不行！

这很容易解决，只要运行一个小的静态服务器来提供你的内容，我选择了`koa.js`但是你可以很容易地使用一个静态服务器包

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[【angelmunoz】](https://github.com/AngelMunoz)/[辅助总管](https://github.com/AngelMunoz/AuExplorer)

### 一个 aurelia-script 电子混合应用程序的小型概念验证

<article class="markdown-body entry-content container-lg" itemprop="text">

# 探索者

一个 aurelia-script 电子混合应用程序的小型概念验证

```
pnpm install # or npm
pnpm start # or npm 
```

aurelia+electron =<g-emoji class="g-emoji" alias="heart" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2764.png">[![Screenshot](img/c144bfa8f8143dbc5140a9e07a888f2c.png)](https://github.com/AngelMunoz/AuExplorer./s2.png)</g-emoji>

这是一个小样本，完成得又快又脏，但对我来说，它显示了开始使用`aurelia-script`是多么容易，因为 aurelia 是基于约定的，如果你需要超越单一脚本，你会很容易做到

</article>

[View on GitHub](https://github.com/AngelMunoz/AuExplorer)

首先，我们在根目录下的`index.js`文件中的代码非常简单

```
// imports
const Koa = require('koa');
const serve = require('koa-static');
const { resolve } = require('path');
const { spawn } = require('child_process');

const app = new Koa();
let elcProcess;

// I'm not sure how secure is this at all
app.use(serve(`${__dirname}/node_modules`));
app.use(serve(`${__dirname}/renderer`));

// get the correct electron bin for your platform
const electron = process.platform === 'win32' ? resolve('node_modules/.bin', 'electron.cmd') : resolve('node_modules/.bin', 'electron');
const indexFile = resolve(__dirname, 'main/index.js');

// after we successfully start our server, then spawn the electron process
app.listen(45789, '127.0.0.1', () => {
  // you could also add argv arguments if you need
  // like [indexFile, ...process.argv.slice(2)]
  elcProcess = spawn(electron, [indexFile], {});
  // bind process monitoring if you need
  elcProcess.on('error', onElcError);
  elcProcess.stdout.on('data', onElcData);
  elcProcess.on('exit', onElcExit)
});

function onElcData(chunk) {/*...*/}

function onElcError(err) {/*...*/}

function onElcExit(code, signal) {/*...*/} 
```

Enter fullscreen mode Exit fullscreen mode

没什么特别的，只是你的隔天节点服务器。

在渲染器中，我们有 aurelia 应用程序，它的启动非常类似于我在上面的代码沙箱中展示给你的那个

```
<script src="/aurelia-script/dist/aurelia_router.umd.js"></script>
<script src="/localforage/dist/localforage.js"></script>
<script src="/dayjs/dayjs.min.js"></script>
<script>
  const aurelia = new au.Aurelia();
  aurelia
    .use
    .standardConfiguration()
    .developmentLogging();
  aurelia
    .start()
    .then(() => aurelia.setRoot(`app.js`, document.body))
    .catch(ex => {
      document.body.textContent = `Bootstrap error: ${ex}`;
    });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

你可能会想:`why do I need to manually call these libraries! iugh! it's 2019!`好吧，我只是尝试了一下作为概念验证，所以可能会有更好的方法来做这件事，也许是包裹？，或者您可以构建您的应用程序，然后将包放入其中，但是这个示例的主要思想是为了简单起见，只需将一些东西放在一起，然后就可以解决问题了！

需要考虑的另一件事是，我关闭了示例的`node integration`,并添加了一个预加载脚本来将`ipcRenderer`添加到窗口对象中，这样我就可以向`main`进程来回发送消息(稍后将详细介绍)。

让我们来看看我们的 app.js 文件

```
// simple class
export class App {
  constructor() {
    this.message = "Hello world";
    this.menuOpen = false;
    // bind process events to your class functions
    ipcRenderer.on('get:home:ans', this.setHomePath.bind(this));
  }

  // normal life cycle methods available!
  async activate() {
    const home = await localforage.getItem('home');
    if (!home) {
      // send a message to the main process
      ipcRenderer.send('get:home');
    }
  }

  // just like in any other aurelia app!
  configureRouter(config, router) {

    config.options.pushState = true;

    config.map([
      {
        route: ["", "home"],
        name: "home",
        // relative to the root of the server
        moduleId: "pages/home.js",
        title: "Home",
        nav: true
      },
      {
        route: "contact",
        name: "contact",
        moduleId: "pages/contact.js",
        title: "Contact",
        nav: true
      }
    ]);
    this.router = router;
  }

  toggleMenu() {/*...*/}

  async setHomePath(event, path) {/*...*/}
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能想知道，ipcRenderer 怎么会在那里？不需要，不需要导入，不需要任何东西，那是因为我们有一个小的预加载脚本来完成这项工作，我将在主目录中的`index.js`处显示`createWindow`函数，并省略其余的。

```
 function createWindow() {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    /*...*/
    webPreferences: {
      // call the preload script
      preload: `${__dirname}/preload.js`,
      // disable node integration for this window
      nodeIntegration: false
    }
    /*...*/
  })

  // and load the index.html of the app.
  mainWindow.loadURL('http://localhost:45789')
  /*...*/
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在我们的预加载脚本

```
const { ipcRenderer } = require('electron');
window.ipcRenderer = ipcRenderer;
window.PATH_DIVIDER = process.platform === 'win32' ? '\\' : '/'; 
```

Enter fullscreen mode Exit fullscreen mode

如果需要的话，你可以使用这个脚本来暴露节点内部，就像 [inAppPurchase API](https://electronjs.org/docs/api/in-app-purchase#inapppurchase)
一样，但是在我看来，你应该能够通过使用`ipc`进程间通信来完成大部分事情。

### 思想

这是一个很酷的实验和尝试，感觉真的很棒，只需拉一个脚本标签，就可以拥有 aurelia 及其惯例的所有功能！
例如，如果你想要 vue 的所有功能，最理想的是拥有`vue`文件，但是当你使用脚本标签时，这实际上是不可能的，你需要将你的 vue 组件改变成与`vue`文件不匹配的其他语法，我觉得目前这同样适用于其他框架。

#### 额外

如果您想知道您通常使用的 Dependecy 注入和可绑定装饰器如何适应`aurelia-script`,您可以查看这个示例

> [https://codesandbox.io/s/92vmpwnjjo](https://codesandbox.io/s/92vmpwnjjo)

请在下面分享你的想法和评论，祝你有一个美好的一周！