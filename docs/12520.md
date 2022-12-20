# 博客文章:Test::Timer 的 2.10 版本

> 原文：<https://dev.to/jonasbn/release-210-of-testtimer-5f5k>

我刚刚发布了一个 [Test::Timer](https://jonasbn.github.io/perl-test-timer/) ，现在它应该可以在你的 CPAN 本地下载了。

这个版本是一个维护版本，有一些内部清理。但是我对这个版本的变化特别满意，所以我想和你分享细节。

Test::Timer 提供了一个测试接口，该接口遵循 [Test Anything 协议](http://testanything.org/) (TAP)并与现有的 Perl 测试工具链集成，它依赖于[基准测试](https://metacpan.org/pod/Benchmark)，这是一个较老但稳定且相当流行的基准测试模块。

基准测试的默认输出如下:

```
2 wallclock secs ( 0.00 usr +  0.00 sys =  0.00 CPU) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着早期版本的 Test::Timer 必须解析这个字符串，以便在流程的实际断言部分隔离数据。

因此，Test::Timer 实现并很好地封装了这个例程，使用正则表达式进行解析。

```
# helper method to change benchmmark's timestr to an integer
sub _timestring2time {
    my $timestring = shift;

    # $timestring:
    # 2 wallclock secs ( 0.00 usr +  0.00 sys =  0.00 CPU)
    my ($time) = $timestring =~ m/(\d+) /;

    return $time;
} 
```

Enter fullscreen mode Exit fullscreen mode

Perl::critical 抱怨正则表达式没有遵循正则表达式的最佳实践。

这让我开始思考。我喜欢正则表达式，同时我知道它们可能容易出错，难以调试，在这种情况下用于解析我无法控制的组件的输出，这意味着输出可能会在没有通知的情况下改变*(意味着我没有注意到)。在这种特殊情况下，我不认为这是一个迫在眉睫的风险，但仍然是一个风险。*

我决定用不同的方法来解决这个问题，而不是花时间去遵循 Perl::critical 推荐的正则表达式的最佳实践。

1.  正则表达式的变化不会解决上述解析问题
2.  Benchmark 必须提供更多超越基准数据或者格式化输出的方法

我通读了文档，写了一个小的原型。

第一次尝试是通过改变`timestr`的输出来改变 Benchmark 提供的输出格式。然而，这并不像我希望的那样简单，仍然需要解析。

然后我发现了一组访问器和`real`似乎是我所需要的。

下面是最终的原型:

```
#!/usr/bin/env perl

use strict;
use warnings;

use Benchmark;

my $t0 = Benchmark->new;

sleep(1);

my $t1 = Benchmark->new;

print timediff($t1, $t0)->real;

print "\n";

exit 0; 
```

Enter fullscreen mode Exit fullscreen mode

最终的解决方案如下:

```
my $timestring = timediff( $t1, $t0 )->real;

return $timestring; 
```

Enter fullscreen mode Exit fullscreen mode

来自以前:

```
# parsing benchmark output
my $timestring = timestr( timediff( $t1, $t0 ) );
$time = _timestring2time($timestring);

return $time; 
```

Enter fullscreen mode Exit fullscreen mode

我最终排除了`_timestring2time`,仅仅依靠基准测试并吸取了一些教训:

*   阅读所有文档
*   封装是救世主

接下来的步骤是对基准对象进行完整的封装，并使用“依赖倒置原则”进行评估。