# 书呆子的篮球

> 原文：<https://dev.to/teckert/basketball-for-nerds-5bf0>

> 受到 Gayle Laakmann McDowell 的[破解编码采访](https://www.amazon.com/Cracking-Coding-Interview-Programming-Questions/dp/0984782850/ref=sr_1_1?ie=UTF8)中一个问题的启发。
> 也发表在我的[博客](https://thomaseckert.dev/posts/basketball-for-nerds)上。

假设一个朋友向你挑战，让你在篮球场上选择两种游戏中的一种:

1.  你只有一次机会投篮。
2.  你有三次机会投两次或更多次。

你想赢，你知道投篮的确切概率。哪场比赛你胜算更大？

## 赢得第一场比赛的机会

这个游戏包括一次射门机会。因为已知出手的可能性是`p`，所以胜算的几率也是`p`。让我们把这个写成

```
s_1(p) = p 
```

Enter fullscreen mode Exit fullscreen mode

其中`s_1(p)`是赢得第一局的可能性。

## 赢得第二场比赛的机会

在这里，数学变得更加棘手。因为你必须让*至少三次出手中的两次*，所以有四种方法可以赢得这场游戏:

| 第一 | 第二 | 第三 |
| --- | --- | --- |
| 🏀 | 🏀 |  |
|  | 🏀 | 🏀 |
| 🏀 |  | 🏀 |
| 🏀 | 🏀 | 🏀 |

三枪全部命中的概率是第一枪命中的概率*乘以*第二枪命中的概率*乘以*第三枪命中的概率`p^3`。

三投两中的概率是两投概率`p^2`乘以一投不中概率`1 - p`。这种情况有三种可能。如此综合起来，这就是`3p^2(1 - p)`。

我们把投中所有球的概率加上三投两中的概率，得到

```
s_2(p) = p^3 + 3p^2(1 - p) = 3p^2 - 2p^3 
```

Enter fullscreen mode Exit fullscreen mode

## 玩游戏

现在是决定玩哪一个游戏的时候了。赢得每场比赛的机会取决于`p`，我们记得这是一次射门的可能性。我们写点 Python 来根据`p`是什么来选择玩哪个游戏吧。

为了形象化这个函数，让我们使用`matplotlib`和`numpy`。我们运行`pip install matplotlib`，并在代码中编写

```
import matplotlib.pyplot as plt
import numpy as np 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一个函数来计算赢得第一局(单杆)的可能性。这个函数将只返回它的输入，但是把它写下来作为一个函数将会阐明我们的意图。我们还将更好地表达我们的命名约定，这里称之为`p`、`single_shot_probability`和`s_1`、`one_shot_win_probability`。

```
def one_shot_win_probability(single_shot_probability: float) -> float:
    """
    Calculates the probability of winning a game with one chance to make one shot
    from the probability of making a single shot. 
    """
    return single_shot_probability 
```

Enter fullscreen mode Exit fullscreen mode

类似的，我们写一个计算`s_2`的函数叫做`two_of_three_shot_win_probability`。

```
def two_of_three_shot_win_probability(single_shot_probability: float) -> float:
    """
    Calculates the probability of winning a game with three chances to make 
    at least two shots from the probability of making a single shot.
    """
    return 3*single_shot_probability**2 - 2*single_shot_probability**3 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们快速切入 Python。`two_of_three_shot_win_probability`是函数的冗长名称。有时候那是合适的。我认为在一个场景中，代码库中的所有程序员都知道这些函数的意图，这就太冗长了*。它比我通常写的更冗长，也许在一个专业的任意篮球游戏概率公司的代码库中，代码看起来像这样:* 

```
def win_2_by_3_prob(p):
    # Win if 2/3 shots made.
    return 3*p**2 - 2*p**3 
```

Enter fullscreen mode Exit fullscreen mode

见鬼，如果你只需要一个简单的函数，你可以把它写成 lambda:

```
win2by3 = lambda p : 3*p**2 - 2*p**3 
```

Enter fullscreen mode Exit fullscreen mode

计算 2/3 游戏的三种方法都做同样的事情，这是编程的一部分，决定你将如何写它。

既然我们已经有了计算赢得任何一场比赛的概率的函数，我们就可以画出这两个函数与单次投篮的概率的关系。让我们以 0.01 为步长从 0 到 1 改变这种可能性，并使用`matplotlib`绘图。

```
p_range = np.linspace(0, 1, 100)
one_shot_probs = [one_shot_win_probability(p) for p in p_range]
two_by_three_shot_probs = [two_of_three_shot_win_probability(p) for p in p_range]

plt.plot(p_range, one_shot_probs, label="One of One to Win")
plt.plot(p_range, two_by_three_shot_probs, label="Two of Three to Win")
plt.ylabel("Probability of Winning Game")
plt.xlabel("Probability of Making a Single Shot")
plt.legend()
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

总之，我们得到代码

```
import matplotlib.pyplot as plt
import numpy as np

def one_shot_win_probability(single_shot_probability: float) -> float:
    """
    Calculates the probability of winning a game with one chance to make one shot
    """
    return single_shot_probability

def two_of_three_shot_win_probability(single_shot_probability: float) -> float:
    """
    Calculates the probability of winning a game with three chances to make 
    at least two shots from the probability of making a single shot.
    """
    return 3 * single_shot_probability ** 2 - 2 * single_shot_probability ** 3

p_range = np.linspace(0, 1, 100)
one_shot_probs = [one_shot_win_probability(p) for p in p_range]
two_by_three_shot_probs = [two_of_three_shot_win_probability(p) for p in p_range]

plt.plot(p_range, one_shot_probs, label="One of One to Win")
plt.plot(p_range, two_by_three_shot_probs, label="Two of Three to Win")
plt.ylabel("Probability of Winning Game")
plt.xlabel("Probability of Making a Single Shot")
plt.legend()
plt.show() 
```

Enter fullscreen mode Exit fullscreen mode

运行这段代码给我们提供了情节

[![A plot of the likelihood of winning each game.](img/cde5d42b45362e9b1f659a313661bcd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zu1yczN---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/69npj31c7id8klctcwyr.png)

我们可以看到，如果单次出手的概率小于 0.5，那么第一场比赛就是最佳选择。如果这个概率大于 0.5，第二场比赛是最好的。如果我们投中一球的概率正好是 0.5，那么我们赢得任何一场比赛的机会都是一样的。

那么你会玩哪个游戏呢？*