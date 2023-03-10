# 关于创建一个帮助学习傣文的 Ionic 应用程序的思考

> 原文：<https://dev.to/programmingdecoded/reflections-on-creating-an-ionic-app-to-help-learn-the-dai-script-306n>

为了 SIL，我创建了一个定制版的 [TeachMeWords](https://phil4literacy.github.io/TeachMeWords/) 来学习傣族文字(傣族是中国的一个少数民族)。

首先我简单介绍一下这款应用的外观。在第一个屏幕上，用户选择 7 个课程中的一个。点击课程后，用户可以选择学习单词或练习单词:

[![](img/7f37ae7142d9a78514ac6148b02e095c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qzxF8cah--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/03/DaiApp01.png)

在学习模式中，人们可以点击字符或相应的示例单词来听它是如何发音的。每一课只包含 6 个角色。

[![](img/4f53babdb4ca3289f15e0e73dd404883.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xClXF1-C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/03/DaiApp02.png)

在练习模式中，一个人听到字符或示例单词，并得到 4 个答案选项(2 个不同的字符加上它们相应的示例单词)。一旦你答对了，它就会进入下一个问题。在所有的字符和示例单词都被查询后，它将重复错误回答的问题，直到一个人第一次回答所有正确的问题。

[![](img/7c130034c9d335d6594555ae672c2ac4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C92fETsh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.programming-decoded.com/wp-content/uploads/2019/03/DaiApp03.png)

虽然这是一个相当简单的应用程序，但它需要一些原型才能达到傣族教师想要的效果。这个问题可能是因为他们一开始根本不知道自己到底想要什么。通过第三人(另一个外国人)与他们远程通信，理解和交流需求变得更加困难。最终，飞到那里与他们见面并当面讨论事情可能是值得的。

我很高兴的一件事是响应式设计是如何工作的。在学习和练习模式下，用户不必滚动来查看所有内容。如果一个旋转到横向模式，它将显示按钮在旁边，而不是在下面。为此，我使用了 css float 属性，但我也可以用 css grid 实现同样的效果。

我遇到的一个有趣的问题是，在一些手机上，它会以错误的顺序显示课程，但在我的手机上不会，即使我使用的是旧版本的 Android。我不得不使用 Android 模拟器来调试这个问题，在那里我设法重现了这个问题(我尝试了一个旧的 Android 版本)。我最终改变了排序函数。

加载应用程序时，数据从 json 文件导入并存储在本地存储中。

该应用程序非常简单，但我仍然希望做一些自动测试。可悲的是，我没有得到这个工作。希望有一天，自动测试的设置将被内置到 Ionic 中。

尽管 Ionic 和 Cordova 让开发人员很容易上手，但是当升级到一个新版本的 Ionic 时，可能会很沮丧。我实际上想使用一个简单的插件来显示应用程序版本，但它不能与我正在使用的 Ionic 版本一起工作，并且更新依赖关系会导致其他问题。根据我的经验，升级到新版本的 Ionic 会导致系统崩溃，所以目前我没有这么做。

但总的来说，我很高兴，除了一个我能够修复的小问题，这个应用程序在我们迄今为止测试的所有手机上都运行良好。

现在我只是在等着看这个应用程序是否会被使用，并帮助那些想学习阅读傣文的人。