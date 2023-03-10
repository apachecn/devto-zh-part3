# 喜欢 Jest 的理由:开发者体验

> 原文：<https://dev.to/lirantal/reasons-to-love-jest-the-developer-experience-4o6f>

哦是的。Jest 的开发者体验正在将编写测试的行为从一件苦差事转变为一段有趣的时间，我保证！🤓

这篇文章是我上一篇关于 Jest 框架的文章的后续:

[![](img/8161903e216cb8c182bcfda161c85908.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7E1fzQkn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4uyjaz6b3sykye6p1ilm.png)

# 标志

啊，那个标志。不就是好吗？就像它试图告诉你“你会写测试吗？这会很有趣的！”就这样，它引诱你进入

[![](img/b3b3d1b7a120b436de46c3f98ead18b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VHSqx-mw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zh9gp3zq42oxnn0zqdtg.png)

好吧，但严重的是，我只需要在左侧的一个项目排序对齐其余的项目。请原谅我🤷‍.️

如果你愿意的话，关于这个标志的一个趣闻——
最近我得知 Jest 标志是由[詹姆斯·皮尔斯](https://medium.com/@jamespearce)在最后一分钟的草图中创造的，他在草图中反复考虑了几个选项([推特参考](https://twitter.com/jamespearce/status/1011494561682620416))，但更有趣的是[克里斯托夫·中泽友秀](https://medium.com/@cpojer)提到……彼此相邻的圆圈让他想起一个与缓慢相关的加载动画:-)

[![](img/f878d45d8a9ea3d2199442befb2413ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0NZmcDWZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v5hmmpwozrcexq70egrr.png)

# 视觉差异和有效详细度

好的开发者体验很大一部分是提高你的生产力。谈到测试，当测试失败时，您希望快速找出测试中的问题。

以这段代码片段为例:

[![](img/8aa62c1dd4323c8caf93da676a49df18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8KUfXh0_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/abcz1a4pwy8hv8kx7mms.png)

它在测试的源代码中有一个打印错误。下面是 Jest 在控制台中显示错误的方式:

[![](img/629f1c6ab762e2554e6d0aa57844455d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VH4DuxTE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5xow6bg6oaf4f1z2jyq.png)

它为实际文件提供了很好的上下文、行号和指向确切问题的箭头，还用语法高亮显示代码。

你打算在你的断言中比较两个对象吗？完全没问题。Jest 是如此冗长，以至于它会显示出这种巨大的差异，即使是在您比较的对象之间不同的嵌套键也是如此:

[![](img/5fab75ad2f8d76c1bc34d1fcbb9fbfcb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WFseeEjV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szklo1yvk3i9uk3eip0x.png)

补充说明:Jest 已经非常模块化，它的许多功能都转移到了社区可以利用的独立模块中。

如果你喜欢上面的不同，你可以在你自己的项目中使用它，请看这里:[http://jestjs.io/docs/en/jest-platform.html#jest-diff](http://jestjs.io/docs/en/jest-platform.html#jest-diff)

# 轻松的约定

测试套件惯例
如果你来自不同的测试运行者或框架，你会知道它们在测试套件语法上有所不同。

[![](img/a5b83d522d240abe88593c93e83a3348.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MpD8xJxH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ditx8p6qds7ra8xrhlmp.png)

有些使用 describe.only()，有些只能使用 test()。在其中一些中，您可以通过 test.skip()禁用测试，而在另一些中，您可以通过 xit()禁用测试。

用 Jest，没关系。它尽最大努力优化生产力，而不是墨守成规。

你可以写 test()，或者一个嵌套的 describe()和 test()，或者直接用它()。不用动脑筋。

[![](img/4ba9ea732b8fc4265e1346ecfe634900.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CmoT_Qgy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l68k1bqov4pwmm3rv4bz.png)

您应该为测试使用哪种文件命名约定？谁在乎呢！😜

Jest 将自动获取任何*.test.js 或*.spec.js 文件扩展名，以及 **tests** 目录中的任何文件。此外，Jest 有一个友好的命令行界面，可以帮助你理解意大利面手指的含义:

[![](img/2926405a0bbe64741c8363ee58a5b80e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PDEUZlw---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrkq4zkwn0nvp9inhsek.png)

当然，这不是一次时间旅行，但它是 Jest 提高生产率和开发人员友好性的另一个基石。

最重要的是小事。

# 测试替身

在自动化测试中，我们编写并执行单元和集成测试，通常的做法是利用不同种类的测试对来隔离系统的不同部分。

有不同的隔离方法，有不同的目标和行为，但都统称为测试替身。

Sinon 等其他库要求您为测试显式声明和选择一种类型的 test double(stub、mock、spy ),而 Jest 将所有内容封装到一个名为 Mock object (jest.fn)的入口点中。

通过测试代码，可以以不同的方式访问和使用 Mock，但本质上，您不需要在测试代码中为测试双精度类型的这种决定而烦恼。这是 Jest 带来的另一个生产力提升。

也就是说，您仍然应该理解测试原则。

> 建议阅读马丁·福勒博客中关于双打测试的内容:[https://martinfowler.com/bliki/TestDouble.html](https://martinfowler.com/bliki/TestDouble.html)

# 沉浸式观看模式

Jest 的观察模式有助于简化您的开发工作流程:

*   显而易见的是——当变化发生时立即运行测试(在 IDE 中，或者说您切换了一个分支)。
*   Jest 会为您决定自动运行哪些测试。它管理关于你的源代码的元数据，所以当一个源代码文件被改变时，它可以学习如何只运行相关的测试文件。
*   Jest 的互动观看模式会告诉你，如果你过滤任何文件类型。例如，如果您使用特定的 glob path 运行 jest，它会将其显示为活动过滤器:

[![](img/bb5fdb58af8393bd403989d6bb78ccb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q32ekc7_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fz7t7mdtngl4nrahgrn3.png)

*   不再 test.only()使它成为您的测试代码，并意外地滑入您的 PR。使用 Jest，您可以直接从控制台通过文件名或测试名轻松过滤测试运行。因此，只需通过测试名称进行过滤，只有当您对测试文件进行更改时，它才会重新运行:

[![](img/9c556611360ff7a9e3a39276405bed8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--opRQYKcf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vaf4iq2o3qiteqeaoeui.png)

您应该知道的关于测试跑步者的其他事情:

*   Jest 将首先运行最慢的测试，以优化并行 CPU 工作并减少总的测试运行时间。
*   Jest 将首先运行之前失败的测试，以提供快速的反馈循环
*   Jest 将选择测试运行的顺序，因此您绝对不应该期望它们会按字母顺序或其他方式运行。对于您来说，它们完全是随机运行的，将测试文件命名为 01 _ loginfuctions . spec . js、02_createUsers.spec.js 是一种不好的做法。

-

那么，您喜欢使用 Jest 时的开发人员体验吗？