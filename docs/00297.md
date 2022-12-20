# Ruby Code Golf:挤压字符数的技巧

> 原文：<https://dev.to/jkvyff/ruby-code-golf-tips-to-squash-character-counts-4bda>

因此，您希望编写简洁的 Ruby 代码，用 40 个字符来完成魔术，但也许您可以将其压缩到 39 个字符。这就是代码高尔夫的精彩世界。用尽可能少的字符编写解决问题的代码。

抱歉 linters 和良好的代码约定一般。但是我正试着写一个程序，可以舒服地坐在 50 字节的内存里。

# 去掉字节

让我们深入一些常见的用例，在这些用例中，您可以用更少的字符做同样的工作。

## 方法

我们习惯了这样的方法。

```
#(22 bytes)Beautiful, legible, and far too many bytes.
def add1(x)
    x + 1
end

#(15 bytes)Method name is now one char and we lost formatting.
def d x
x+1
end

#(12 bytes)You have probably used this compact form for a short method.
def d x{x+1}

#(10 bytes)But we can do better with a lambda object.
d=->x{x+1} 
```

注意:Lambdas 不像其他方法那样被调用，您可以选择:

1.  `d.call(4) => 5`
2.  `d.(4) => 5`
3.  `d[4] => 5`

所以我们用 lambda 和变量
`d=->x` vs `def d x`
定义节省了 2 个字节，但是我们为最短调用
`d[x]` vs `d x`
损失了一个字节，无论我们使用方法还是 lambda，程序中调用和变量的数量都会改变。

## 循环

快速提示:如果给你的循环赋值一个参数，使用`<`或`>`比使用`==`更短，但仍然有效。

在 ruby 中创建循环的方法有很多，但最有用的是:

1.  `.map`将数据更改到相同大小的容器时
2.  当一个人知道执行一个方法的次数时
3.  `.upto`对于在字符串上使用`.times`方法的情况

```
#upto example
'1'.upto('3'){puts "cat"}
#or
'a'.upto('c'){puts "cat"}
> cat
> cat
> cat
=> nil 
```

## 打印

Ruby 帮助我们解决了这个问题，因为方法有一个隐式的返回值，但是这并不总是足以得到我们想要的。在看到输出方法`print`、`puts`和`p`后，似乎`p`是显而易见的选择。

然而，`print`、`puts`和`p`并不是彼此的别名，虽然默认使用`p`是一个不错的选择，但有时如果您的数据类型的默认输出是您想要的格式，使用其中一个会更快。

```
#calls to_s on the object
print 1,2,3
> 123 => nil

#calls to_s on the object and newline after an element
puts 1,2,3
> 1
> 2
> 3
> => nil

#calls Also adds newline, but calls inspect, so you return the actual class of an object
p 1,2,3
> 1
> 2
> 3
> => [1, 2, 3] 
```

提示:因此，用 print 或 puts 而不是 string 打印符号可以节省一个字节。

双引号中的字符串的转义序列在打印中也很有用。

*   `\"`–双引号
*   `\\`–单反斜线
*   `\a`–铃声/警报
*   `\b`–退格键
*   `\r`–回车
*   `\n`–换行
*   `\s`–空格
*   `\t`–标签

## 加贺

1.  如果你想使用数组的很多部分，试试`t, *m, b = [1,2,3,4,5]`
    *   t => 1
    *   [2，3，4]
    *   b => 5
2.  节省一个字节
    *   使用问号`?,`而不是`','`调用字符
3.  `.join()`的别名是`\*`，所以`d.join(',')`可能会变成`d\*?,`
4.  用`-[nil]`或`-[p]`代替`.compact`
5.  用`a==a.uniq`检查数组的唯一性
6.  查找字符是否包含在数组中

```
n = [1,2,3,4,5]
#(5 bytes)use the triple equals
n===3
> true

#shorter than the alternatives

#(11 bytes)
n.member? 3
> true

#(12 bytes)
n.include? 6
> false 
```

## 挑战

使用这些技巧，我能够登上一个有趣的高尔夫挑战网站的排行榜。有本事你打我最好。
[![](img/ad1ba743ca174be374091623473412d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nAgVW624--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j40yhrkxbkuswr8z9o2l.png) 
(抱歉码高尔夫网站为双倍发布...忘记写名字了。)

## 资源

https://code golf . stack exchange . com/questions/363/tips-for-golf-in-ruby？page = 2&tab = Votes
[https://medium . com/ruby-golf/ruby-golf-cheat sheet-EB 27 EC 2 CDF 60](https://medium.com/ruby-golf/ruby-golf-cheatsheet-eb27ec2cdf60)
[https://www . gare threes . co . uk/2013/05/04/p-vs-puts-vs-print-in-ruby/](https://www.garethrees.co.uk/2013/05/04/p-vs-puts-vs-print-in-ruby/)
[http://golf.shinh.org/p.rb?pascal+triangle](http://golf.shinh.org/p.rb?pascal+triangle)