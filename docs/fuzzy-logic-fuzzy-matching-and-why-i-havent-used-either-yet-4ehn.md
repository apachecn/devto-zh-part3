# 模糊逻辑，模糊匹配，还有为什么我两个都没用过(还没！)

> 原文：<https://dev.to/isalevine/fuzzy-logic-fuzzy-matching-and-why-i-havent-used-either-yet-4ehn>

## 1:测量单词之间的距离

想象一下，你可以通过一次改变一个字母来测量两个单词之间的距离，并找出将一个字母转换成另一个字母所需的最少改变。想象一下，每一个变化都会将单词之间的距离推远一英尺，你可以用卷尺测量这个距离:将本田变成现代最少需要 3 个变化，所以想象这些单词之间的距离是 3 英尺。现在，想象你可以做到这一点，不仅仅是单词，而是任何字符串——想象*你能想到的每一个字符串，*一次，全部排列在你面前，每一个都与所有其他的保持完美的距离。

两个字符串之间的距离叫做编辑距离，它是真实的。

[![pikachu gasp!](img/e456f74e75f86c5cd597a289726ec2a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OoM4rw35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u0dnnvqz1whdnuzg0w89.png)

好的，编辑距离*真的很酷*，我*真的真的很兴奋*能深入其中——但是我们需要把它放在上下文中。编辑距离是一个真正的数学度量(它也符合[一些几何定律](http://richardminerich.com/2012/09/levenshtein-distance-and-the-triangle-inequality/))，它被用在许多常见的技术中，比如搜索引擎和拼写检查器。但是编辑距离的真正力量在于它使计算机在处理用户输入时具有灵活性。简而言之，**编辑距离给了计算机一种猜测和假设用户*想要*输入什么的方法。更广泛地说，这是一种被称为“模糊逻辑”的东西的说明——一种不仅根据 0 和 1，而且根据 0.0 和 1.0 之间的数字来评估真假的方法。**

## 2。布尔逻辑与模糊逻辑

回想一下你刚开始学编码的时候(像我！)—常规的 ol '计算机逻辑是如何影响你对世界的看法的？我猜你可能试图用真/假和如果/那么来看待事物，以便练习将现实世界翻译成代码。多方便啊，我们可以问的关于*的每一个问题*都可以用对或错来回答，对吗？

当然不是！现实很乱，即使是简单的问题也能很快陷入灰色地带。考虑两种说法:“Isa 是 5'10”对“Isa 是高的”。这两个都可以回答为真或假。第一个问题也可以通过独立的测量来验证，比如卷尺。(顺便说一句，5 尺 10 的问题是真的。)但是假设我们也认为“伊莎很高”是真的——既然高是定性的，我们怎么*知道，*呢？

[!["isa is 5'10" vs "isa is tall"](img/ec6b21bd4a5464770040bf6b371b36a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oFrKd47i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3s3vmdbus3hafj1wn94t.png)

“模糊逻辑”的核心是它能描述**接近真理**或**真理的可能性。**作为编码生，我们习惯看到布尔逻辑——真/假，是/否，如果/否则，0 或 1。“伊莎身高 5 英尺 10 英寸”这句话是对的，但这并不是描述我的唯一方式。模糊逻辑让我们在不确定的情况下决定“Isa 高”是真是假。相反，额外的背景有助于定义*这句话有多接近真实*——例如，它有助于知道我周围的人有多高！

“Isa 高”可以返回另一个值，比如 0.7，而不是 0 或 1。翻译过来就是:“如果你叫伊莎·塔尔，那有 70%的可能是真的。”

模糊逻辑是一个广泛的概念，对经典逻辑及其编码以外的相关领域具有深远的影响。模糊逻辑由洛夫蒂·扎德博士于 20 世纪 60 年代正式创造，与自然语言的计算机翻译相关，它允许人类以更灵活、不精确和口语化的方式与计算机进行交互。

换句话说，**模糊逻辑使人类能够以一种更加*人性化*的方式与计算机互动。**

## 3。模糊(字符串)匹配

我之所以与模糊逻辑发生冲突，是因为我想在一个命令行界面游戏中增加灵活性，这是我为了好玩而做的。我的 Ruby 井字游戏程序要求用户输入坐标，将他们的标记放在这块板上:

[![tic-tac-toe command line interface](img/8f2ad102fa036341bf22dd4d24b96b2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l3yvr8UM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/syb0ejwokpg8ufbdqg43.png) 
*我用 Ruby 做的第一个井字游戏程序的截图！*

我想让用户输入任意坐标组合——a1，A1，1a，1A——并让它们都有效。因此，我编写了一些代码来检查字符串长度，整数 1/2/3 和字母 a/b/c 的存在，并将所有字母都变成小写。任务完成！

[![arrested development: mission accomplished!](img/a6354459cc60fd49661c4ba70b1969b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a4TA5X4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vignette.wikia.nocookie.net/arresteddevelopmeimg/2/27/2x02_The_One_Where_They_Build_a_House_%2528107%2529.png/revision/latest/scale-to-width-down/1000%3Fcb%3D20121126041516) 
*谢谢，Gob！*

然而，我没有使用真正的模糊逻辑。我实际上是在重写满足某些参数的字符串——每一步都需要布尔逻辑。相反，这段代码展示了模糊逻辑的一个*子集*的简化版本，称为“模糊匹配”，更具体地说是“模糊*字符串*匹配”我的井字游戏不是在寻找一个*精确的*字符串匹配，而是寻找一个足够接近的*字符串。*

这个概念在我的第一个 Flatiron School 编码项目中再次出现——我正在开发另一个包含大量菜单的命令行界面，我希望用户能够输入数字以外的内容来选择菜单选项:

[![menu options on a command line interface](img/b0ac2202a9ed47fdab789d11eb3837e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vzEJb24x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/reii3llzj1t5eqnnflir.png) 
*我的命令行界面为 Flatiron School 的 Ruby 项目。*

因此，我构建了一个 Ruby 类“同义词库”,用数组中包含的一堆硬编码的同义词，并通过诸如`if user_input == “1” || synonyms1_array.include?(user_input)`这样的条件将它们链接到菜单选项:

[![thesaurus program with many arrays of synonyms](img/190e3a520ee39c4509b52062a1380882.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jXfZ1Y09--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7zjf6i8xlnh74wd3l9o6.png) 
*好吧，是的，这不是我写灵活代码最自豪的时刻...*

它起作用了，但是我再次没有使用真正的模糊逻辑——我只是构建了更复杂的条件，最终仍然返回真或假。相反，模糊匹配是对两个字符串有多接近*的一种衡量——如果我有办法衡量我的用户输入与字符串“search”有多接近，我就不需要硬编码单词“search”的每一个排列。要是……*

 *## 4。“亲密度”和编辑距离

…哦，等等，完全有一种方法可以从数学上测量两个弦之间的距离！还记得我们一开始想象的**编辑距离**吗？这是一个奇怪的概念，但是像编辑距离这样的“亲密度测量”来自模糊逻辑，因为它提供了一种使用*定量*方法来回答*定性*问题的方法。“两根弦有多相似？”是一个定性的问题，但如果我们建立数学规则，我们可以用硬数字来解决它——例如，专注于一次只改变一个单独的字符，并将每次改变作为“距离”的度量两个字符串之间最基本的“接近度”是 Levenshtein 距离，或编辑距离。

就像我们在本文开头“想象”的那样，编辑距离检查两个字符串，并描述*将一个字符串转换成另一个字符串所需的最少更改次数。还有其他一些规则:每次“更改”影响一个字符，只能添加、替换或删除字符:*

[![levenshtein distance, from www.cuelogic.com](img/4cb5519b66ac6ec6abc3cc61fb942c68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zzP3KQC9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r3cbftzx0xav075v04rv.png) 
*截图自[www.cuelogic.com](http://www.cuelogic.com)上 Nikhil Babar 的精彩介绍 Levenshtein Distance。*

方便的是，有一种算法可以确定这个*最小变化数*，叫做 Wagner-Fischer 算法——[查看 Nikhil Babar 关于自己运行算法的精彩介绍，在这里找到任意两个字符串之间的编辑距离。](https://www.cuelogic.com/blog/the-levenshtein-algorithm)

但要点是:单词 Honda 和 Hyundai 之间的“距离”是 3，因为这是将一个单词转换成另一个单词所需的最少的单个字符变化。

在应用中，编辑距离是我们日常使用的工具的重要组成部分。搜索引擎使用编辑距离来进行建议的搜索和纠正打字错误。您手机的自动更正功能会使用编辑距离来预测您要键入的单词。拼写检查器使用编辑距离来确定拼写是否错误，并建议正确的拼写。当 Ruby errors 问你“你的意思是……”时，他们使用编辑距离来猜测你是否在方法或对象名中打错了。所有这些都是模糊字符串匹配的例子——换句话说，*真实的*模糊逻辑。

## 5。为什么我还没用过真正的模糊逻辑？(后续步骤和结论)

那么，当我还没有在自己的代码中实际使用过模糊逻辑时，我为什么要写它呢？简答:真的很复杂，但也真的真的很酷！我很幸运周围的人指出我的代码是近似模糊匹配的，因为这使我能够进一步研究它——一旦我这样做了，我就意识到我的字符串操作和同义词库代码只是模糊逻辑的深度和灵活性的粗略近似。

除了在代码中对 Levenshtein Distance 等新概念建模的挑战之外(其他人已经在许多语言中完成了这一点[)，还需要设置参数和阈值来将模糊数据分类为“足够接近真”或“不够接近假”——我通过](http://rosettacode.org/wiki/Levenshtein_distance)[这个 fuse.js 工具处理了这种分类的复杂性，以说明 javascript 中的模糊字符串匹配。](https://fusejs.io/)

因此，我的下一步是深入一些使用模糊逻辑和模糊匹配的源代码，以便我可以从中学习。人们给我指出的几个地方是:

1.  [Atom](https://github.com/atom)的源代码——自动完成多种语言的代码/语法建议

2.  [Ruby](https://github.com/ruby/ruby)的源代码——在出错时提供“您的意思是…”方法/对象名称中的拼写错误的建议

3.  Ruby gems [Searchkick](https://github.com/ankane/searchkick) 和 [Amatch](https://github.com/flori/amatch) ，以及 Ruby library [“模糊字符串匹配”](https://github.com/kiyoka/fuzzy-string-match)——这三个都是使用不同度量和算法实现模糊匹配的 Ruby 代码的例子

我期待着带着更多的实践经验回到这个主题，并对模糊逻辑更广泛的含义有更好的理解！

### 引文和致谢

特别感谢尼基·摩根和熨斗西雅图的指导老师帮助我指出模糊逻辑的方向。

[http://www.levenshtein.net/](http://www.levenshtein.net/)

[https://www.cuelogic.com/blog/the-levenshtein-algorithm](https://www.cuelogic.com/blog/the-levenshtein-algorithm)

[https://medium . com/@ julientregato/an-introduction-to-fuzzy-string-matching-178805 cca2ab](https://medium.com/@julientregoat/an-introduction-to-fuzzy-string-matching-178805cca2ab)

[https://search enterprise ai . tech target . com/definition/fuzzy-logic](https://searchenterpriseai.techtarget.com/definition/fuzzy-logic)

[https://en . Wikipedia . org/wiki/levenstein _ distance](https://en.wikipedia.org/wiki/Levenshtein_distance)

[https://en . Wikipedia . org/wiki/Wagner % E2 % 80% 93 fischer _ algorithm](https://en.wikipedia.org/wiki/Wagner%E2%80%93Fischer_algorithm)

[https://cs . brynmawr . edu/Courses/cs 330/spring 2012/spelling checkers . pdf](https://cs.brynmawr.edu/Courses/cs330/spring2012/SpellingCheckers.pdf)

[http://richardminerich . com/2012/09/levenshtein-distance-and-the-triangle-inequality/](http://richardminerich.com/2012/09/levenshtein-distance-and-the-triangle-inequality/)

[http://rosettacode.org/wiki/Levenshtein_distance](http://rosettacode.org/wiki/Levenshtein_distance)

[https://www.episodate.com/api](https://www.episodate.com/api)(我第一次接触“模糊算法”这个术语！)*