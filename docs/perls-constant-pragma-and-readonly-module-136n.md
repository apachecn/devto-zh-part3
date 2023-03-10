# 博客文章:Perl 的常量杂注和只读模块

> 原文：<https://dev.to/jonasbn/perls-constant-pragma-and-readonly-module-136n>

有一天，我在对一些 Perl 代码进行代码审查时，发现了一个*神奇的数字*。我向作者指出了这一点，强调了使用[常量 pragma](https://perldoc.perl.org/constant.html) 而不是常规可变变量的概念，正如[Perl::Critic](https://metacpan.org/pod/Perl::Critic)Policy:[Perl::Critic::Policy::ValuesAndExpressions::ProhibitMagicNumbers](https://metacpan.org/pod/Perl::Critic::Policy::ValuesAndExpressions::ProhibitMagicNumbers)所推荐的那样

他相应地修改了代码以使用一个常量，但将常量的值复制到一个常规变量中，以便可以在字符串插值中使用，声明在插值字符串中使用常量是*丑陋的*。

我完全同意，但提议的解决方案也没有多大意义。

这是一个例子，所以你可以把代码形象化(*这不是实际的代码* ):

```
use v5.10;

use constant TRUTH => 42;

my $truth = TRUTH;

say "The truth is $truth"; 
```

Enter fullscreen mode Exit fullscreen mode

一个很好的常数和适当的字符串插值使用一个常规变量。另一种方法展示了常量与字符串输出的结合使用:

```
use v5.10;

use constant TRUTH => 42;

say 'The truth is '.TRUTH; 
```

Enter fullscreen mode Exit fullscreen mode

我推荐使用只读的。

```
use v5.10;

use Readonly;

Readonly::Scalar my $truth => 42;

say "The truth is $truth"; 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们两全其美的东西:

*   我们有一个不可变的变量
*   我们可以在字符串插值中使用它

Readonly 带来了额外的好处，所以如果您不小心更改了不可变变量，您的 Perl 应用程序就会出错(您的应用程序名称和行号可能会有所不同)。:

```
Modification of a read-only value attempted at immutable.pl line 11 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
#!/usr/bin/env perl

use strict;
use warnings;
use v5.10;

use Readonly;

Readonly::Scalar my $truth => 42;

$truth = 1;

say "The truth is $truth"; 
```

Enter fullscreen mode Exit fullscreen mode

策略[Perl::Critic::Policy::ValuesAndExpressions::ProhibitMagicNumbers](https://metacpan.org/pod/Perl::Critic::Policy::ValuesAndExpressions::ProhibitMagicNumbers)建议使用[常量 pragma](https://perldoc.perl.org/constant.html) 或[只读](https://metacpan.org/pod/Readonly)，但是另一个相关的 Perl::Critic 策略不建议使用常量 pragma，即:[Perl::Critic::Policy::ValuesAndExpressions::ProhibitConstantPragma](https://metacpan.org/pod/Perl::Critic::Policy::ValuesAndExpressions::ProhibitConstantPragma)。它的广泛使用很容易证明*丑陋*也如上面的例子中所描述的，此外[只读](https://metacpan.org/pod/Readonly#Comparison-with-%22use-constant%22)中的文档提供了令人信服的案例。然而，我一直很喜欢 Perl 的常量 pragma。我[很久以前就写过这个](https://lastmover.wordpress.com/2010/05/06/constant-vs-readonly/)，实际的资料已经不存在了，并且在某个时候丢失了。

为了解释，现在是时候重做写了。constant pragma 有一个关键的好处，那就是 Perl 编译器可以在此基础上进行优化。我来演示一下:

```
#!/usr/bin/env perl

use strict;
use warnings;
use v5.10;

use constant DEBUG => 1;

if (DEBUG) {
    print STDERR "Hello Happy World of Debugging\n";
}

exit 0; 
```

Enter fullscreen mode Exit fullscreen mode

让编译器后端( [B](https://metacpan.org/pod/B) )和它的同伴(B::Terse)做他们的命令:

```
$ perl -MO=Terse constant_optimization.pl
LISTOP (0x7ff13f15cbe8) leave [1]
    OP (0x7ff13f15cb00) enter
    COP (0x7ff13f15cc30) nextstate
    LISTOP (0x7ff13f15cc90) scope
        COP (0x7ff13f15ccd8) null [193]
        LISTOP (0x7ff13f15cd38) print
            OP (0x7ff13ec0c8f8) pushmark
            UNOP (0x7ff13f15cdc8) rv2gv
                SVOP (0x7ff13ec0c8a0) gv  GV (0x7ff13f02ac38) *STDERR
            SVOP (0x7ff13f15cd80) const  PV (0x7ff13f8d0ab8) "Hello Happy World of Debugging\n"
    COP (0x7ff13f15cb48) nextstate
    UNOP (0x7ff13f15cba8) exit SVOP (0x7ff13ec0c938) const  IV (0x7ff13f8d0a70) 0
constant_optimization.pl syntax OK 
```

Enter fullscreen mode Exit fullscreen mode

但是如果将常量设置为 false ( `0` ):

```
> perl -MO=Terse constant_optimization.pl
LISTOP (0x7fac8f504b30) leave [1]
    OP (0x7fac8f89d780) enter
    COP (0x7fac8f89d6d8) null [193]
    OP (0x7fac8f504bc8) null [5]
    COP (0x7fac8f89d630) nextstate
    UNOP (0x7fac8f89d738) exit SVOP (0x7fac8f89d7c8) const  IV (0x7fac900554b8) 0
constant_optimization.pl syntax OK 
```

Enter fullscreen mode Exit fullscreen mode

产品只有 7 行，`Hello Happy World of Debugging`字符串的输出被优化掉了，其中第一个版本是 13 行，包括依赖于常量值的流控制中的语句包装。

如果我们使用只读 :
重复这个练习

```
#!/usr/bin/env perl

use strict;
use warnings;
use v5.10;

use Readonly;

Readonly::Scalar my $DEBUG => 0;

if ($DEBUG) {
    print STDERR "Hello Happy World of Debugging\n";
}

exit 0; 
```

Enter fullscreen mode Exit fullscreen mode

首先我们将不可变变量设置为`0`

```
$ perl -MO=Terse readonly_optimization.pl
LISTOP (0x7fc797820e60) leave [1]
    OP (0x7fc797821df0) enter
    COP (0x7fc797820ea8) nextstate
    UNOP (0x7fc797820f08) entersub
        UNOP (0x7fc797820f80) null [158]
            OP (0x7fc797820f48) pushmark
            OP (0x7fc795c0c938) padsv [1]
            SVOP (0x7fc797820fc8) const  IV (0x7fc7970b16e0) 0
            UNOP (0x7fc795c0c8a0) null [17]
                SVOP (0x7fc795c0c8f8) gv  GV (0x7fc797895018) *Readonly::Scalar
    COP (0x7fc797821f20) nextstate
    UNOP (0x7fc797821f80) null
        LOGOP (0x7fc797821fc0) and
            OP (0x7fc797820e28) padsv [1]
            LISTOP (0x7fc797820c30) scope
                COP (0x7fc797820c78) null [193]
                LISTOP (0x7fc797820cd8) print
                    OP (0x7fc797820da8) pushmark
                    UNOP (0x7fc797820d68) rv2gv
                        SVOP (0x7fc797820de8) gv  GV (0x7fc79602ac38) *STDERR
                    SVOP (0x7fc797820d20) const  PV (0x7fc7970b19c8) "Hello Happy World of Debugging\n"
    COP (0x7fc797821e38) nextstate
    UNOP (0x7fc797821e98) exit SVOP (0x7fc797821ed8) const  IV (0x7fc7970b1a28) 0
readonly_optimization.pl syntax OK 
```

Enter fullscreen mode Exit fullscreen mode

其次我们已经用不可变变量设置为`1`

```
> perl -MO=Terse readonly_optimization.pl
LISTOP (0x7fcce402e060) leave [1]
    OP (0x7fcce402e7f0) enter
    COP (0x7fcce402e0a8) nextstate
    UNOP (0x7fcce402e108) entersub
        UNOP (0x7fcce402e180) null [158]
            OP (0x7fcce402e148) pushmark
            OP (0x7fcce3d09f48) padsv [1]
            SVOP (0x7fcce402e1c8) const  IV (0x7fcce512fef8) 1
            UNOP (0x7fcce3d09eb0) null [17]
                SVOP (0x7fcce3d09f08) gv  GV (0x7fcce5237418) *Readonly::Scalar
    COP (0x7fcce402e920) nextstate
    UNOP (0x7fcce402e980) null
        LOGOP (0x7fcce402e9c0) and
            OP (0x7fcce402e028) padsv [1]
            LISTOP (0x7fcce402de30) scope
                COP (0x7fcce402de78) null [193]
                LISTOP (0x7fcce402ded8) print
                    OP (0x7fcce402dfa8) pushmark
                    UNOP (0x7fcce402df68) rv2gv
                        SVOP (0x7fcce402dfe8) gv  GV (0x7fcce4822838) *STDERR
                    SVOP (0x7fcce402df20) const  PV (0x7fcce51301c8) "Hello Happy World of Debugging\n"
    COP (0x7fcce402e838) nextstate
    UNOP (0x7fcce402e898) exit SVOP (0x7fcce402e8d8) const  IV (0x7fcce5130228) 0
readonly_optimization.pl syntax OK 
```

Enter fullscreen mode Exit fullscreen mode

输出非常相似，没有进行优化，流控制部分的值显示为`0`或`1`，这取决于您正在阅读的清单，但除此之外，它们在行数和内容上都是相同的。

结论:当你的部分代码可以受益于不变性时，比如*幻数*，使用[只读](https://metacpan.org/pod/Readonly)，然而当你想受益于编译器优化时，使用[常量杂注](https://perldoc.perl.org/constant.html)。

但是和 Perl 一样，*有不止一种方法来做这件事*和[Perl::critical::Policy::values and expressions::ProhibitMagicNumbers](https://metacpan.org/pod/Perl::Critic::Policy::ValuesAndExpressions::ProhibitMagicNumbers)也指向 [Const::Fast](https://metacpan.org/pod/Const::Fast) 并且根据 [Const::Fast](https://metacpan.org/pod/Const::Fast) 的文档，它是 [Readonly](https://metacpan.org/pod/Readonly) 的替代方法——然而这需要另一篇博文。

本文中使用的所有代码示例都可以在 [GitHub](https://github.com/jonasbn/blog-examples/tree/master/perls_constant_pragma_and_readonly_module) 上获得