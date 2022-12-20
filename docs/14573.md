# 博文:虫子！？-救援静态分析

> 原文：<https://dev.to/jonasbn/bugs---static-analysis-to-the-rescue-2m17>

Dave Cross 写了一篇很有见地的博客文章“一个微妙的错误”,描述了 Perl 中的一个反模式导致的一个错误，这个错误相当微妙(这就是最初文章的标题)。

是的，对你的代码库进行扫描/搜索并找出这个 bug 是可能的。但是一次性的分析带来了一些问题，比如让同样的错误永久地远离我们的代码库。

而这正是[静态程序分析](https://en.wikipedia.org/wiki/Static_program_analysis)(或者仅仅是静态分析)工具的用途，尤其是当与你的源代码控制系统或者 CI/CD 系统集成时。

Perl 的主流静态分析工具被命名为 [Perl::Critic](https://metacpan.org/pod/Perl::Critic) 。我已经为 Perl::critical 编写了几个策略来执行我自己的编码策略，我对静态分析的概念非常感兴趣。我热情的原因是我真的真的喜欢代码审查，但是我经常观察到我们倾向于讨论显而易见的事情，所以如果我们想从耗费时间和精力的人工审查中获得最大的收益，我们就把*枯燥的*工作留给计算机，这意味着对你的代码库进行静态分析，而人类可以做人类非常擅长的事情，那就是使用创造力、模式匹配和经验来发现其他的东西。

基于 Dave 的帖子，它立即触发了我的一些想法，我已经开发了第一个发现 Perl 源代码中的反模式和可能的 bug 的策略。

简介:[Perl::critical::Policy::input output::prohibithighprecentlogicaloperatorrorhandling](https://github.com/jonasbn/perl-critic-policy-inputoutput-prohibithighprecedentlogicaloperatorerrorhandling)

```
sub _is_logical_operator {
    my ( $self, $sibling ) = @_;

    if ($sibling) {
        if ($sibling->class eq 'PPI::Token::Operator') {
            if ($sibling->content eq q{||}) {
                return $TRUE;
            }
        }
        return $self->_is_logical_operator($sibling->snext_sibling());
    }

    return $FALSE;
} 
```

我自己也不喜欢这个名字，而且它有些错误，因为使用`or`而不是`||`也是一个逻辑运算符。其次，你仍然可以在括号中使用`||`操作符。

第一个实现非常优雅，只依赖于递归函数`_is_logical_operator` ，但是它没有处理括号的存在。

所以我不得不[引入一个正则表达式](https://github.com/jonasbn/perl-critic-policy-inputoutput-prohibithighprecedentlogicaloperatorerrorhandling/blob/master/lib/Perl/Critic/Policy/InputOutput/ProhibitHighPrecedentLogicalOperatorErrorHandling.pm#L42-L50) :-/

```
 return if $elem->snext_sibling()->content() =~ m/^[\s]*[(]/xism; 
```

在干净代码的旗帜下，我喜欢至少隔离正则表达式——以不同的方式处理它会很棒，所以我仍然在考虑一个解决方案。

我已经通知了 Dave 我的实现，并在推特上发布了相关信息，现在我正在写博客，所以希望我能得到一些评论或反馈——我提到过我喜欢代码评论吗？:-)

当我稳定了代码并最终确定了文档后，我正在考虑向 [Perl::Critic 发行版](https://github.com/Perl-Critic/Perl-Critic)发出一个 pull 请求，因为我对维护另一个独立的策略并不感兴趣，但在另一篇博客文章中对此有更多的介绍。

请查看让我知道你的想法:

*   是否有我没有捕捉到的场景？
*   在我的实现中有没有我没有发现的错误？
*   政策实施是否值得？
*   还有别的吗？