# 编写更好代码的工具

> 原文：<https://dev.to/kartik2406/tools-for-writing-better-code-21mh>

在这篇文章中，我将列出一些我个人使用的工具，它们提高了我的编码技能和开发经验。这些工具很容易安装和学习，从长远来看会对你有帮助。

### 去吧

Git 允许你对你的代码进行版本控制，你只需要点击几下鼠标就可以轻松回到你几个月前写的代码。学习 git 节省了我大量的时间。您不必维护代码的单独副本。当处理一个新特性时，只需创建一个分支，以前如果我想尝试一些东西，我会创建一个代码的副本。还有像 [Github](https://github.com/) 这样的在线库托管，你可以有一份你的代码的拷贝，然后你可以下载到另一台机器上。当多人在同一个项目上工作时，这也很有帮助，如果没有冲突的话，更改会自动合并。Github 甚至有一个不错的[帖子和教程](http://try.github.io/)的集合。

### VS 代码

VS code 看起来不错，在你编码的时候不会妨碍你，但是提供了你可能需要的所有特性。它有一个名为 Intellisense 的特性，可以为变量和函数建议自动完成选项。如果您将鼠标悬停在内置方法上，您可以看到它需要哪些参数，有时还需要文档，这样可以省去我去浏览器的麻烦。此外，如果你很难一下子记住太多的快捷方式，那么它已经覆盖了你，只需记住一个快捷方式。命令选项板，它是您可以执行的所有选项的可搜索列表。一旦你找到了你要找的动作，只要按下回车键就可以了。除了这些特性，它还有很好的 git 支持。它将帮助你轻松解决冲突。VS 代码还附带了大量的扩展来帮助你编码。

我将在这里列出几个我最喜欢的。

*   [自动关闭标签](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag) & [自动重命名标签](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag):写 HTML 时有帮助的东西。
*   [括号对上色器](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer):每个嵌套的括号都有不同的颜色编码，你可以很容易地发现括号在哪里闭合
*   [更好的评论](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments):你可以让 Todo、Info 的评论有不同的颜色，这样你就可以很容易地找到它们。
*   Chrome 调试器:允许你使用 VS 代码调试客户端 JS。
*   [Git 历史](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)&[Git lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens):VS 代码内额外 Git 超能力。
*   [Live server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) :为你正在处理的文件提供服务，你可以在浏览器中查看应用程序，在你修改代码时刷新。

### 棉绒

Linters 允许您在整个代码库中强制执行编码指南。这样，如果你想提高你的编码技能，如果一个新人开始在同一个项目中工作，在整个项目中不会有多种代码风格。对于 Javascript 来说，看看 ESLint，有很多规则你可以在你的代码库上执行。

### 漂亮些

一个整洁的代码不亚于一件艺术品。一个整洁的代码不仅好看，而且可以帮助你快速调试。
手动格式化你的代码可能有点烦人，但是[漂亮的](https://prettier.io/)来帮忙了。你可以为你的 ide 安装更漂亮的插件或者运行节点包，它会格式化代码。如果你和很多人一起做一个项目，这个效果会更好，因为即使有人修改了代码，样式还是会保持不变。它允许您拥有自己的配置文件，以防您不喜欢默认规则。

### Git 挂钩

如果你能在提交、推送任何东西之前格式化你的代码或者运行测试，那不是很好吗？这会节省你的时间和精力，不是吗？你可以在你的代码上运行 linter，如果它失败了，修改将不会被提交。好吧，git 钩子让你做完全一样的事情。查看[这个网站](https://githooks.com/)了解更多信息。如果你正在使用 node，你可以使用这个很棒的软件包: [husky](https://www.npmjs.com/package/husky) 。只需在提交之前添加您想要做的事情，推入 pacakge.json 并让 husky 为您处理其余的事情。

### Nodemon

如果您正在使用 Node 开发 API，那么一旦做出更改，您将经常需要手动重启节点进程。当你编码时，这是令人沮丧的，有了 [nodemon](https://nodemon.io/) 你就不会面临这个问题。只需使用 nodemon 运行脚本，一旦您对同一文件夹中的脚本或文件进行任何更改，它就会重新启动。