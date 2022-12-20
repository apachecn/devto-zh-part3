# 测试……提炼代码时的安全网。

> 原文：<https://dev.to/lancew/tests-your-safety-net-when-refining-you-code-2hjb>

任何了解我的开发风格的人都会知道，我的个人方法是非常面向测试的，我倾向于首先冗长，然后在代码工作后精炼代码以使其更干净和/或更习惯……并且它被证明是有效的。

那么它看起来像什么呢？

最近，在我的$dayjob 中，我必须循环访问一个数组，并在将该数组返回到另一个逻辑之前应用一个修改。这是我解决这个问题的粗略方法，但是用一段基于令人敬畏的[TrueAchievements.com 十二天圣诞挑战](https://www.trueachievements.com/event/Twelve-Days-Of-Christmas-2018?utm_source=rss&utm_medium=rss)的假想代码替换$dayjob。

```
# 12_days_of_christmas.t

use strict;
use warnings;

use Test2::V0 -target => 'TA';

isa_ok $CLASS, 'TA';
can_ok $CLASS, 'TwelveDaysOfChristmas';

done_testing; 
```

Enter fullscreen mode Exit fullscreen mode

是的，就从一个简单的测试开始，这个测试确认我的模块/方法存在:

```
$ prove -lv
t/12_days_of_christmas.t .. Can't locate TA.pm in @INC 
```

Enter fullscreen mode Exit fullscreen mode

上面是 Perl 告诉我，“嘿，Lance，你实际创建那个文件怎么样，我的孩子”。所以我继续这样做:

```
#TA.pm

use strict;
use warnings;

sub TwelveDaysOfchristmas {}

1; 
```

Enter fullscreen mode Exit fullscreen mode

你可以说我已经创建了这个库的框架，而我的子例程实际上什么也没做。所以在这个阶段，我按下向上箭头并返回，重新运行“ *prove -lv* ”，得到我:

```
$ prove -lv
t/12_days_of_christmas.t ..
ok 1 - TA->isa('TA)
not ok 2 - TA->can('TwelveDaysOfChristmas') 
```

Enter fullscreen mode Exit fullscreen mode

是的…你可能已经发现了打字错误，我没有键入子程序的名字和" *can_ok* "测试中的名字相同。我是一个糟糕的打字员，我总是犯这样的错误。所以我真的倾向于尽早地、经常地编写这样的测试。我一直在运行它们(有时用 Linux 的" [watch](https://linux.die.net/man/1/watch?utm_source=rss&utm_medium=rss) "命令),这样我就能得到最快的警告，我做了一些我力所能及的蠢事。

这也暗示了我，我天生的冗长在这里伤害了我，所以我可以做一点小小的改变，在测试中缩短子名称，然后模块和一切都很好，我的终端显示:

```
$prove -lv
t/12_days_of_christmas.t ..
ok 1 - TA->isa('TA')
ok 2 - TA->can('TwelveDaysOfChristmas')
1..2
ok
All tests successful 
```

Enter fullscreen mode Exit fullscreen mode

显然，在这个例子中，这一切都有点做作；实际上，在我的$dayjob 中，我正在给对象添加方法。所以我经常只是添加一个方法的基本测试。但是当我连接一个新的服务(通过域对象等从控制器链接到基础设施类)时。)然后，我确实喜欢以类似这样的测试开始，证明我已经正确地连接了所有的东西。我想知道，在我开始构建这个方法之前，我可以从控制器级别调用这个方法。

所以…我希望我的新 twelvedaysofschristmas()sub 循环一系列 XBox 成就，并将它们全部设置为“done”。所以我们走吧…

