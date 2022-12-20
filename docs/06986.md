# Google Code Jam 2019 资格

> 原文：<https://dev.to/choroba/google-code-jam-2019-qualification-19jd>

[![jam](img/0630d81ff73294091afeb7e11f5f29a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WJNt8XlJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y027sv6qdmuyichpvl4f.jpg)

今年的 Google Code Jam 资格赛回合限制是 30 分。仅仅解决两个简单的任务就足以让你度过难关。

我用 Perl 来解决问题，因为这是我能写得最快的语言。

你可以在[档案](https://codingcompetitions.withgoogle.com/codejam/archive/2019)里找到问题。

## 第一个任务

第一项任务听起来很简单:输入的是一系列数字，每个数字都包含数字 4。对于每个数字 N，输出两个数字 A 和 B，使得 N = A + B，并且 A 和 B 都不包含数字 4。

我使用了 [tr](http://p3rl.org/tr) 运算符(类似于你可能从*nix 中知道的`tr`实用程序)将所有的 4 替换为 3。这是我的 A。为了得到 B，我只是从原始 n 中减去 A。

```
#! /usr/bin/perl
use warnings;
use strict;
use feature qw{ say };

chomp( my $cases = <> );
for my $case (1 .. $cases) {
    chomp( my $n = <> );
    (my $i = $n) =~ tr/4/3/;
    my $j = $n - $i;
    say "Case #$case: $i  $j";
} 
```

这给了我第一个 16 分。要获得更多，就需要处理更多的数据。这可以通过使用 [Math::BigInt](http://p3rl.org/Math::BigInt) 核心模块来实现，但是我更倾向于直接进入下一个任务。

```
use Math::BigInt;

...
my $j = 'Math::BigInt'->new($n) - 'Math::BigInt'->new($i); 
```

## 第二个任务

给你一条正方形的路，每一步不是向南就是向东。您需要找到一条不同的路径，在相同的地方开始和结束(西北角和东南角)，但永远不要重用给定的路径。

输入路径被编码成类似于`SSEEESSE`的字符串。令人惊讶的是，音译运营商可以再次提供一个简单明了的解决方案。它对应于与输入线对称的路径，其中轴连接起点和终点。

```
#! /usr/bin/perl
use warnings;
use strict;
use feature qw{ say };

chomp( my $cases = <> );
for my $case (1 .. $cases) {
    <>;
    chomp( my $lydia_path = <> );
    (my $path = $lydia_path) =~ tr/SE/ES/;
    say "Case #$case: $path";
} 
```

这给了我 14 分，所以我的晋升是有保证的。

我还解决了第三个任务，但解决方案很难看，对更大的投入和额外的分数不起作用。它又给了我 10 分。

第一轮见！