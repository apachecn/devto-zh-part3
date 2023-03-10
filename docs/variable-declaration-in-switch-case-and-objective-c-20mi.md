# switch-case 和 Objective-C 中的变量声明

> 原文：<https://dev.to/zeitschlag/variable-declaration-in-switch-case-and-objective-c-20mi>

欢迎回到目标 C 区！上周，我不止一次怀疑自己的理智。例如，有一天，我重构了一些代码来使用一个 enum，我几乎被一个简单的 switch-case 弄糊涂了，直到我提醒自己，我很久以前就被这个问题弄糊涂了。但那时候，我没有写下来。是时候改变这一切了！

**TL；dr:你需要打开一个新的范围，就用花括号。**

大家新年快乐！让我们从一个枚举开始:

```
typedef NS_ENUM(NSInteger, Weekday) {
    WeekdayMonday,
    WeekdayTuesday,
    WeekdayWednesday,
    WeekdayThursday,
    WeekdayFriday,
    WeekdaySaturday,
    WeekdaySunday
}; 
```

比方说，我想知道，那天晚上我是否能喝一杯啤酒。所以，让我们为此写一个方法:

```
- (BOOL)iCanHazBeerOnWeekday:(Weekday)weekday {

    BOOL iCanHazBeer = NO;

    switch (weekday) {
        case WeekdayFriday:
        case WeekdaySaturday:
            NSString *cheers;
            cheers = @"Cheers! 🍻" // could be in one line
            NSLog(cheers);
            iCanHazBeer = YES;
            break;
        default:
            iCanHazBeer = NO;
            break;
    }

    return iCanHazBeer;
} 
```

一切看起来都很好。但是代码无法编译，Xcode 在`NSString *cheers`上报错:

> 预期表达式

过去的我太自信了:嗯，这太简单了，我不需要堆栈溢出这个东西。肯定是有些显而易见，我自己能解决！变量声明是一个表达式，不是吗？U xCODE 怎么了？

过了一段时间，我想起不久前我也看到过同样的问题:上次，我不得不使用花括号来使它工作，类似于这个问题:

```
- (BOOL)iCanHazBeerOnWeekday:(Weekday)weekday {

    BOOL iCanHazBeer = NO;

    switch (weekday) {
        case WeekdayFriday:
        case WeekdaySaturday:
        {
            NSString *cheers;
            cheers = @"Cheers! 🍻"
            NSLog(cheers);
            iCanHazBeer = YES;
        }
            break;
        default:
            iCanHazBeer = NO;
            break;
    }

    return iCanHazBeer;
} 
```

这看起来很奇怪，但是由于语法限制，标签后面不能跟声明。一个更好的解释是关于[栈溢出](https://stackoverflow.com/a/92439) : `case`使用标签，在 C 语言中，在标签之后，必须有一个表达式——天知道为什么。由于声明不是表达式，编译器会报错。这一次，你不能怪 ObjC，而是 c。

解决方法是打开一个新的作用域，这样我就可以在标签后声明变量。好的，在标签后面的花括号后面。但是最终，这种变通方法发挥了作用——并且奏效了。

[在这里](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1124.pdf)你可以找到 a 对 C 的完整引用，相关章节有 *6.7。声明*和 *6.8 声明和阻止*。[再次感谢，栈溢出](https://stackoverflow.com/questions/9730893/in-c-why-do-you-need-a-statement-after-a-goto-label/9730987)！

感谢你阅读这篇文章。

*声明:这篇博文最初发表在我的[科技博客](https://zeitschlag.net/variable-declaration-in-switch-case/)上。*