```
my $got = TA::TwelveDaysOfChristmas(
    [
        ['Foo',''],
        ['Bar',''],
    ]
);

is $got, 
    [
        ['Foo','Done'],
        ['Bar','Done'],
    ]
,
'Correctly set the elements to DONE'; 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经猜到了，我再次运行了测试，得到了这个(为了简洁而编辑的)测试输出:

```
# +---------+---------+
# | GOT | CHECK |
# +---------+---------+
# | <UNDEF> | <ARRAY> |
# +---------+---------+ 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，测试失败了，因为我想要一个数组，而当前 sub 是空的，所以它返回 undef。接下来我会经常放上我能放的最少的返回值，比如“*return 666；*“这样我就能看到测试失败的变化:

```
# +-----+---------+
# | GOT | CHECK |
# +-----+---------+
# | 666 | <ARRAY> |
# +-----+---------+ 
```

Enter fullscreen mode Exit fullscreen mode

同样，这只是一个小的健全性测试，我在 sub 中做了一个更改，测试结果向我这个开发者证明了我仍然在控制中。 [![🙂](img/610185ba910649e2d0e3c5889bf265e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAUB0ZGn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png%3Futm_source%3Drss%26utm_medium%3Drss) 那么接下来我通常会加上什么:

```
sub TwelveDaysOfChristmas {
    return [
        ['Foo','Done'],
        ['Bar','Done'],
    ];
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得所有的测试都通过了，尽管这完全是无用的代码。但是现在我已经完全确定了，如果潜艇返回正确的数据，那么测试就会通过。因此，在这个阶段，我消除了 bugus，只需返回数据，并开始实际的代码工作。

现在，在这个阶段，我将承认我"*可能*"使用每个 Perl 开发人员最常用的命令"**警告**"当然还有"**数据::转储器**"当我起草逻辑时。所以可能是这样的:

```
sub TwelveDaysOfChristmas {
    my @achievements = @_;

    for my $achievement (@achievements) {
       warn '-->', $achievement[0];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你仔细阅读了上面的内容，你就已经预测到测试失败了。警告是错误的，因为我没有正确地解引用数组。

```
$ prove -lv
t/12_days_of_christmas.t .. Global symbol "@achievement" requires explicit package name... 
```

Enter fullscreen mode Exit fullscreen mode

因此，我将该行改为“*warn '—[T3 '，$ achievement—>[0]【T1]”，世界又恢复了正常……不，测试又失败了。这次看起来是这样的:* 

```
$prove -lv
t/12_days_of_christmas.t .. --->$VAR1 = [
          'Foo',
          ''
        ]; 
```

Enter fullscreen mode Exit fullscreen mode

嗯嗯……这是我所期望的吗？不，不是真的不，怎么了？因为在测试中，我通过了，arrayref 没有像代码期望的那样。所以我发现了我犯的另一个小错误。让我们来解决这个问题。

```
sub TwelveDaysOfChristmas {
    my $achievements = shift;

    for my $achievement (@$achievements) {
       warn '-->', $achievement[0];
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它返回这个:

```
$ prove -lv
t/12_days_of_christmas.t .. --->$VAR1 = 'Foo';
-------->$VAR1 = 'Bar'; 
```

Enter fullscreen mode Exit fullscreen mode

很好，我现在正确地循环了成绩，尽管我警告了错误的值…但是不用担心。所以让我们继续。

```
sub TwelveDaysOfChristmas {
    my $achievements = shift;

    my @cheevos;
    for my $achievement (@$achievements) {
       push @cheevos, [$achievemnet->[0], 'Done'];
    }
    return \@cheevos;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们再次运行测试，这一次…成功了！

(注意:我已经去掉了 isa_ok 和 can_ok 测试，所以现在只有一个测试)

```
$ prove -lv
t/12_days_of_christmas.t ..
ok 1
1..1
All tests successful. 
```

Enter fullscreen mode Exit fullscreen mode

所以，代码现在工作了，它正在做我们想要的。任务完成；该回家了，对吗？错了…这段代码很难看(这是故意的)，但我很快就完成了，而且它被证明是有效的。

所以现在我可以很开心地清理代码:

```
sub TwelveDaysOfChristmas {
    my $achievements = shift;

    my @cheevos = map {[$_->[0],'Done']} @$achievements;

    return \@cheevos;
} 
```

Enter fullscreen mode Exit fullscreen mode

好的…这个更小更干净，使用了更惯用的" *map* "而且重要的是仍然有效。但是我们能让它更整洁吗？

```
sub TwelveDaysOfChristmas {
    my $achievements = shift;

    [map {[$_->[0],'Done']} @$achievements ];
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯……好吧。这仍然是工作和有点短。更少的代码意味着更少的 bug。我们还能做什么？

```
sub TwelveDaysOfChristmas {
    [map {[$_->[0],'Done']} @{$_[0]} ];
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们采用了我最初的 6 行冗长代码，使用了一种更惯用的方法，并减少到 1 行 perl 代码。

我必须承认，那里的最后一个版本让我很不舒服。看着它，我很确定没有一个加入我项目的新手开发者会理解这一点。有经验的开发人员将能够理解它…但只有在精神上努力阅读该行之后。

所以我个人可能会在这个阶段停下来:

```
sub TwelveDaysOfChristmas {
    my $achievements = shift;

    my @cheevos = map {[$_->[0],'Done']} @$achievements;

    return \@cheevos;
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为这更容易理解。我们得到一些成绩，我们添加' Done '并把它放入一个新的数组，然后返回它。因为新开发人员可能会发现地图有点难以理解(也许，或者可能只是我)。但是它们可以告诉我们一种正确的方法，我们接受输入，对它做一些事情，然后返回它。

所以我想我要做的事情的概念是非常清楚的；即使完成位不是超级清楚。比起使用一行代码的解决方案，我可能更乐意让代码更易读。也许有些变化是这样的:

```
sub set_achievements_to_done {
    my $achievements = shift;

    my @done_achievements = map {[$_->[0],'Done']} @$achievements;

    return \@done_achievements;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以对我来说；这一次给了我的同事们更多的暗示，那就是潜艇应该取得“完成”的成就。所以地图上的“完成”很容易理解。所以希望更复杂的代码更容易理解，因为子名称表达了意图？

这就是我个人编码方法的一个小例子。我从一个非常简单的测试开始，以确保接线正确。然后编写一个冗长简单的解决方案，使我的测试通过。

一旦我有了一个可行的解决方案，然后，也只有到那时，我才开始查看代码，并试图使它更短、更符合习惯。我想尝试让它对我的语言(这里是 Perl)使用更有效的方法，并减少行数…更少的行数意味着更少的错误，说真的，从长远来看，它会减少错误。

相信我。我没有数据可以引用。但对我来说很有意义。我写了一个 6 行的 sub。有光泽，很好。但是 6 个月后一个同事添加了一些东西，12 个月后一个新的开发人员添加了一些东西。一年后，另一个新的开发者增加了一个东西。但是他们做了一个小小的改变，实际上打乱了我的原始代码。

我的一行解决方案可能很难读懂；但这是一个单一的声明，所以不太可能有人会在声明中间插入一些意想不到的东西。不太可能…不是不可能。

我也有一个测试。通常会写更多。但是，即使是一个证明该方法确实如我所愿的测试，也是一个很好的方式来确定，即使有人后来来了；他们“应该”不能在测试没有失败的情况下破坏我的代码。

不过，我喜欢我的代码相对冗长。我希望它向开发者表达我希望它做什么，而不是告诉计算机我希望它做什么。所以我会为了清晰而牺牲简洁。

我很想知道你对我上面展示的东西的想法。我不保证上面没有 bug。我也不认为这是有史以来最好的代码，我是坐在沙发上写博客时写的代码；所以说实话，我并没有把全部注意力放在这件事上。

我想我想表达的意思是，作为一名开发人员，测试是我的一个工具。我用它们来捕捉我犯的愚蠢的错误，同时证明我的代码做了我想要它做的事情。

长矛