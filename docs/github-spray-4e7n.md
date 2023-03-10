# 👾GitHub Spray:在你的 GitHub 贡献图░▒▓█上画画

> 原文：<https://dev.to/annihil/github-spray-4e7n>

[![](img/cff9b96072fbcb835addd3b63401b9e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aRTRi_7q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/nPZyGNo.gif)

这听起来可能是一个毫无意义的项目，但事实是，我们都喜欢将自己与他人区分开来，也许你会喜欢在你的 GitHub 页面上有这样一个小细节。

[这款开源 CLI](https://github.com/Annihil/github-spray) ，作为一个 npm 模块提供，让您能够“喷涂”您的 GH 个人资料页面。

[![](img/be5913cf31756f2b40ea02d33b9048a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QVUJPFoP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/FlokKGl.png)

命名的想法来自反恐精英，在那里你可以用你的自定义图像喷涂墙壁，这非常受欢迎，以至于一个非常著名的技术是在墙上喷涂一个性感的半裸女士，以吸引敌人的注意，并在他们的防御降低时消灭他们。

使用此工具，您可以在您的个人资料上显示的 53x7 像素日历热图上绘图。

它已经包含了几种字体，以便您可以轻松地写文本。

```
$ github-spray -t hello 
```

[![](img/6e301a291edc7338ffd59fb9603f5d1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IXSDsgnZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/SCMxPUN.png)

不然可以更有创意，自己设计图案。

```
$ github-spray -f my_octocat_heart_smiley_pattern.json 
```

[![](img/37a4f44e5cc94874b2cef4e036ad2d52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ayNBPhwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ucwnJud.png) 
我做了[一个方便的网络应用](https://annihil.github.io/github-spray-generator/)来简化创作。

它有许多选项，如指定开始日期，字体，反转颜色，调整颜色，水平或垂直翻转喷雾，设置一个回购原始网址，并推动它。

你只活一次，去争取吧: [GitHub 喷](https://github.com/Annihil/github-spray)🤙
顺便说一句，喷雾不是永久的，你可以通过移除回购来删除它🙂

编辑:**非常感谢开发社区**让回购出现在 [GitHub Javascript 趋势页面](https://github.com/trending/javascript?since=daily)，我爱你们！！< 3