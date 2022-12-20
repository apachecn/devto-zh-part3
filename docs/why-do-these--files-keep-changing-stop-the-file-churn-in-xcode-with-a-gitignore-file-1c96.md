# 为什么这些美元？@!%文件一直在变？用 gitignore 文件停止 Xcode 中的文件变动

> 原文：<https://dev.to/evandeaubl/why-do-these--files-keep-changing-stop-the-file-churn-in-xcode-with-a-gitignore-file-1c96>

Xcode 通过`git`获得源代码控制支持已经很久了。令人惊讶的是，即使是现在，在它的默认配置中，你可能会发现各种你不想要的文件出现在你的 repos 中:`.DS_Store`、`xcuserstate`、`build`、`.ipa`文件，只是作为开始。

您想要做正确的事情，并且您知道或者听说过检查特定于用户的文件、临时文件或者将产品构建到源代码控制中不是正确的事情。您希望避免由频繁更改的文件导致的合并冲突。或者您只是厌倦了在 IDE 中做一些事情，突然您的项目修改了要提交的文件。

你应该能够告诉 Xcode 你想在你的项目中进行源代码控制，它应该以正确的方式处理一切。但是 Xcode 没有正确处理这一点，让您失望了。

你是做什么的？好消息是，通过在 repo 中应用 gitignore 文件，解决方案只需几步之遥。

* * *

一个`gitignore`文件告诉 git 不要添加或提交与文件中包含的模式列表相匹配的文件。例如，这可以很容易地阻止那些烦人的`.DS_Store`文件:

```
.DS_Store 
```

但是 Xcode 生成了哪些你想忽略的文件呢？好消息是有一个网站， [gitignore.io](https://gitignore.io) ，它收集了许多开发环境和语言的`gitignore`文件，包括 Swift、Objective-C 和 Xcode。这里需要做的就是打开网站，在框中输入 Swift，然后点击 Create。[ **注意:**也有一个 Xcode 选项，但是在回顾它的时候，看起来它最终忽略了重要的文件。这表明即使是互联网也不是完美的，要经常回顾你从那里得到的任何东西！]

将生成名为`.gitignore`的文件保存在 Xcode 项目的顶层。

有了`gitignore`之后，要修复已经提交了这些文件的回购，您需要从回购中删除所有内容，并将其全部添加回去。这样，现在由。gitignore 将被`rm`命令移除，但不会被`add`读取。**确保您在干净回购的顶级目录中运行此程序(没有修改或未提交的文件)。**

```
git rm -r --cached .
git add .
git commit -m"Add .gitignore and remove existing gitignored files from repo" 
```

如果随着时间的推移，您遇到了导致相同问题的其他文件，将这些有问题的模式添加到您的`gitignore`中，并重复上面的 git 命令。这是一个简单，没有麻烦的方式来获得一个干净，最少的 git 回购。

* * *

你喜欢这个建议吗？下一篇关于[为什么不应该在 Swift 代码](https://www.appsdissected.com/force-unwrapping-not-swift-like-wrong/)中使用强制解包的技巧已经在等着你了。或者[注册，让每一条建议都直接进入你的收件箱。](https://www.appsdissected.com/newsletter/)

这篇文章最初发表在 [Apps 剖析。](https://www.appsdissected.com/stop-changing-files-xcode-gitignore/)