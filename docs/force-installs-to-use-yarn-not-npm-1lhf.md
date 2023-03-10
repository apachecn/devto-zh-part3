# 如何强制包装安装使用纱线而不是 npm

> 原文：<https://dev.to/ascorbic/force-installs-to-use-yarn-not-npm-1lhf>

抛开关于哪个节点包管理器更好的争论不谈，有一点应该清楚的是，在同一个项目中切换它们不是一个好主意，因为冲突的锁文件会使项目处于不可预测的状态。如果你在一个有来自 npm 的`package-lock.json`的项目中运行 yarn，那么它会警告你，但是 npm 不会反过来。如果你在一个有`yarn.lock`的项目中运行`npm install`，它会很高兴地安装这些包并创建一个 package-lock.json 供 yarn 下次抱怨。这是我的解决方案。

如果你给你的`package.json`添加一个`"preinstall"`脚本，它会在 npm 或 yarn 安装之前运行。如果退出并显示错误代码，则安装中止。这意味着这是检查的最佳地点。在脚本执行期间，环境变量`$npm_execpath`被设置为 npm 或 yarn 可执行文件的路径，所以我们可以用它来检查安装使用的是什么。您可以使用 node 来检查这一点，但这看起来有些多余，所以我将在脚本中直接使用 shell 命令。我回显变量的内容，通过管道将它传递给`grep`，如果它与 yarn 不匹配，那么它将出错退出。(我在 shell 脚本中的❤️表情):

```
echo "$npm_execpath" | grep -q "yarn\.js$" || (echo '⚠️ Use yarn not npm! ⚠️' && exit 1) 
```

Enter fullscreen mode Exit fullscreen mode

这工作得很好，但是它看起来很混乱，因为它在运行时显示完整的命令，所以即使一切正常，看起来也有一个错误。解决这个问题的方法是让它成为一个单独的脚本，用`--quiet`标志运行。我们可以再次使用`$npm_execpath`来确保我们使用相同的脚本运行它:

```
"scripts":  {  "preinstall":  "$npm_execpath --silent run checkyarn",  "checkyarn":  "echo \"$npm_execpath\" | grep -q \"yarn\\.js$\" || (echo '⚠️  Use yarn not npm! ⚠️ ' && echo && exit 1)"  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行`npm install`，你会得到错误:

```
example git:(master) ✗ npm i

> example@0.0.1 preinstall /Users/matt/Documents/repos/example
> $npm_execpath --silent run checkyarn

⚠️  Use yarn not npm! ⚠️

npm ERR! code ELIFECYCLE
errno 1 
```

Enter fullscreen mode Exit fullscreen mode

...但是如果你运行`yarn` :

```
➜  example git:(master) ✗ yarn
yarn install v1.12.3
$ $npm_execpath --silent run checkyarn
[1/4] 🔍  Resolving packages...
success Already up-to-date.
✨  Done in 1.35s. 
```

Enter fullscreen mode Exit fullscreen mode

安装愉快！