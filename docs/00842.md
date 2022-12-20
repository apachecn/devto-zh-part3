# 仅在必要时在节点中重建

> 原文：<https://dev.to/samthor/rebuild-only-when-necessary-in-node-506e>

如果你的项目需要一些时间来准备——可能是编译或者重写——你可能需要等待更长的时间。这让我们想到了谷歌的圣诞老人追踪器🎅，在这里我们使用闭包编译器来构建大多数游戏。Closure 是一个很好的优化器，但是它并不以速度著称。

所以这篇文章的主题是:缓慢的构建一点也不好玩，我们将学习如何只在需要的时候运行它们！🎉

## 手表原始人

我们可以使用 NodeJS' `fs.watch`函数来通知我们是否实际需要，而不是每次加载编译后的资源或手动重新运行脚本时都构建游戏、场景或代码库。

从表面上看，这是一种简单的方法，它可以告诉您磁盘上的文件何时发生了变化。这样用:

```
const fs = require('fs');
fs.watch('yourfile.txt', (eventType, filename) => {
  // something happened to 'yourfile.txt': ¯\_(ツ)_/¯
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个超级有效的方法，因为它要求你的操作系统让*你*知道什么时候发生了变化(而不是反过来，你的程序必须不断检查)。

### 构建用法

假设你正在编译一些少了[CSS](http://lesscss.com)的文件。您可以通过编译一个文件`entrypoint.less`来做到这一点，该文件具有依赖关系:

```
const less = require('less');

less.render(`@import 'entrypoint.less';`).then((output) => {
  console.info(output.css);

  // contains all files that were imported, e.g:
  //   entrypoint.less => main.less => body.less
  // will result in ['entrypoint.less', 'main.less', 'body.less']
  const files = output.imports;
}); 
```

Enter fullscreen mode Exit fullscreen mode

Less 将为我们提供它在构建中使用的文件的简单列表。其他一些工具可能会为您提供一个[源映射](https://developer.mozilla.org/en-US/docs/Tools/Debugger/How_to/Use_a_source_map)，其中也包含原始文件的名称。

如果这些文件中的**任何一个**发生变化，最终输出无效，我们要重新构建。实际上，这只是意味着在每个文件上调用`fs.watch`:👀

```
 const files = output.imports;
  files.forEach((file) => {
    fs.watch(file, () => rebuildIsNeededCallback());
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这在技术上是可行的，但是它并不适合整个构建系统。请继续阅读！😄👍

### 告诫

虽然`fs.watch`是一个强大的函数，但它也有一些限制。这些可以归结为几点:

*   并不总是能保证告诉你哪个文件已经被修改了
*   在 Linux、macOS 和其他系统上，`fs.watch`跟在被监视文件的*索引节点*之后
    *   ...如果一个文件被移动到了新的位置，你会得到通知
    *   ...如果一个文件被*替换了*，你会被通知一次，但是新文件不会被自动观看
*   当你不再需要它的时候，你需要在结果上调用`.close()`——如果你忘记了，你的程序将保持打开的监听器

实际上，这些警告意味着你应该把每个对`fs.watch`的调用作为一次性的提示事情已经发生了变化。💭请这样想:您不能确定到底发生了什么变化，但这值得一查！

将`fs.watch`视为一次性事件的另一个理由是:如果你的依赖关系*通过添加或删除文件而改变了*，重置你所有的观察者可能比试图保持最新更容易。🤓

## 手表助手

让我们把上面的知识放在一个小助手里，当代码发生变化时，它会帮助你*使*代码无效。这就是我们在圣诞老人追踪器中所做的；我们保留构建输出，直到它不再有效(因为底层源代码已经改变)。

🚨你可能会说“为什么*使*无效，而不只是进行一次完全的重建？”除非你需要尽可能快地输出，否则每次保存都要运行一个昂贵的编译步骤。

因此，下面的`watch`方法将接受一个路径列表，观察它们，并在它们中的任何一个发生变化(或者超时)时调用一个回调函数:

```
function watch(paths, done, timeout=0) {
  let watchers;
  let timeoutId;
  const finish = () => {
    // To finish, we close watchers (because it's not clear
    // what state they are in), cancel the timeout callback,
    // and let the user know something changed.
    watchers.forEach((w) => w.close());
    clearTimeout(timeoutId);
    done();
  };

  if (timeout > 0) {
    // If a timeout is given, 'succeed' after ~timeout. This is
    // useful to *always* rebuild or invalidate after a time.
    timeoutId = setTimeout(finish, timeout);
  }
  watchers = paths.map((p) => fs.watch(p, finish));
} 
```

Enter fullscreen mode Exit fullscreen mode

一定要看一下代码👆，因为我已经留下了一些解释它做什么的评论。让我们把这个和上面的例子放在一起。

## 少即是多

那么，当依赖关系改变时，我们如何使输出无效呢？

我们可以用两个方法和一个缓存变量来实现这一点:

*   确保包含结果的`Promise`可用；和
*   `buildCSS`在需要时实际重建(因为这是`async`，它返回一个`Promise`)。

```
let compileCache;

async function buildCSS() {
  console.debug('rebuilding CSS...');
  const output = await less.render(`@import 'entrypoint.less';`);

  watch(output.imports, () => {
    compileCache = null;  // force a rebuild next time
  }, 60 * 1000);

  return output.css;
}

// call getCSS whenever you need CSS, and it'll always be up-to-date
function getCSS() {
  if (!compileCache) {
    compileCache = buildCSS();
  }
  return compileCache;
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是一个非常简单的例子，只缓存一个结果:如果您想扩展它，您可以使用一个输出字典，如果它们的依赖关系发生变化，每个输出都可以失效。

### 终于

为了最终将`getCSS`连接到这个世界，我现在将向您最喜欢的 NodeJS 服务器添加一个处理程序，这样当我加载比如说`/compiled.css`时，它将返回`getCSS`的结果，确保编译后的版本总是最新的。在[波尔卡](https://github.com/lukeed/polka)中，它可能看起来像:

```
polka()
  .get('/compiled.css', (req, res) => {
    res.end(getCSS());
  }); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道更多你可以安装开发服务器来做这件事的方法，请在下面告诉我！💬

## 感谢

如果你直接使用一个现代的打包系统(或者构建工具)，那么这个工具可能已经在使用`fs.watch`了。然而，我仍然希望您已经了解了如何使用`fs.watch`来改进您的构建系统！

说句题外话:我个人已经不再使用像`gulp`和`grunt`这样的构建工具，而是直接使用定制的构建工具*或者按需执行编译的*网络服务器(由`fs.watch`提供支持，就像我们在圣诞老人追踪器中所做的那样)。

one