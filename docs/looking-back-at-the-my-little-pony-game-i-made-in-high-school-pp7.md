# 回顾我高中时做的《我的小马》游戏

> 原文：<https://dev.to/meisekimiu/looking-back-at-the-my-little-pony-game-i-made-in-high-school-pp7>

# 背景故事

我要坦白一件事...我曾经是一个“brony”。当“我的小马”热潮达到顶峰时，我才高二。作为一个已经在观看面向小孩子的节目中找到怀旧魅力的青少年，我被整个 brony 的事情吸引住了，在马拉松式地完成整个系列直到我准备好第二季首播后，我开始了众所周知的 brony 习惯，制作我所做的关于那些会说话的卡通马的一切。你可能还记得很久以前，似乎每个人的个人资料图标都是小马，各种小马迷因飞得很快。那是迷因仍然有顶部和底部文本的时代。

[![Pony smiling with 'u mad bro?' in ancient meme text](img/ef0d911a45aed15ea8c47f3126ba3b41.png "thanks i hate it")](https://res.cloudinary.com/practicaldev/image/fetch/s--67tdbJr5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/umadbro.jpg) 

*一个在古代作为模因传递的例子*

总之，在高中的时候，我参加了各种各样的编程课程。“计算机编程”是一门非常基础的课程，正如人们对高中选修编程课的期望一样。它确实给了我第一次机会来简单地玩玩 Linux(Linux 的想法当时让我大吃一惊，但我从未想过我会把它作为我的主要操作系统),而且在我的最终项目中，我有机会做一些非常新颖的事情:只用 HTML 和 JavaScript 制作一个游戏原型...*之前 HTML5 其实是个东西！*

但是真正对我这个程序员产生重大影响的课程是下一级编程课程，“游戏设计与开发”。它更像是一门“计算机编程 II”课程，尽管显然它也主要关注游戏开发。我*喜欢*这门课。我学到了太多的编程基础知识，这些奥莱利动物书籍从未真正教过我(它们教会了我编程语言，但更复杂和抽象的编程概念要么没有被讨论，要么就在我的脑海中掠过)。

[![A parody O'Reilly book cover that reads 'Messy Unorganized Code'](img/5933f4318113e91f38c183ed13f98f00.png "Past me has written some of the worst code future me will ever read")](https://res.cloudinary.com/practicaldev/image/fetch/s--SSjWpGNN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/generate.png) 

*尽管如此，我还是遵循了书中所有的建议*

这门课是我第一次真正接触面向对象的代码。在这门课之前，类和对象的概念看起来更像是一种漂亮的语法糖；一种制作关联数组的奇特方法。当我最终学会了继承和抽象类的魔力时，我被震撼了，为一个我们用基于 Java 的工具 Greenfoot 做的简单游戏做了一个不必要的充实的对象架构。我最初讨厌 Greenfoot，直到我了解了 OOP 的乐趣。当时我甚至写了一个小日志，你可以看到我对它的厌恶发展成“在面向对象编程之前我怎么生活？怎么会？!"。不管怎样，这是我做的游戏的图片:

[![Title screen for my Muffin Mare game](img/8b2e1c2268ec120e245a5bc08a4d02a7.png "Huh. For the original class I had, we made a web portfolio to show off all of our projects. And now I'm doing it again... it's like poetry, it rhymes.")](https://res.cloudinary.com/practicaldev/image/fetch/s--BdkuW97Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/muffinmare_titlescreen.png) 

*后来我在尽量保留原 Java 代码的类结构的同时，用 C++重新制作了这个游戏。没有，只是没有*

这个游戏不是我在这篇博文中将要谈论的我的小马游戏。取而代之的是，我将谈论我在 XNA 游戏工作室制作的课程的最终项目。XNA 给我留下了特别深刻的印象，因为这是我第一次有机会直接从代码制作游戏...框架中没有拖放界面，甚至没有任何实际的“引擎”代码来提供帮助，这真的很令人兴奋。就好像我是一个真正的游戏开发者！

# 代码审核时间

我将在下一篇博文中回顾真实的游戏本身。在这篇文章中，我将回顾我为这个游戏所做的一些实际的工程决策。哦，男孩有很多...在此审查决策。不过简单介绍一下这个游戏，这是一个受 roguelikes 和回合制 JRPGs 启发的游戏。在内部，它一直被称为“PonyRPG”。我在两周内写了大部分代码....作为一个对面向对象架构只有非常天真理解的高中生。哦不。

## 代码问题#1:混乱无序

代码库最突出的问题是它有多乱。代码到处都是，当时我真的不知道任何“干净”的架构设计模式。包含基本游戏循环和绘图循环的“主”游戏类是...**两千条**长的线。这个类中到处都是大量嵌套的“助手类”,它们更像是结构，而不是具有方法的实际类。当时我认为将类拆分到它们自己的文件中是愚蠢的，所以我将许多更小的类塞进使用它们的文件中，然后就这样了。

[![Nested classes in nested classes](img/7373ab9ce8500f27871db184a38211e6.png "Yo dawg I heard you like nested classes so I nested classes in your... eh... you get the punchline")](https://res.cloudinary.com/practicaldev/image/fetch/s--wYOk6kcH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/codesample_1.png) 

*看看我在代码库中使用的各种嵌套类。这些大多都位于海量的主游戏类。*

在游戏的代码库中有几个像这样的巨型类，它们几乎负责游戏逻辑的大部分，并且有大量的子类和随机帮助函数都塞在其中。将所有代码分散到具有单一职责的类中会使代码更加简洁。这些大型类中有许多函数组，可以很容易地提取到它们自己的类中，从而使代码更简洁，更易于调试。

## 代码发布#2:枚举器？我几乎不认识她！

设计游戏架构的一个核心部分是允许游戏以多种状态或“屏幕”存在。标题屏幕、加载屏幕、不同的游戏模式和迷你游戏都是独立游戏状态的例子。您不希望控制标题屏幕的代码出现在主播放器类中。您还希望能够释放游戏状态不再使用的内存。标题画面上显示的游戏 logo，大概应该是在你真正进入游戏后才卸载，只有在玩家选择回到标题画面后才重新加载。

在*《PonyRPG》*中，标题画面图形从未卸载，因为游戏中没有实际的状态管理系统。游戏的状态完全由主游戏类中的一个变量管理，并且每帧检查几次:一个名为“T1”的`Enum`。在主游戏循环中有一堆`if`语句检查我们处于什么游戏模式。因此，我们基本上结束了 4 个游戏循环都挤在一个游戏循环。

[![Using an Enum instead of Polymorphic Dispatch](img/f550e3c601a3e754704bc83f188d7085.png "#REGION CONSIDERED HARMFUL?")](https://res.cloudinary.com/practicaldev/image/fetch/s--n0riK6ZS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/codesample_2.png) 

*不如图:海量的`mode == gameMode.gameplay`“段代码*

幸运的是，这是我唯一用于状态管理的工具...
...
***好的，好的，*** 所以在核心游戏循环中我使用了一个非常相似的设计(反)模式来管理“内在”游戏状态。所以还有另外一个`Enum`来管理玩家的状态:战斗之外，战斗之内，在脚本事件中，使用保存点，等等。...很糟糕。

这些不同的状态应该用实际的类来管理。主游戏循环将只运行活动状态对象的`Update()`函数，通过多态，主游戏类不会真正知道或关心游戏处于什么状态。虽然 Enum 和 Switch 语句实际上功能相同，但是使用多态分派要比包含 T4 容易得多。不是主游戏类到处都有一堆看似随机的变量，而是只有*关心*这些随机变量的游戏状态应该知道它们。我不需要不断地在我的代码中添加另一个`if`或`case`语句，我只需要在代码库中添加一个新类，并在某个地方实例化它。游戏本身可以更新，而根本不需要编辑主游戏循环或其周围的类。

## 代码问题#3:没有剧透！

好吧，下一个问题并没有什么大不了的...好吧，那些之前污染了几乎整个代码库的大规模架构缺陷，但我认为这需要解决！这个游戏是非常数据驱动的，有敌人和关卡参数...几乎所有的东西都是从外部数据文件中载入的。那是*而不是*糟糕的设计部分！我希望能够屏蔽这些数据，不仅防止人们篡改数据和破坏游戏，还防止游戏剧透，例如最终的 boss 战会是什么样子等等。我*非常*保护这个游戏数据，所以我不仅把它嵌入到游戏的可执行文件本身，我还使用了 *AES 加密*来使它不可能被看到，如果你在一个十六进制编辑器中打开可执行文件！

[![hunter2](img/dc243186432454abb6ff672851cee2fa.png "Asynchronous Emotion Stimulator")](https://res.cloudinary.com/practicaldev/image/fetch/s--HUJYghJN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/codesample_3.png) 

*哦上帝，我已经泄露了解密代码，哦上帝现在人们可以破解这个游戏了！！！*

这使得管理数据成为一种痛苦，因为我必须不断地重新加密我想要编辑的任何游戏数据。这种对数据处理的偏执也扩展到了用于保存数据的文件。我基本上使用了加密和一个复杂的“通过模糊实现安全性”格式，该格式有多个校验和(这里我不是在谈论模糊理论)。这种存储保存数据的复杂方法太复杂了，以至于我现在批评的最新版本的代码库...甚至不能加载保存的数据。通过增加越来越多的安全措施，我打破了之前运行良好的保存数据功能！

我认为保护数据不被读取和操纵肯定是有价值的，但我在这个游戏中的做法不仅仅是防止任何人篡改数据。还阻碍了游戏的发展，坏了一个特色！我可能应该保持这种东西更简单，并增加更多的基本检查。

## 也不全是坏事

所以我已经指出了这段可怕代码的一些突出问题。这是否意味着我高中时的自己是个糟糕的程序员？...嗯，算是吧。尽管我觉得重要的是少去想我当时有多糟糕，多想想从那以后我作为一名程序员成长了多少。我是说，我在高中的时候，我怎么会知道我应该把游戏状态想象成下推自动机而不是有限状态机呢？这是我第一个直接从代码制作的游戏项目，没有拖拽。我很自豪我甚至做了一个(大部分)完全可玩的游戏！我想高中的我会为现在的我烘烤这些代码而感到骄傲，也会很好奇那些更“专业”的游戏开发者是如何做好游戏架构的。我的意思是，公平地说，我仍然有很多不知道正确的游戏架构应该如何做。

无论如何，这个代码库的*部分并没有那么糟糕！我之前谈到的数据驱动设计是我最初开发游戏时真正引以为豪的东西！我之前的 XNA 实验使用了完全硬编码的值。这个游戏没有专门的敌人类，只有一个敌人类，这个敌人类被实例化并填充了游戏数据中正确的敌人数据。*

[![xml!](img/a9b069474895ce5d0d36d7a955e0b864.png "Yeah I used XML; you have a problem with that?")](https://res.cloudinary.com/practicaldev/image/fetch/s--9xHwH2Gl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/codesample_4.png) 

*如何为敌人存储数据(未加密 lol)*

代码库的另一个不太坏的部分围绕着游戏中出现的各种对话。`PonyRPG.engine.TextWindow`类可能是整个代码库中设计最好的类。它包括像老的视频游戏一样一次滚动一个字符的文本，正确地包装文本，并且可以通过各种填充和边距设置进行配置。它有一个相关的类，`PonyRPG.engine.TextWindowOptions`,这只是它的各种选项的数据结构...并且它存在于自己单独的文件中，而不是作为一个嵌套的类放在其他地方！虽然这里的代码还可以改进很多，但它绝对是这里最原始的代码...

...它这么好的原因是因为我实际上是在这个项目之外开发的！我最初是为我在 XNA 中开发的另一个与小马相关的游戏开发这个类的，我把它做得尽可能通用，这样我就可以在我制作的其他游戏中重用它。它具有从未在“PonyRPG”项目中使用过的功能，比如在显示对话时显示人物肖像的能力，就像《收获之月》系列中一样。

[![Old game](img/c9aee985b83f75a78d72e83b5a3b4842.png "I don't use Windows XP, this screenshot is from 2012 when I was developing this game!")](https://res.cloudinary.com/practicaldev/image/fetch/s--000GBLcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/dd_alpha_1.png) 

*原项目中的 TextWindow 对象在*中被使用

当我第一次制作这个文本框的时候，我真的感觉很神奇，我可以在我的游戏中插入小的视频游戏对话框！

# 好的...那么游戏实际上是什么样的呢？

Welp，关于游戏的代码我已经说得够多了。我打赌你想知道这个游戏是什么样子的。它实际上叫什么？游戏设计好吗？所有这些乱七八糟的代码仅仅是为了一次奇妙的我的小马游戏体验而做出的牺牲吗？我的下一篇博文将致力于将游戏本身作为一个游戏来回顾，而不仅仅是探究代码的所有问题。留意我个人博客的下一篇文章，会很乱的！

[![Twilight Sparkle...](img/c04d5dba67dcc8abe8bdbd0caba12ee2.png "Hold my cupcakes, I'm going in!")](https://res.cloudinary.com/practicaldev/image/fetch/s--ilMNK27H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.mew151.net/img/9/ponyrpg_title2.png)