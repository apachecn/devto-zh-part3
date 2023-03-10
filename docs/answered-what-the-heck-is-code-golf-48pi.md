# 回答:什么是代码高尔夫？⛳

> 原文：<https://dev.to/healeycodes/answered-what-the-heck-is-code-golf-48pi>

通常，代码高尔夫意味着两种情况之一:

*   缩短一段代码的行为。

*   互联网上一个非常不知名的社区，他们竞相编写非常短的代码。对一些人来说，他们被视为一个“玩笑开得过火”的群体，但对我来说，他们是**工匠**。

然而，代码高尔夫挑战并不总是关于编写最短的代码。有些比赛以[创意](https://codegolf.stackexchange.com/questions/21835/most-creative-way-to-display-42)评分。有些问题甚至只是在问*这可能吗？* —结果很美。稍后，我们将看到一个只匹配自身的正则表达式。

代码高尔夫比赛最常见的衡量标准是答案所需的字节数，通常可以理解为 UTF-8 字符的数量。`print('Hello, World!')`将是一个 22 字节的答案。就像高尔夫球一样，最低分获胜。

对于“打印你的程序中没有的每一个 ASCII 字符”， [Umbrella](https://codegolf.stackexchange.com/a/15497/78322) 是当前 JavaScript 的领导者，有 84 个字节，相对较长。

```
// Alerts 'bcdfghijkmnpquvxyz6840'
"!#$%&*+,-13:<=>?@[\]^_`{|}~AERTOWS";alert('BCDFGHIJKMNPQUVXYZ'.toLowerCase()+95*72) 
```

让我们看一个极端的例子，GolfScript 条目。语言参考[此处](http://www.golfscript.com/golfscript/quickref.html)。

```
# Outputs the remaining ASCII characters
{`),32>^.}.~ 
```

高尔夫脚本很难理解。大多数时候，我们为人类而不是机器编写代码。在《代码高尔夫》中，人们写作是为了得分。标准编程语言虽然经常被使用，但可能有局限性。例如，我从未见过 C#或 Java 答案在最短答案获胜挑战中名列前茅。

#### 深奥的语言

深奥的语言:

*   奇怪想法的实验。
*   可能很难在中对[编程。](https://esolangs.org/wiki/Unreadable)
*   甚至可能被设计成一个笑话。
*   很可能[不切实际](https://esolangs.org/wiki/JSFuck)。

就像主流编程语言随着时间的推移而演变一样，代码高尔夫语言也是如此。这些语言的设计目标不同。有些人希望能够编写最小的程序。有些写[只是因为](http://esolangs.org/wiki/Shakespeare)。

[Hexagony](https://github.com/m-ender/hexagony) 是一种二维编程语言。让我们来看一个打印 Hello，World！’的程序。你可以在[TiO . run](https://tio.run/##y0itSEzPz6v8/19BQcFDwVohVcGaS0EhB8hKAWItLiBRBMT5QBzOBRKGsLUUTLgUjIHQWsFBQU/BDqjHWsEIyLdRiAGyFUzAavT//wcA)a . k . a . a .在线运行这个程序。在线尝试一下——一个 **636** 语言的开源平台。

```
 H ; e ;
  l ; d ; *
 ; r ; o ; W
l ; ; o ; * 4
 3 3 ; @ . >
  ; 2 3 < \
   4 ; * / 
```

好奇吗？我最喜欢的一段高尔夫代码是语言创建者对他的素性测试程序的描述。阅读这些解决方案，并尝试自己的解决方案，锻炼了我解决问题的能力。它帮助我了解我的语言的边缘。

#### 社区

与开源社区类似，code golf 通常是一项协作工作。语言是在开放中发展的，问题会找到有用的答案。竞赛“参赛作品”也将得到礼貌的帮助。我记得我第一次尝试 code golf 时，我收到了欢迎消息和减少字节数的建议。

StackExchange 的[编程难题& Code Golf](https://codegolf.stackexchange.com/) 部分是互联网上最活跃的 Code Golf 社区，在这里你可以找到许多正在使用的语言。 [Esolangs](https://esolangs.org/wiki/Main_Page) 是一个社区 wiki，其中深入介绍了最深奥的语言。Twitter 上的#codegolf 标签很慢，但很受欢迎。

#### 只匹配自身的正则表达式

正如承诺的那样，[这篇](https://codegolf.stackexchange.com/questions/28821/regex-that-only-matches-itself)帖子很有趣。海报首先声明*这很可能是不可能的*，然后详细阐述了挑战。幸运的是， [jimmy23013](https://codegolf.stackexchange.com/users/25180/jimmy23013) 向我们展示了这个挑战不仅是可能的，而且是可以非常简单地解决的。

```
# RegEx that only matches itself
# in (PCRE) Perl Compatible Regular Expressions
<^<()(?R){2}>\z|\1\Q^<()(?R){2}>\z|\1\Q>

# A more widely compatible version:
/^\/()(?R){2}\/\z|\1\Q^\/()(?R){2}\/\z|\1\Q/ 
```

#### 这个数是质数吗？

先说一个 Python [回答](https://codegolf.stackexchange.com/a/57650/78322) (59 字节)。这就像质数检查一样幼稚，但却缺乏创造性。它展示了一个典型的 code golf 答案:*解决问题，然后应用语法技巧*。(评论我自己的)。

```
# take a number via stdin n=int(input())
# build an array of this number's factors from 1 to n
# if there is only one factor (the number one) then it is prime! print([i for i in range(1,n)if n%i==0]==[1]) 
```

JavaScript 的主要答案有点难以区分。

```
// Alert true or false given an input number
alert(!/^(?!(..+)\1+$)../.test(prompt())) 
```

[海报](https://codegolf.stackexchange.com/a/57692/78322)告诉我们它使用了一个“很酷的一元正则表达式来确定素性”。进一步研究发现[这篇](https://iluxonchik.github.io/regular-expression-check-if-number-is-prime/) ~5000 字的文章解释了说正则表达式。您可能已经开始意识到，代码高尔夫就像编程一样，是一个没有尽头的兔子洞。但是你越深入，回报就越多。

#### 网络上的其他地方

JS1k 是一个 JavaScript 演示程序，与 1024 字节以下的“花哨的 JavaScript 演示程序”竞争。它们给人留下了非常深刻的视觉印象。

还有始于 1984 年的国际模糊 C 代码竞赛。虽然它比高尔夫球更接近寻宝！这是卡洛斯·杜阿尔特于 1995 年创作的迷宫生成器作品([来源](https://www.ioccc.org/years.html#1995_cdua)):

[![1995/cdua](img/349fd04901c16faa7fdf3f4f1aa42f32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CKJl5xzO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uv0hztfowvptckm4x5jg.png)

向我最喜欢的两个 esolang[05a B1](https://github.com/Adriandmen/05AB1E)和 [Jelly](https://github.com/DennisMitchell/jellylanguage) 致以荣誉。它们都在挑战中得到了很好的体现，并且通常伴随着精心编写的解释，这是学习它们的一个很好的切入点。

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。