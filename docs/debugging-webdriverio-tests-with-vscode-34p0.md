# 使用 VSCode 调试 webdriverio 测试

> 原文：<https://dev.to/intricatecloud/debugging-webdriverio-tests-with-vscode-34p0>

[https://www.youtube.com/embed/66E7y12GQaE](https://www.youtube.com/embed/66E7y12GQaE)

编辑-如果你有兴趣看我直播这篇文章-看看我最新的 Youtube 视频！

当你试图找出为什么你的测试有时点击了错误的元素或者就是不能工作时，用 webdriverio 调试测试可能会令人沮丧。我是否使用了正确的选择器？它实际上点击的是哪个元素？你可以做三件事来帮助你向下钻取:

*   向测试中添加许多 console.log 语句
    *   使用调试器一次一行地通过测试
    *   使用 webdriverio 的`browser.debug()`获得与浏览器的交互式 js 会话
    *   虽然这似乎是显而易见的选择，但是使用 browser.debug 有它自己的限制，我在这里描述了这些限制

我搜索并找到了这篇让 [webdriverio 测试在 vscode](http://blog.likewise.org/2017/02/debugging-a-javascript-webdriverio-project-in-vscode/) 中运行的帖子，以帮助我一行一行地通过测试文件。那个帖子用的是老版本的 node，新版本的 node (v8.11.x+)抛出如下错误:

```
node --debug=127.0.0.1:5859

(node:29011) [DEP0062] DeprecationWarning: `node --debug` and `node --debug-brk` are invalid. Please use `node --inspect` or `node --inspect-brk` instead. 
```

Enter fullscreen mode Exit fullscreen mode

有两个部分可以让它与新版本的 node 一起工作:VSCode 配置和 webdriverio 配置

## VSCode 配置

[参见此处创建 VSCode 启动配置](https://code.visualstudio.com/docs/editor/debugging#_launch-configurations)。添加这个文件将允许您[运行 VSCode 调试器](https://code.visualstudio.com/docs/editor/debugging)并逐行调试您的代码。

这是一份工作。vscode/launch.json 文件，您可以根据自己的需要使用和修改该文件

```
{
  "type": "node",
  "request": "launch",
  "name": "Run in debug",
  "port": 5859,
  "timeout": 60000,
  "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/wdio",
  "cwd": "${workspaceRoot}",
  "console": "integratedTerminal",
  "args": [
      "--spec", "main.js"
  // "--spec main.js" will be passed to your executable as
  // "wdio '--spec main.js'" (which isn't what you want)
  ],
  "env": {
      "DEBUG": "1" 
      // use an environment variable to be able
      // to toggle debug mode on and off
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个配置将运行您的`runtimeExecutable`，通过定义`"port"`变量，它将尝试连接到端口 5859。一旦成功建立了连接，您就可以设置断点并逐步完成测试。

## web drivero 配置

在 webdriverio 端，我们需要告诉它在端口 5859 上监听来自调试器的连接。

在您的`wdio.conf.js`文件中:

```
exports.config = {
  debug: true,
  execArgv: ['--inspect-brk=127.0.0.1:5859],

  // other wdio configuration
  specs: ['some/specs/here'],
  suites: {
    ....
  }
  ....
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码片段将启动 webdriverio，并开始侦听来自 127.0.0.1:5859 上的调试器的连接(这是在 VSCode 配置中完成的)。程序将在这一点停止，等待调试器连接，如果没有连接，命令将失败。

一旦运行成功，您应该会看到这种类型的输出

```
/Users/dperez/workspace/wdio/node_modules/.bin/wdio "--spec" "main.js"

Debugger listening on ws://127.0.0.1:5859/9698ad4c-8d7d-447f-a259-1c566cd511d6
Debugger attached. 
```

Enter fullscreen mode Exit fullscreen mode

您将看到程序在这一点上暂停，直到建立连接。如果您从未看到“附加调试器”，这意味着 VSCode 没有连接到您的程序，因此您不能设置断点和调试。

如果您将此作为 CI 流程的一部分运行(Gitlab/Jenkins ),您可以使调试模式可配置。您可以在您的。vscode/launch.json 配置。

```
...
"console": "integratedTerminal",
"env": {
  "DEBUG": 1
} 
```

Enter fullscreen mode Exit fullscreen mode

这将通过使用:
来运行带有环境变量设置程序

```
DEBUG=1 /Users/dperez/workspace/wdio/node_modules/.bin/wdio "--spec" "main.js" 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的 wdio.conf.js 文件:

```
exports.config = {
  debug: process.env.DEBUG === '1',
  execArgv: process.env.DEBUG === '1' ? ['--inspect-brk=127.0.0.1:5859'] : []
  ...
  // remaining wdio options
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当您在 VSCode(设置了环境变量的地方)中运行程序时，程序将只等待附加到调试器，而在其他任何地方，如果没有它，程序都将正常运行。

## 包装完毕

如果您需要单步调试您的程序并观察程序是如何执行的，请将这些代码片段添加到您的配置中。这肯定比在代码中编写大量的 console.logs 要好。

*   有兴趣使用 webdriverio 的`browser.debug()`模式？[查看我关于使用 wdio 的调试模式玩浏览器](https://www.intricatecloud.io/2018/11/webdriverio-tips-using-browser-debug-to-help-debug-your-tests/)的帖子。
*   如果你在这里是因为你试图解决可怕的[元素在点上不可点击的错误信息，请在这里查看这个帖子](https://www.intricatecloud.io/2018/11/webdriverio-tips-element-wrapped-in-div-is-not-clickable/)

如果你想读更多这样的东西，给我一个❤️，在 dev.to 上跟我来