# Perl 每周挑战周#1 挑战#2: FizzBuzz

> 原文：<https://dev.to/jj/perl-weekly-challenge-week-1-challenge-2-fizzbuzz-3g6i>

接下来是第二个第一周的挑战。

> 写一个小程序来解决 FizzBuzz 问题，并打印数字 1 到 20。然而，任何能被 3 整除的数字都应该用‘嘶嘶’这个词来代替，任何能被 5 整除的数字都应该用‘嗡嗡’这个词来代替。那些既能被 3 又能被 5 整除的数字变成了“嘶嘶作响”。

[![That's me with another Buzz, Lightyear Buzz](img/f75926d12cdef7749b4b75f100a147d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2eslUpUK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jz19hhzxqmza9hi97872.jpg)

好了，解决办法来了。作为一门函数式语言，Perl 6 并不十分困难:

```
say (1..20 ==> map( { ($_ == 15)??"FizzBuzz"!!$_ } ) ==> duckmap( -> Int $_ { ($_ %% 5 )??"Buzz"!!$_} ) ==> duckmap( -> Int $_ { ($_ %% 3 )??"Fizz"!!$_} )); 
```

首先，我使用 [*火箭*操作员](https://docs.perl6.org/routine/==%3E)只是为了好玩。它很好地说明了事情是如何发展的，朝什么方向发展，做了什么。它实际上是一些函数的语法糖，这些函数可以用作方法，或者将它们的“目标”(注意 rocket 上的双关语)作为第二个参数。`map`和`duckmap`就是这样的函数(大部分*函数*函数也是)。所以还是挺充足的。所以火箭的三级完成了这个任务

*   在第一个阶段中，获得 15 分钟的时间
*   在第二阶段，我们有一个多样化的列表:数字和一个字符串。一个简单的`map`将不得不首先检查它是否是一个数，然后检查它是否能被整除。可能有一些简单的方法可以做到这一点，但是使用[奇妙的`duckmap`](https://docs.perl6.org/routine/duckmap) 要简单得多，它只映射鸭子，因此得名；如果不是，它会通过。所以让我们创建一个[尖块](https://docs.perl6.org/language/control#for)，它只能应用于被替换的 int。
*   第三级可能会降低到第二级，但这是一个火箭，它需要飞行，所以让我们对第三级做同样的事情。

这是它打印的内容

```
(1 2 Buzz 4 Fizz Buzz 7 8 Buzz Fizz 11 Buzz 13 14 FizzBuzz 16 17 Buzz 19 Fizz) 
```

很好，不是吗？查看 [Perl 每周挑战站点](https://perlweeklychallenge.org/blog/a-new-week-a-new-challenge/)以获得更多 Perl 和 Perl 6 的精彩解决方案！

也看看第一个每周挑战[，翻译和计算](https://dev.to/jj/perl-weekly-challenge-1-translate-letters-and-count-4cdf)