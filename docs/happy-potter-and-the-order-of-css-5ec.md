# 《哈利·波特与 CSS 的秩序》

> 原文：<https://dev.to/thekashey/happy-potter-and-the-order-of-css-5ec>

首先，这不是一场普通的战斗。不是关于`CSS`对`CSS-in-JS`，不是`atomic CSS`对`BEM`，不是`LESS`对`SASS`。这场战斗是关于秩序的。

这场战斗是秩序与混乱的较量。在秩序和之间，...*另一个*订单。简而言之——关于 CSS **属性**的不同排序方式。不多不少。

简而言之，有三种方法可以做到:

*   不要这样做。尽可能写任何 CSS。这是最流行的方式
*   按感觉对属性进行分组。只是把一些东西放在一起。这是相当流行的方式。
*   按字母顺序排列所有属性。最简单但不太受欢迎的方法。

> 从现在开始，让我们忘记第一种方式。这不是办法。

### 2012 年

css-tricks 有一个很好的投票，结果如下:

[![poll results](img/3eb7bb42e9988083eb1d3044de3058ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XlxuZ1VJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/erl34nrwpmphg3uvikhd.png)

回到 2012 年，“分组排序”更受欢迎，但现在已经是 2019 年了，有什么变化吗？

alpha(betical)、众所周知的 **ABCSS** 和 **group** 之间的区别很明显，但最好让这种区别更清楚，并再次定义这些术语。

### 初始状态

让我们从这期[更漂亮的](https://github.com/prettier/prettier/issues/1963)
的理想混乱开始

```
.wrapper {
    top: 20px; /* is it absolute? fixed? You begin searching downwards */
    margin-left: 20px; /* Any other margins applied? */
    display: flex; /* How is this flexed?  searching for justify / align / flex rules */
    position: absolute; /* ah absolute */
    height: 100%;        /* and width? */
    margin-bottom: 20px;
    border-radius: 5px;  /* Is there even a border? */
    color: red;
    justify-content: center;
    margin-left: 2px;
    left: 0px;
    width: 100%;        /* and height? */
    border: 1px solid red;
} 
```

Enter fullscreen mode Exit fullscreen mode

漂亮？这就是普通 CSS 几年后没有任何控制的样子。

### ABCSS

按字母顺序排序

```
.wrapper {
    border: 1px solid red;
    border-radius: 5px;
    color: red;
    display: flex;
    height: 100%;
    justify-content: center;
    left: 0px;
    margin-bottom: 20px;
    margin-left: 20px;
    margin-left: 2px;   /* ouch? */
    position: absolute;
    top: 20px;
    width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

它只是帮助我们注意到我们有两个`margin-lefts`。下次添加新属性会更容易，特别是如果所有的排序都是在`stylelint`级自动完成的- **你不必自己排序**(这是一个非常**大胆的**声明！).

> 字母排序的主要**优势**-更容易的物业定位。你只是在浏览一个表格，比如一个词汇表，没错，这就是我正在寻找的属性。

主要**缺点** - `height`先于`width`，`left`先于`top`，两者之间还有一段*距离*。在`height`和`width`之间，甚至还有`left`和`top`之间的`position:absolte`。

字母排序是**散射上下文**。
但是如果你有*短*发型，比如比上面例子中短两倍，这不是问题——复杂性来自于尺寸。

> 就我个人而言，没有分类我无法生活，因为当我处理宽度时，我需要高度尽可能接近，而不是空白；我需要在位置后右下方，而不是在边框后；字体在接近浮动时是没用的，等等。瓦迪姆·马克耶夫

值得一提的是——字母排序对于大的样式来说效果不好，但是对于小的样式来说却很方便。原子的或功能性的 CSS 非常适合他们。

> 在原子 CSS 中，你创建了无数的小类，每一个都是关于小的“东西”。比如位置、颜色、大小或边框半径。你知道——原子 CSS 是关于结果的分离，以及关于**将事物组合在一起**。

我用了一些在线工具来制作它。大量的在线工具、ide 插件和 CLI 工具都支持 alpha sort out of the box。👍-易于使用。虽然它很容易使用。以后不容易。

### 组

按组、组群、桶、簇分类。分成多个订单...

```
.wrapper{
    position: absolute;
    top: 20px;
    left: 0;

    display: flex;
    justify-content: center;

    width: 100%;
    height: 100%;
    margin-bottom: 20px;
    margin-left: 20px;
    margin-left: 2px;

    color: red;

    border: 1px solid red;
    border-radius: 5px; 
} 
```

Enter fullscreen mode Exit fullscreen mode

这次更多的是关于`separation of concerns`——所有的属性都被分组(聚集)到一个桶中。排序原则被称为“由外向内”:

*   布局属性(位置、浮动、清除、显示)
*   方框模型属性(宽度、高度、边距、填充)
*   视觉属性(颜色、背景、边框、方框阴影)
*   排版属性(字体大小、字体系列、文本对齐、文本转换)
*   杂项属性(光标、溢出、z 向索引)

问题——围绕它的**不止一个标准**。更具体地说，不止五个。这是个问题。还是没有？

> 同样，排序本身是由工具 [CSSCOMB](http://csscomb.com) 自动完成的。

主要**优势**——所有属性按常识分组。所有 flex 属性-一起。所有位置，左，右，上，下，一起。颜色？-一起！

*   如果你需要添加一些东西——在某个地方添加，让工具来完成这项工作。
*   如果你需要寻找什么——寻找一个群体，然后在群体内部寻找。顺便说一下，这比之前的线性搜索要快得多。(真的- O(logn) vs O(n))

> 这需要一些澄清:
> 
> *   与阿尔法排序-你正在寻找一行的开始，指出开始和结束“你的”第一个字母。然后你要穿过这个子块，就完成了。那基本就是 O(n)了。经过一些训练- O(n/2)，只要你知道“在哪里”寻找道具。
> *   通过分组排序——你知道在“哪里”寻找一个道具——分组有一个固定的位置，然后遍历小的子集，这就完成了。那基本就是- O(3)，连“log n”都算不上。

主要的缺点是你必须学会如何分离结果。99%的受访者表示这是一个大问题——编写*分组* CSS。但这是工具工作，不是你的。所以根本不是问题。

> 是的，有一个以上的标准，但这不是一个问题，然后订购是由一个工具完成的。和漂亮女孩一样的问题。

#### 分组内排序

集团内部的排序几乎是**与**的字母顺序。它总是按字母顺序排列，除了左/右/上/下/宽/高，这些是*设定的期望*的主题，就像`x`应该在`y`之前。

## 民意测验

> ![Anton Korzunov profile image](img/951d3be8c74f47ad2b91e2cc0080bd91.png)安东·科尔祖诺夫[@ the kashey](https://dev.to/thekashey)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)需要强烈意见——如何订购 CSS 属性。
> -按字母顺序，只要“任何人都可以做”
> -分组，按感觉分组道具
> -没有顺序，我正在写 CSS(我的最爱)
> 
> 2012 年的一次民意调查-[css-tricks.com/poll-results-h…](https://t.co/ZkY6irv60U)
> 
> /cc[@ markdalgleish](https://twitter.com/markdalgleish)， [@pepelsbey_](https://twitter.com/pepelsbey_) ，[@ sitnikcode](https://twitter.com/sitnikcode)21

所以，我围绕这个问题做了一些研究。

*   2012 年，css-tricks 报告 45%的开发者使用组，14 个字母排序，39 个什么都不用
*   今天创建的民意调查(尚未完成)- 50%的群体，25%的阿尔法，25%不使用任何东西

#### 使用群组的理由:

*   分组是把东西放在一起的最自然的方式:这就是你写它们的方式。想象一下，如果你必须按字母顺序在一个句子中写单词。上、右、下、左自然属于一起，就在位置之后:绝对

    > ![Vadim Makeev profile image](img/242df96c6fd5471461a9ef2c23292696.png)瓦迪姆·马克耶夫@ pepelsbey _![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ theKashey](https://twitter.com/theKashey)[@ markdalgleish](https://twitter.com/markdalgleish)[@ sitnikcode](https://twitter.com/sitnikcode)分组是把事情放在一起的最自然的方式:那就是你写它们的方式。想象一下，如果你必须按字母顺序在一个句子中写单词。上、右、下、左自然归属在一起，位置之后刚好:绝对。2019 年 3 月 23 日 12 点- 24 日[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1109956283929714690)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1109956283929714690)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1109956283929714690)

*   组的一个很好的理由可能是原子 CSS——atom/utility 通常被绑定到一个“组”上，这是形成功能框架的自然方式。所以“普通的”CSS 可能看起来像组合的原子——即分组的

    > ![Anton Korzunov profile image](img/951d3be8c74f47ad2b91e2cc0080bd91.png)安东·科尔祖诺夫[@ the kashey](https://dev.to/thekashey)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ pepelsbey _](https://twitter.com/pepelsbey_)[@ markdalgleish](https://twitter.com/markdalgleish)[@ sitnikcode](https://twitter.com/sitnikcode)组可以是原子 CSS 的一个很好的理由——atom/utility 通常绑定到一个组，所以这是一种自然的方式。
    > 
    > 所以“正常的”CSS 可以像原子一样组合起来——或者分组。
    > 
    > [@ adamwathan](https://twitter.com/adamwathan)——我没看错吧？2019 年 3 月 24 日 22:21PM[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1109943404723466242)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1109943404723466242)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1109943404723466242)

*   较小的团队可能更喜欢将相关属性(例如定位和框模型)聚集在一起[惯用 css](https://github.com/necolas/idiomatic-css#declaration-order) 。

*   两种方式都有利弊。一方面，字母顺序更加通用(至少对于使用拉丁字母的语言来说是这样)，所以对于先排序一个属性再排序另一个属性没有争议。然而，对我来说，看到诸如 bottom 和 top 这样的属性不在一起是非常奇怪的。为什么动画应该出现在显示类型之前？字母顺序有很多奇怪的地方。另一方面，按类型对属性进行排序非常有意义。每个与字体相关的声明都被收集起来，顶部和底部被重新组合，阅读一个规则集有点像阅读一个短篇故事。[萨斯指南](https://sass-guidelin.es/#naming-conventions)。

*   gzip 后，分组排序的文件缩小了 2%。魔法。有一篇文章，从压缩的角度分析了 5 种不同的 css 道具排序方式。[阅读研究](http://peteschuster.com/2014/12/reduce-file-size-css-sorting/)

> 是的，gzip 可以根据排序进行压缩，这没问题。

#### 不使用组的原因

*   “群体”是伟大的，如果它对每个人都意味着同样的事情。通常不会。你也许可以自动执行分组，然后你就扯平了。

    > ![Adam Ahmed profile image](img/3bae2563dc5b7794dcc939b656d0156c.png)亚当艾哈迈德@ types things![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ the kashey](https://twitter.com/theKashey)[@ markdalgleish](https://twitter.com/markdalgleish)[@ pepelsbey _](https://twitter.com/pepelsbey_)[@ sitnikcode](https://twitter.com/sitnikcode)“群体”如果对每个人来说都意味着同样的事情，那就太棒了。通常不会。你也许可以自动执行分组，然后你就扯平了。我个人也喜欢“whatever”tho，因为庞大笨拙的规则糟透了，即使它们在整个代码库中是一致的。2019 年 3 月 24 日 23 点 47 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1109964896874033152)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1109964896874033152)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1109964896874033152)

*   大型团队可能更喜欢字母排序带来的简单性和易维护性。[又一次惯用——CSS](https://github.com/necolas/idiomatic-css#declaration-order)

#### 使用字母顺序的理由:

*   我过去常常按“组”来做，但这太主观了。此外，还有大量的插件来对一组行进行阿尔法排序。Chrome 开发工具`computed style`也是 alpha 版本。(*失去参考*)
*   按字母顺序。其他任何事情都意味着任何参与这个项目的人也需要学习你的模式。
*   我自己的公司正在使用 alpha 排序，没有任何解释(这对我来说是一个惊喜)。
*   很容易 lint，有工具可以自动格式化，没有学习曲线。
*   编辑 CSS 文件时，查找要更改的声明可能很耗时。为了减少阅读和书写规则时的认知负荷，按字母顺序书写。这是唯一经得起未来考验的排序规则，因为它没有观点。当您在一个规则中有超过 10 个 CSS 声明时，您会很高兴知道在哪里可以在一瞬间找到您要找的东西！(CSS 在 44 分钟内)
*   [谷歌 css 指南](https://google.github.io/styleguide/htmlcssguide.html#Declaration_Order)推荐 ABCSS。>基于我在许多不同环境中多年的经验，我只能，并且强烈地，推荐按字母顺序对声明进行排序。我们可以把事情简单化。另一个对我们来说意味着很少工作的选择就是不分类。不排序是没有用的，因为结构化代码更易于管理和维护。[关于 CSS 中的声明排序](https://meiert.com/en/blog/on-declaration-sorting/)

### 使用的术语

*   **认知负荷**。公平地说，如果有工具可以对代码进行分类，那么编写代码的“认知负荷”将为零，如果分组不会给你留下 10(20)行总是不同的 CSS 代码，那么这一负荷将会更低。我们已经讨论过“复杂性”，这是很清楚的，对群体来说是较低的。

    *   “无需解释”。如果你不能解释为什么某样东西比另一样东西好或坏，你只能凭经验判断。这不是坏事，这可能只是你的直觉，但不是在这种情况下。

顺便说一句，关于谷歌

> ![Vadim Makeev profile image](img/242df96c6fd5471461a9ef2c23292696.png)瓦迪姆·马克耶夫@ pepelsbey _![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ the kashey](https://twitter.com/theKashey)[@ fcorradini](https://twitter.com/fcorradini)[@ PostCSS](https://twitter.com/PostCSS)[@ markdalgleish](https://twitter.com/markdalgleish)[@ sitnikcode](https://twitter.com/sitnikcode)谷歌的风格指南充满了反模式，我总是告诉我的学生忘掉它。这并不是说整个公司都同意这一点，或者它得到了维护。关于这件事，我会问网上的人。2019 年 3 月 25 日上午 07:07[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1110075645726478336)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1110075645726478336)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1110075645726478336)

那么，TLDR

*   我们大多数人(通常包括我在内)都不在乎*订购*CSS 道具。我们大多数人也不在乎 CSS 本身。你知道——[大分水岭](https://css-tricks.com/the-great-divide/)。
*   JS 中的 CSS 也不太鼓励最佳实践。
*   像 atomic CSS 这样的最佳实践是组的最佳选择(通过设计)，而 BEM，不幸的是，没有将属性排序作为其标准的一部分(除了 B(大小)和 E(样式)分离) :(

这实际上有点出乎意料，这种固执己见的东西没有很好地建立和定义，我不得不写这篇文章。

> 有趣的事实组和字母顺序可能是最好的，例如 lint 你的模块导入，像 [eslint-plugin-import](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/order.md)
> 首先有组，字母顺序只在一个组内使用。

*   组的样式 lint:[https://github . com/const verum/stylelint-config-rational-order](https://github.com/constverum/stylelint-config-rational-order)(或 csscomb)
*   阿尔法排序的样式线头:[https://github.com/hudochenkov/stylelint-order](https://github.com/hudochenkov/stylelint-order)

到目前为止，我听到了相当合理的论点，但没有一个(对我来说)是合理的。

另请参见:

*   (关于所有变体)[https://media temple . net/blog/tips/different-logical-ways-group-CSS-properties/](https://mediatemple.net/blog/tips/different-logical-ways-group-css-properties/)
*   (关于群组)[https://web design . tuts plus . com/articles/outside-in-ordering-CSS-properties-by-importance-CMS-21685](https://webdesign.tutsplus.com/articles/outside-in-ordering-css-properties-by-importance--cms-21685)
*   (关于 abcss)[https://medium . com/@ jerrylowm/alphabetize-your-CSS-properties-for-loud-780 EB 1852153](https://medium.com/@jerrylowm/alphabetize-your-css-properties-for-crying-out-loud-780eb1852153)

选择你的立场，并解释你的立场。我有一些爆米花要分享。

> 我自己的看法...是关于模式匹配。人类擅长这个。ABCSS 很好学，但是有什么模式可以搭配吗？团队可能很难学习，但这都是关于模式的...你可以匹配。不要想今天，想想明天。是的- **认为**，不要盲目使用。

# 结论

这对你来说有点出乎意料，因为**排序并不重要**。一点也不。我们在讨论点餐是一种...为了什么？为了什么？我们试图解决的订购问题。

## 什么问题？又来了！

*   小款式有什么问题吗？大概 1-3 条规则那么长？很明显——**没有**。
*   大规则有什么问题吗？比如 30 多条规则那么长？哦是的！
*   这中间有什么问题吗？当然可以。

> 复杂伴随着规模。只有大小才重要。

为什么一定要有大款？你不知道。没有理由要大款。一旦你创造了一个大东西——执行**分解**，然后**将它分割**成碎片，或助手，或原子——**分块吃熊**。

*   让我们想象你正在使用`SASS/LESS`——只是提取一些常见的东西来混合。把所有款式的东西都拿走，然后重新组装起来。
*   您使用的是[SMACSS](https://css-tricks.com/methods-organize-css/#article-header-id-1)——井的样式被关注点自然地分开。
*   BEM？b 代表尺寸，E 代表其他一切——结果的分离。
*   原子 CSS(像自举，超光速粒子)-每个原子都包含“一个东西”，小而健全的东西。
*   分子 CSS(像 bulma 一样，今天才发明了这个术语)——每一种风格都是在分子上应用原子操作。

> 它总是关于两件事——关注点的分离，以及尽可能把事情做得小。

分组也是如此，而字母排序让你的样式变得又大又难管理。它根本没有解决“真正的问题”。

让我们尽量避免“真正的”问题

```
// grouped
.wrapper{
    position: absolute;
    top: 20px;
    left: 0;

    display: flex;
    justify-content: center;

    width: 100%;
    height: 100%;
    margin-bottom: 20px;
    margin-left: 20px;
    margin-left: 2px;

    color: red;

    border: 1px solid red;
    border-radius: 5px; 
}

.wrapper{
    position: absolute;
    top: 20px;
    left: 0;

-    display: flex;
-    justify-content: center; +    FLEX-CENTER 
-    width: 100%;
-    height: 100%; +    ALL-SPACE 
-    margin-bottom: 20px;
-    margin-left: 20px;
-    margin-left: 2px; +   MARGIN-BETWEEN // margin-bottom: 20px; 
    color: red;

-    border: 1px solid red;
-    border-radius: 5px; +    BORDER-RED
+    BORDER-ROUND }

.wrapper{
    position: absolute;
    top: 20px;
    left: 0;

    FLEX-CENTER

    ALL-SPACE

    MARGIN-BETWEEN

    color: red;

    BORDER-RED
    BORDER-ROUND
} 
```

Enter fullscreen mode Exit fullscreen mode

这仍然是一种定制风格，但是我已经用`things`(任何种类的混合)代替了“普通”风格。顺便说一句，一件好事应该只影响一个群体的财产。这次很容易吗？

> 当我解释 CSS 中类型排序的问题时，我意识到类型排序者的动机是一个特殊的问题，而不仅仅是偏好。这是一些更基本的东西，一些让我们 web 开发者更头疼的东西。潜在的问题是人们在 CSS 中重复自己太频繁了——或者重复错误的代码太频繁了。 [CSS、DRY 和代码优化](https://meiert.com/en/blog/css-dry-and-optimization/)

*   弄一个大款式
*   分成“物”，群体就是好的东西。
*   提取公共

# 脚本

添加:

*   `lint-staged`-npx MRM lint-暂存
*   增加`stylelint`、`stylelint-config-rational-order`和`stylelint-order`

*   提交时配置林挺

```
// package.json
"lint-staged": {
    "*.scss": ["stylelint --fix", "git add"],
} 
```

Enter fullscreen mode Exit fullscreen mode

*   配置 stylelint

```
// .stylelintrc
{
  "plugins": [
    "stylelint-order",
    "stylelint-config-rational-order/plugin" // remove to keep only alpha sort
  ],
  "rules": {
    "order/properties-order": [],
    "plugin/rational-order": [
      true, {
      "border-in-box-model": false,
      "empty-line-between-groups": true
    }]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者使用`stylelint-semantic-groups`

*   `yarn add stylelint-order stylelint-semantic-groups`

```
const { semanticOrdering } = require('stylelint-semantic-groups');

module.exports = {
  plugins: ['stylelint-order'],
  rules: {
    /* optional by recommended */
    'order/order': [
      'custom-properties',
      'dollar-variables',
      'declarations',
      'at-rules', // <-- important, `@media` should go before `&:pseudo`
      'rules',
    ],
    /* the actual usage of this package */
    'order/properties-order': semanticOrdering,
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode