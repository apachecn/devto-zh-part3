# 如何修复“扔 er；//未处理的“错误”事件”？

> 原文：<https://dev.to/spekulatius1984/how-to-fix-throw-er-unhandled-error-event-d4k>

有些错误是容易和简单的。快速修复，几分钟内解决问题。其他人则不然，你需要挖掘更长的时间。这是给我的其中之一。为了方便其他开发者，我在这里分享我的解决方案。

这个问题似乎特别影响像我这样的 Linux 用户。我不能确定这一点，因为我只运行 Linux🐧️在我的[基础](http://elementary.io/)上这个问题通常出现在我试图运行 npm/yarn 命令的时候，这些命令本应该监视文件的变化(例如 CSS/SCSS 或 JavaScript 文件)。相关的`dev`和`prod`命令工作正常。它只影响了`watch`命令，并且看起来与文件系统不兼容有关。

在一个项目中使用我的瑞士 armyknife 构建工具 [Laravel Mix](https://peterthaleikis.com/tags/laravel-mix/) 时，这个特殊的案例问题出现了。当运行`npm run watch`(实际上是`yarn watch`)时，我在之后得到这个错误消息*，编译步骤完成:* 

```
events.js:182
      throw er; // Unhandled 'error' event
      ^ 
```

行号取决于项目中使用的具体库，但是`events.js`文件通常是它的一部分。

如果得到支持，`watch-poll`通常可以解决问题。但是有些图书馆，比如我的 eleventy starter(这个博客)，不支持 poll，所以我需要采用不同的方法。在花了一些时间研究和尝试后，我找到了一个目前可行的解决方案。在重新安装节点依赖关系之前，我删除了`node_modules`-文件夹(`rm -rf node_modules`)并强制清除了 NPM 缓存(`npm cache clear --force`)。概括起来有以下几个步骤:

```
rm -rf node_modules
rm package-lock.json yarn.lock
npm cache clear --force
npm install 
```

请注意:这也删除了锁定文件。从而更新您的项目，确保在将更改推向生产之前测试所有内容🙏️

## 更新#1:解决方案:停止所有其他“监视”进程

如果您有许多节点进程正在运行，这可能是您的问题的一部分。我注意到，如果我有许多正在运行的观察进程(针对其他项目),这个问题会更频繁地出现。在如上所述停止这些操作并清除文件后，问题得到了解决。

## 更新#2 (2020-03-04):错误:ENOSPC:已达到文件观察者数量的系统限制，观察“src/assets”

随着最近我的依赖项的更新，我注意到一个新的，更好理解的已知错误的措辞。这会让你更快地找到正确的方向。同样，我已经能够使用上面提到的方法解决这个问题。

## 更新#3 (2020-04-23):增加允许文件句柄

一次偶然的机会，我发现了一个增加允许的文件句柄数量的解决方案。这实际上是随着我最近对微软 Linux 工作室代码的追踪而来的。它抛出了一个常见的错误:

```
Visual Studio Code is unable to watch for file changes in this large workspace (error ENOSPC) 
```

Microsoft 建议采取以下措施来增加允许的文件句柄数量。你可以通过更新你的`/etc/sysctl.conf`文件。详细步骤列在[这里](https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc)。