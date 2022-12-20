# 我的新 swift 工作流程或如何提高您的 swift 生产率！

> 原文：<https://dev.to/nverinaud/my-new-swift-workflow-or-how-to-boost-your-swift-productivity--5di8>

每次我修改 Xcode 或 AppCode 时手动运行测试让我抓狂(因为 AppCode 中的缓慢和错误)，所以我决定自动化它。

目标是什么？拥有这样的工作流程(当我做一些 JavaScript 时，我观察并喜欢它) :

1.  编辑一些源代码，
2.  无需我做任何事情，观察测试自动运行，给我即时反馈我做得有多好。

下面是我如何为 swift 开发启用这个工作流(它也可以应用于其他语言，只要您可以从命令行运行测试) :

1.  安装[节点](http://nodejs.org/)
2.  安装 [nodemon](http://nodemon.io)
3.  在您的`workspace`(或`project`)文件夹中运行这个命令(使用您最喜欢的终端或嵌入在 AppCode 中的终端，我使用它是因为我在全屏下工作)。

```
nodemon -e swift,strings --exec "xcodebuild -workspace MyApp.xcworkspace -scheme \"MyApp\" -destination 'platform=iOS Simulator,name=iPhone XS,OS=12.1' test" 
```

只要把`workspace` & `scheme`换成你的就行了！您可以对任何 swift 或 strings 文件进行更改，boom 测试将自动运行！

如果对你有帮助，请在评论或推特上告诉我！

* * *

我最初写了我使这成为可能的完整故事(带戏剧)。我把它留在下面给你欣赏！😁

# 背后的全部故事

这一切都始于一次挫折。

我喜欢 *Xcode* 构建&运行测试的**速度**。

但是，woa，**重构能力**，尽管越来越好，仍然远远落后于 *AppCode* 所提供的。

我喜欢 *AppCode* 的**重构能力**。

但是，哇，构建和运行测试太慢了。

它每隔一段时间就失败一次，比如，每小时都会出现一个“db lock blabla”错误，要求我清理、清理构建文件夹、祈祷、哭泣、重新启动 IDE、祈祷。最终生产力又回来了。

选择你的痛苦！

## 伟大的 Ide(一)

所以，昨天我有了一个想法。

> 等等，构建你自己的 IDE，对吗？

没错。

> 你知道这是一个巨大的工程，像一个巨大的工程！

是啊，很酷不是吗？！

> 你没有时间做那件事。

什么？...

哦，我想你是对的。我注定要失败吗？

> 你是开发商，开发商永远不会完蛋！1 小时内找到可行的解决方案怎么样？像一个**创业挑战**！

## 挑战接受！

使用 *AppCode* 作为源代码编辑器& *Xcode* 来构建&运行测试不是很酷吗？

那会是我的 MVP。

> 手动在两个 ide 之间来回切换？你开玩笑吧。

哦...很痛苦不是吗？

使用 *AppCode* 的集成终端从命令行“构建并运行”怎么样？

> 好像好多了！

所以我的解决方案只差一点点了！

* *钻研手册* *

*TADA！*这是我的命令！

```
xcodebuild -workspace MyApp.xcworkspace -scheme "MyApp" -destination 'platform=iOS Simulator,name=iPhone XS,OS=12.1' test 
```

> 真的吗？

没错。我现在的工作流程是:

1.  编辑一些源代码
2.  运行上面的命令
3.  `goto 1`

> 不错，但是我有另一个挑战！

我还有一些时间，告诉我。

> 编辑源代码时自动运行这个命令**怎么样？**

嗯...

有一天我做了这件事...nodeJS...
...当代码改变时，手表在运行...比如热重装，运行测试...

> 很酷的故事...但是这是严肃的事情，不是 JavaScript 炒作的事情。

我是认真的！

哦，是的，我是...

所以它使用一个名为`nodemon`的 npm 包来监视文件变化。

* *正在搜索节点...深入研究文档* *

*TADA！*我可以运行这个`nodemon`东西，告诉它注意执行我的自定义命令的`swift` & `strings`文件&！

> 证明一下！

没问题，看这个！

```
nodemon -e swift,strings --exec "xcodebuild -workspace MyApp.xcworkspace -scheme \"MyApp\" -destination 'platform=iOS Simulator,name=iPhone XS,OS=12.1' test" 
```

> 哦，拜托，你是说你现在的工作流程是...

1.  编辑源代码
2.  没有第二步。

## 我学到的教训

1.  挫折对我来说是一个强有力的行动号召。
2.  学习不相关的东西，比如这种情况下的 nodeJS，可以打开新的视野&提高创造力。
3.  使用 1 小时时间盒迫使我找到一个足够好的解决方案，结果超出了我最初的预期！