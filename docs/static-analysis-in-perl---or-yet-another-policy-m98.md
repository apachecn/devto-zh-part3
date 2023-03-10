# 博客文章:Perl 中的静态分析——或者另一种策略

> 原文：<https://dev.to/jonasbn/static-analysis-in-perl---or-yet-another-policy-m98>

写完我上一篇博文:“[bug！？-静态分析拯救](https://dev.to/jonasbn/bugs---static-analysis-to-the-rescue-2m17)“我查看了我的博客帖子列表，我发现我从来没有写过关于我最近实施的另一个[Perl::critical](https://metacpan.org/pod/Perl::Critic)政策的博客。

实现的想法也不是我自己的，而是由 Joelle Maslak ( [@jmaslak](https://mobile.twitter.com/jmaslak) )在 2018 年 6 月的一条推文引发的。

> Perl folk:寻找一个公关挑战任务？检查正则表达式中真正应该是[0-9]或应该具有/a 正则表达式修饰符的\d。默认情况下，Perl 是跨国的！#TPCiSLC

读完 tweet 后，我认为 Perl::Critic 策略听起来是一个不错的解决方案，因为我们希望将潜在的问题永久地排除在我们的代码库之外，静态分析是一个很好的过程和工具。所以我实现了[Perl::critical::Policy::regular expressions::require default](https://github.com/jonasbn/perl-critic-policy-regularexpressions-requiredefault)。

该策略的目标是建议对正则表达式使用 Perl 5.14 中引入的内置改进[，以避免以下潜在问题:](https://perldoc.pl/perl5140delta#%2Fd%2C-%2Fl%2C-%2Fu%2C-and-%2Fa-modifiers)

*   视觉欺骗
*   字符和字符串转换漏洞

这两个方面都是与 Unicode 相关的安全问题。实际上，我从来没有发现是谁在 Perl 中实现了这个特性，但是这个策略只是依赖于这个实现，所以 Perl 维护者实现了这个特性，提供了在 Perl 中编写更安全的正则表达式的工具，我的策略只是试图推荐正则表达式使用这个特性——它是否适合您的代码库仍然由您来决定:-)

该策略被上传到 [MetaCPAN](https://metacpan.org/pod/Perl::Critic::Policy::RegularExpressions::RequireDefault) 上，可以通过 CPAN 获得，但是我正在考虑尝试在 Perl::Critic core 中采用它。

一定要让我知道你的想法-公关和反馈最受欢迎...