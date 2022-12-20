# 停止挥舞魔法数字的魔杖

> 原文：<https://dev.to/mortoray/stop-waving-the-wand-of-magic-numbers-3b1n>

`37`。你不知道那个数字是多少，是吗？没有上下文和标签的数字是一个随机值。它什么也没告诉我们。想象一下，走过一个广告牌，上面画着一个人在船上，文字是一个巨大的数字`89`。我很感兴趣，但也很困惑。我们理所当然地拒绝无意义的数字...

...那么，为什么它们如此频繁地出现在源代码中呢？任何时候一个常数出现在一个表达式中，比如`12`，我们就称之为幻数。通过一些黑暗的飞船，他们从以太中出现并填充我们的代码。我们不知道它们从哪里来，它们意味着什么，或者它们是如何到达那里的。

> 虽然我没有深入到这篇文章的层次，但我的书有一章是关于面试作为一项基本程序员技能的。一些简单的事情，比如固定神奇的数字，可以给面试官留下积极的印象。

## 神奇的例子

当我进行面试时，我会让候选人制作一副卡片。我让他们简化了，用顺序编号的卡片，而不是花色和等级。在大部分代码中，我看到了一个这样的循环:

```
for i in 0..52:
    cards.add( i ) 
```

Enter fullscreen mode Exit fullscreen mode

数字`52`不传达任何信息。仅仅因为我们在谈论一个纸牌游戏，并且候选人已经假设了一副标准的扑克，数字`52`才会出现。当然有很多卡牌游戏没有 52 张牌。

我请求他们把牌分发给多个玩家。我也接受分牌。我经常得到这样的代码:

```
for i in 0..26:
    player1.add( cards[i] )
for i in 26..52:
    player2.add( cards[i] ) 
```

Enter fullscreen mode Exit fullscreen mode

现在还不清楚这是不是把牌分成了两半。我得在脑子里重现那一幕。写作时犯错很容易。如果您看到的代码是:

```
for i in 0..27:
    player1.add( cards[i] )
for i in 28..51:
    player2.add( cards[i] ) 
```

Enter fullscreen mode Exit fullscreen mode

代码是否考虑了一些包含性/排他性的结束条件？这里有一堆一个接一个的错误吗？什么是`51`？它根本不符合我所知道的任何数字，甚至不符合关于卡片的数字。

## 幻数

在此代码:

```
for i in 0...52: 
```

Enter fullscreen mode Exit fullscreen mode

`52`被认为是一个神奇的数字。未来的编码者不知道它是什么。这些类型的数字不传达任何有关其用途的信息。

很容易摆脱他们。给这些数字起个名字。

```
num_cards_in_deck = 52
for i in 0..num_cards_in_deck: 
```

Enter fullscreen mode Exit fullscreen mode

我们立即提高了这段代码的质量。读者知道数字`52`是什么意思。循环现在逻辑上为一副牌中的每张牌做一些事情。

给一个数字起个名字就去除了它的魔力。这里我们已经在代码旁边完成了，但是通常像`num_cards_in_deck`这样的符号最终都是全局常量。它们是与上下文无关的事实。如果多段代码需要相同的值，那么创建一个常量并共享它。

> 另一个解决这个难题的方法是在循环中添加一个注释，比如`# create a standard size deck`。对这个说不！评论是最后的手段。它们没有结构，不能被编译器执行，并且不可避免地变得过时。使用合适的符号名称更好——代码胜过散文。

用我们的新常数，我们可以改变第二个循环:

```
for i in 0..(num_cards_in_deck/2):
    player1.add( cards[i] )
for i in (num_cards_in_deck/2)..num_cards_in_deck:
    player2.add( cards[i] ) 
```

Enter fullscreen mode Exit fullscreen mode

同样，代码的质量也有了很大的提高。这些循环现在有了意义；显而易见，我们把牌分成了两半。它也不太可能出错，因为我们让编译器来做除法。而且，如果卡数变化，这个循环还是正确的。

我不情愿地使用这个例子，只是因为它出现在我的采访中。这两个循环还是不好。从防御性编程的角度来看，您根本不应该使用常量值。你应该用`len(cards)`和`len(cards)/2`。许多幻数可以用这种方式消除。不要把你的代码捆绑到一个任意的值上，而是把它建立在你已经拥有的其他知识的基础上。在这种情况下，我们有一个`cards`集合，其中有我们需要的所有信息。

## 把魔杖放下

然而，并不是所有的常数都是幻数。特别是有一些特殊情况，`1`。将`1`加到一个数上会产生它的自然后继数。对于正在发生的事情不会有任何困惑。还有更多，但没有看到具体的代码，我不希望给出一般的例外。

甚至像`2`这样的数字可能看起来并不神奇，但仍然可以改进。在我给出的例子中，很明显它把某物分成两半，但它没有传达一半的意思。在那个例子中最好说`num_players`。很多时候，即使数字很明显，给它一个名字也是有帮助的。它们增加了代码的清晰度。

尽管魔术可能很有趣，但现在是时候放下欲望，停止在代码中编造这些数字了。

* * *

*看我的书[什么是编程？](http://bit.ly/2GZbNvD)学习成为一名伟大的程序员需要什么。我看着人，软件存在的原因，软件核心的代码，还有你，键盘后面的人。*

* * *

## 附录:更好的交易循环

在我看来，上面显示的交易循环仍然是错误的。虽然不再与幻数相关，但这里的代码以一种更干净的方式在玩家之间进行交易。它避免了重复循环。

```
for i in len(cards):
    player_cards[i % num_players].add( cards[i] ) 
```

Enter fullscreen mode Exit fullscreen mode

另一个可能的选择，更接近模拟交易，完全避免范围。它还使用了一个包含卡片的 OOP 播放器。当您需要对所有状态进行可视化建模时，或者当涉及部分处理时，这种类型的代码是合适的。它保持中间状态。

```
cur_player = 0
while len(cards) != 0:
    player[cur_player].cards.add( cards.pop() )
    cur_player = (cur_player + 1) % num_players 
```

Enter fullscreen mode Exit fullscreen mode