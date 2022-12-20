# Synctractor -使用量角器测试 React/Vue 应用程序

> 原文：<https://dev.to/morcatko/synctractor-testing-react-vue-apps-with-protractor-fcg>

# 量角器

可能每个前端开发人员都听说过量角器。面向角度的端到端测试框架。还有很多其他类似的框架。然而量角器有一个很大的特点，当测试角度应用。它会自动等待您的网站准备就绪。它不会在加载过程中测试你的网站。量角器知道什么时候等待，什么时候测试。

量角器可以用于任何网站。不管是用 Angular，React，jQuery 还是静态 html 写的。要做到这一点，你必须禁用量角器配置文件
中的*同步*

```
onPrepare: function() {
    browser.ignoreSynchronization = true;
} 
```

这将禁止*等待*迫使量角器尽可能快地测试，甚至在你的页面完全加载之前…很可能它会开始失败。

解决方法是手动等待。

```
await browser.get("/login")
await $("#username").sendKeys("user");
await $("#password").sendKeys("password");
await $("#loginBUtton").click();
expect(await $("#message).getText()).toEqual("Welcome 'user'"); 
```

非常清晰且只包含用户动作和期望的代码必须通过等待、休眠和超时来扩展

```
await browser.get("/login")
// Wait for page load by checking presence of login button
await browser.wait(EC.presenceOf($(#loginButton)));
await $("#username").sendKeys("user");
await $("#password").sendKeys("password");
await $("#loginButton").click();
// Wait for login call & new page render
await browser.sleep(2000);
expect(await $("#message).getText()).toEqual("Welcome 'user'"); 
```

这个可以用，但是很脆弱。`browser.sleep`等待 2 秒钟。在此期间，用户很可能已经登录(也可能没有登录)。通常的“解决办法”是使用很长的睡眠时间，等待一些特定的页面元素，或者当你的应用程序准备就绪时注入页面的标记，或者类似的解决方法。

你可能想知道，它怎么可能对 Angular 如此简单，对其他框架却如此复杂。量角器实际上有两部分。一个是量角器本身，另一个是角框架。在运行 E2E 测试期间，这两个部分相互通信，确保一切正常。

# 同步牵引器

同步拖拉机来了。一个库，允许你使用非角度应用程序(react，vue)的量角器，并依赖于内置的同步和自动等待。它封装了异步调用(`fetch`，`setTimeout`)，并通过模拟角度部分在测试运行期间为量角器提供所需的信息。

它很容易使用

1.  安装它`npm i -save synctractor`
2.  将此添加到应用入口点的第一行

```
 import * as synctractor from 'synctractor'; 
    synctractor.init();
    synctractor.monitorFetch();
    synctractor.monitorTimeout((_, t) => t !== 11000); 
```

(请参见 [github](https://github.com/Morcatko/synctractor#settimeout) 了解神奇数字 11000 的解释)

就是这样。您可以从量角器配置文件中删除`browser.ignoreSynchronization = true;`,并从规格文件中删除所有睡眠。量角器会与你的应用程序进行通信，并在需要时等待。

[检查 synctractor repo 中的 React 和 Vue 示例](https://github.com/Morcatko/synctractor/tree/master/examples)

PS:目前只支持 fetch。AJAX 调用不受监控，量角器不会等待它。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【morcatko】](https://github.com/Morcatko)/[同步拖拉机](https://github.com/Morcatko/synctractor)

### 非角度应用程序的角度量角器同步(React，Vue，...)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 同步牵引机

非角度应用程序的角度量角器同步(React，Vue，...)使用这个库，你可以在你的量角器测试中去掉几乎所有的`browser.sleeps`和`browser.waits`，并且可靠地使用与量角器和 angular 相同的同步机制。

## 快速设置

1.  安装同步牵引器`npm i --save synctractor`
2.  从量角器配置中移除`ignoreSynchronization`,因为不再需要它
3.  添加这个作为你的应用入口点

    ```
    import * as synctractor from 'synctractor'
    synctractor.init()
    synctractor.monitorFetch();
    synctractor.monitorTimeout((_, t) => t !== 11000); 
    ```

    的第一行(参见下面的`setTimeout`细节来解释这个神奇的数字)

## 手控式

有一种自动模式(`synctractor.monitorXXX()`)，您可以在应用程序入口点设置 synctractor，仅此而已；还有一种手动模式，您只需初始化 synctractor，但必须更新所有代码的调用。在自动模式下。您可以通过`synctractor.nativeXXX()`接听未被监听的电话

1.  …

</article>

[View on GitHub](https://github.com/Morcatko/synctractor)