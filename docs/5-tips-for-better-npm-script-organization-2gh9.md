# 更好的 NPM 脚本组织的 5 个技巧

> 原文：<https://dev.to/marcel_cremer/5-tips-for-better-npm-script-organization-2gh9>

不久以前，在 JS 项目中使用 grunt、gulp 和其他工具来组织构建、测试和其他任务是非常正常的。然而，这需要人们安装、维护和理解不同的构建系统、结构和配置。

### NPM 武丁来救援了！

每个使用 npm 作为包管理器的人都已经有了 package.json，并且能够从那里运行脚本。那么，有什么比使用 npm 脚本来完成项目的自动化任务更明显的呢？

然而，由于 npm 脚本对复杂构建系统的建模没有任何意见，脚本的组织和一致性是维护开发人员的责任。这里有一些你可以做的提示。

### 1。一致的脚本名称

如果你开始在几个任务中使用 NPM，你的脚本部分可能会增长得很快。举例来说，你可能有用于单元测试、集成测试、覆盖报告以及开发观察模式的脚本。如果它是有机的，你可能会得到类似于
的东西

```
{  "build":  "...",  "test":  "...",  "test-watch":  "...",  "integration-test":  "...",  "watch-integration-test":  "...",  "test-coverage":  "...",  "test-integration-coverage":  "...",  "build-prod":  "..."  } 
```

Enter fullscreen mode Exit fullscreen mode

***Urks...*T3】**

你可能一开始没有注意到，但更常见的是，你会拼错你想做的任何事情，例如，你写

```
npm run integration-test-watch 
```

Enter fullscreen mode Exit fullscreen mode

而不是

```
npm run watch-integration-test 
```

Enter fullscreen mode Exit fullscreen mode

触发众所周知的“找不到命令”错误。

所以有时候当你想把它换个方向，猜猜会怎样？下次试的时候又写错了**，因为你没有系统...还没:)**

 **因此，我们需要的是一些命名方案，这是一致的，在你自己的审美观点。例如，我使用类似于:
的东西

```
{  "build":  "...",  "build:production":  "...",  "test":  "...",  "test:coverage":  "...",  "test:watch":  "...",  "test:integration":  "...",  "test:integration:coverage":  "...",  "test:integration:watch":  "..."  } 
```

Enter fullscreen mode Exit fullscreen mode

所以我从我想做的事情开始，然后指定它或者调用额外的行为。因为顺序总是保持不变，所以我不太可能拼错。

我也有一些“内部”脚本，只在 package.json 内部用于干的目的。通常我让它们以一个标签开始，这样人们就不会想到直接使用它们。例如:

```
{  "\#:copy:assets":  "...",  "\#:copy:configuration-templates":  "...",  "\#:generate:polyfills":  "..."  } 
```

Enter fullscreen mode Exit fullscreen mode

### 2。表演

人们通常不关心的一件事是性能。如果我们想到单元测试，很明显每一秒都很重要。所以如果你每分钟按下 ctrl+s 大约 10 次，每个测试周期花费 3 秒，你就花了**大约一半的时间(10 * 3 = 30 秒)等待你的测试结果**！很可怕，不是吗？

如果你在一个类型脚本环境中，代码也需要在测试之前编译成 javascript。可以执行。所以，在浪费时间等待之前，先花一分钟思考一下你的任务执行。

*   只要你能够并行执行任务，就同时使用类似于[和](https://www.npmjs.com/package/concurrently)的模块(例如，让你的 typescript 编译器监视。ts 文件，您的测试会观察输出。js-files)。
*   尽量避免不同的任务做同样的事情(例如，集成测试和单元测试都会触发 typescript 编译器)
*   为不同的任务制作精细的脚本，并将它们链接到方便的脚本中
*   不时地检查你的脚本，看看你遇到了什么瓶颈

### 3。平台独立性

因为我主要在 windows 上开发(如果你愿意，可以怪我...)，我真的很讨厌当我想为一些开源项目做贡献的时候，还有

```
npm run start 
```

Enter fullscreen mode Exit fullscreen mode

脚本失败，因为它依赖于一些 unix 命令。尽可能使用节点实现，以避免特定于平台的代码。

例如:

*   使用 [rimraf](https://www.npmjs.com/package/rimraf) 代替 rm -rf
*   使用[复制文件](https://www.npmjs.com/package/copyfiles)而不是通过操作系统命令复制
*   ...你明白了。)

更糟糕的是 npm 包的使用依赖于**本地操作系统调用**，这些调用在可用之前需要用 node-gyp 编译。

如果你不相信我，看看 [stackoverflow](https://stackoverflow.com/search?q=node-gyp) 关于 node-gyp 制造了多少问题，**在**你决定使用某个原生 OS 库来“异步相加 2 个数”(或者其他类似的奇闻)！

我知道，有些场景需要本地库，这很好，但是在将它们作为依赖项添加之前，请三思。例如，如果原因是性能，则提供一个*“缓慢的、平台无关的、nodejs”-路*作为缺省值，并添加*“本机调用”-路*作为**对等依赖**，这样人们可以自己决定，性能是否值得本机模块编译。

永远不要选择“可能不为某人工作”，因为你推开的人可能会给你一个难以置信的拉请求。

### 4。附加参数

有时候，我看到一些项目多次执行完全相同的脚本，只是为了添加不同的参数。如果你提供方便的方法(例如，启动一个开发 web 服务器)，这没问题，但是如果你有一打这样的方法，你也可以考虑人们只是参数化你的脚本。

所以代替

```
{  "start":  "node server.js",  "start:integration-port":  "node server.js --port=4202",  "start:https":  "node server.js --https=true",  "start:integration-port:https":  "node server.js --port=4202 --https"  } 
```

Enter fullscreen mode Exit fullscreen mode

您还可以提供一个 start 命令，并使用-将附加参数传递给脚本。可以像
一样使用

```
npm run start
npm run start -- --port=4202
npm run start -- --https=true npm run start -- --port=4202 --https=true 
```

Enter fullscreen mode Exit fullscreen mode

### 5。NPX 而不是单一的命令脚本

从 NPM 5 开始，NPM 支持一个叫做“NPX”的工具。它主要做的是，从你的依赖关系中执行一个脚本作为节点可执行文件。

例如代替写

```
node ./node_modules/typescript/bin/tsc 
```

Enter fullscreen mode Exit fullscreen mode

你可以写

```
npx tsc 
```

Enter fullscreen mode Exit fullscreen mode

并将执行您的本地 typescript 编译器(更多信息[在这里](https://www.npmjs.com/package/npx))。

有时候我会查看一些项目，它们有 20 个 NPM 的剧本，其中一些是

```
 {  //  ...  "webpack":  "webpack",  "tsc":  "tsc",  //  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

这些都没用。使用 NPX，并收紧你的包。

你有更多的建议吗？你如何构建你的 package.json？**