# 博客文章:regular expressions::require default 的 2.00 版本

> 原文：<https://dev.to/jonasbn/-blog-post-release-200-of-perlcriticpolicyregularexpressionsrequiredefault-19pj>

我知道我刚刚在“[Blog post:Static Analysis in Perl-or that another policy](https://dev.to/jonasbn/static-analysis-in-perl---or-yet-another-policy-m98)”中写了关于这个策略的博客，但是当我结束这篇文章时，我正在考虑尝试将这个策略引入到[Perl::critical](https://metacpan.org/pod/release/PETDANCE/Perl-Critic-1.132/lib/Perl/Critic.pm)core 中。

我查看了 TODO 列表和已知问题列表,认为该策略还不适合 PR to Perl::critical，因为我希望它实现我列出的所有特性。

突然灵光一现，我实现了*最后一个*功能，结果已经上传到 CPAN 2.00 版。

如果您希望策略执行的评估是严格的还是自由的(默认)，2.00 版引入了配置功能。

因此，您可以向 Perl::Critic 配置文件添加一个配置参数，并启用严格性。

```
[RegularExpressions::RequireDefault]
strict = 1 
```

Enter fullscreen mode Exit fullscreen mode

该策略如果启用，默认情况下允许使用`a`和`aa`。然而，如果`strict`被启用，`a`将触发违规，`aa`不会，因此有*严格度*。

示例:

```
my $ascii_letters =~ m/[A-Z]*/i;  # not ok
my $ascii_letters =~ m/[A-Z]*/a;  # ok, but not ok with strict enabled
my $ascii_letters =~ m/[A-Z]*/aa; # ok 
```

Enter fullscreen mode Exit fullscreen mode

该策略还评估在相同条件下是否启用了编译指示:

示例:

```
use re 'a';  # ok, but not ok with strict enabled
use re 'aa'; # ok 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，这个实现仍然存在编译指示使用的问题，因为很难在源代码中本地处理编译指示的启用和禁用。Perl::Critic 使用 [PPI](https://metacpan.org/pod/PPI) 对有问题的源代码进行外部解析，这意味着不像您看到的一些静态分析工具那样是实际的 AST，所以这些事情不像这些工具那样容易，这些工具更多地集成到源代码的实际解析中。

无论如何，我现在将继续评估 Perl::critical 采用该策略的可能性。