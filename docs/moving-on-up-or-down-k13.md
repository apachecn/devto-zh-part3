# 向上(或向下)移动

> 原文：<https://dev.to/zmdominguez/moving-on-up-or-down-k13>

<center>

> ![unknown tweet media content](img/3999bff3c7268384255da68eb7a6b940.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D3MGtFoUIAAeObm.mp4" type="video/mp4"></video>![Zarah Dominguez 🦉 profile image](img/3e356e2a494586eef648535b4c5142b5.png)扎拉·多明戈斯🦉@ zarahjutz![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)对枚举中的元素重新排序时使用 CMD+SHIFT+UP/DOWN 表示分号。😍这是小事！00:52am-03 2019 年 4 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1113242947460325376)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1113242947460325376)17[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1113242947460325376)129

</center>

我今天早上在重新发现 CMD+SHIFT+UP 后发了这条微博。我知道这条捷径已经有一段时间了，但我想我直到最近才有理由使用它。

然后我想起了这个快捷方式的一个变体，字面意思是“移动不关心的东西”。

<center>

> ![unknown tweet media content](img/bffc12746f25d57174f1a6f3fae4df91.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D3M60E5V4AAKMSQ.mp4" type="video/mp4"></video>![Zarah Dominguez 🦉 profile image](img/3e356e2a494586eef648535b4c5142b5.png)扎拉·多明戈斯🦉@ zarahjutz![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)编辑:(vs SHIFT+ALT+UP/DOWN 只移动行而不考虑上下文)04:32am-03 Apr 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1113298274838929408)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1113298274838929408)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1113298274838929408)9

</center>

这是 IntelliJ，这些动作不仅仅作用于属性。它们基本上适用于代码中任何有作用域的东西——单独的行、函数，甚至是类！

有两个主要的移动动作，*移动语句*和*移动线*。取决于你想做什么，这些都有各自的好处。

移动一个*语句*意味着 IDE 将考虑特定元素所在的上下文。在单行代码的情况下，这意味着维护其当前范围。这意味着——如果一行代码在函数内部，使用 CMD+SHIFT+UP/DOWN,*永远不会*将它移出函数。

也就是说:

[![](img/deebe82347955d5b0238b704606ea786.png)](///assets/moving_up/cmd_shift_line.gif)

但是这一行想挪到另一个功能！这是它自己说的！这时 CMD+ALT+UP/DOWN 就派上用场了:

[![](img/37782afa0cfea7a134dedb30e2a6d0ce.png)](///assets/moving_up/cmd_alt_line_2.gif)

当重新排序或重构函数时，这个快捷方式非常方便:

[![](img/67d491b09eb50b887de4743c989e6a42.png)](///assets/moving_up/cmd_shift_fun.gif)

我把它作为一个练习留给读者，让他们看看快捷方式如何处理类。动起来，停止复制粘贴！👯