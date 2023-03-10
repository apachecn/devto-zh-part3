# 按引用传递，按值传递

> 原文：<https://dev.to/jeremy/pass-by-reference-pass-by-value-c9e>

有一天，当我在杂货店购物时，有人注意到了我的 RubyConf t 恤，并问道:“那么你实际上在 Ruby 中做了什么吗，或者你只是去参加了会议？”我说，“嗯，现在我只是一个业余爱好者，但我希望有一天我能做得足够好，并为此获得报酬。”他马上问道:“按引用传递和按值传递有什么区别？”

我不知道。

我的意思是，我依稀记得以前听过这些术语，但我无法清楚地说出它们的确切含义。所以我回家并开始学习它，现在我可以感激地说，我不仅理解了编程语言的一个方面，这是我以前不知道的，而且我可以把它传授给其他人，他们可能和我在同一个地方。

但是在我们到达那里之前，首先了解一些事情是很重要的:计算机内存和变量。

***记忆***

[![](img/09e10335ccbb42d730c10d7705be73c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bx0z0Xqg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jy654rgdpwsii3ob5h2w.jpg)

打个比方，我喜欢把内存想象成电脑里一堆贴着标签的小盒子。当我用 Ruby 写程序时，我创建了一个对象，这个对象位于内存中的一个特定位置——在其中一个有标签的盒子里。当我需要特定的数据时，可以从内存中的那个位置获取它。现在需要理解的重要一点是，不同的对象会占用或多或少的内存，这取决于它们是什么。整数比字符串占用更少的内存空间。

**注意:**这是一个故意过于简化的类比。内存，以及 Ruby 利用内存的方式，是一个更复杂的话题，我希望在以后的日子里详细讨论这个话题(阅读:一旦我觉得我已经足够理解并解释它了)。但是，如果此时您正在考虑堆和垃圾收集，您应该找一些更高级的东西来阅读。如果你不知道这些术语的意思，现在不要紧张，继续读下去，我的朋友。你知道此时你需要知道什么。

***变量和*变量赋值**

当我们谈论“值”时，我们谈论的是可以赋给或存储在变量中的东西，这是一个描述性短语，指向赋给它的值。在 Ruby 中，值可以是字符串、布尔值、整数、浮点、数组、散列...你明白了。将对象赋给变量允许我们在自己编写的方法中使用这些对象。这叫做“传递”变量。

如果我创建一个名为

`best_sandwich_ever`

并且(有争议地)给它分配了一个字符串对象“Reuben”，

`best_sandwich_ever = "Reuben"`

`Reuben`是我正在处理的值，我的程序现在知道当我使用变量`best_sandwich_ever`时，它需要访问存储在我计算机物理内存中的字符串对象`Reuben`。一旦我将我的`Reuben`字符串赋给变量，我的操作系统就会将该字符串存储在内存中一个特定的、编号的位置，所以现在当我使用`best_sandwich_ever`变量时，我正在使用我的`Reuben`字符串。

```
best_sandwich_ever = "Reuben"
puts "I love me a #{best_sandwich_ever}!"
"I love me a Reuben!" 
```

Enter fullscreen mode Exit fullscreen mode

如果我想创建一个返回`best_sandwich_ever`变量的方法，我将需要在该方法中定义变量，否则它将超出该方法的范围，并且该方法将无法访问它。

```
best_sandwich_ever = "Reuben"

def fav_sammy
  return "I love me a #{best_sandwich_ever}!"
end

fav_sammy

# => NameError (undefined local variable or method `best_sandwich_ever' for main:Object) 
```

Enter fullscreen mode Exit fullscreen mode

然而，我可以将变量作为参数传递给方法。

```
best_sandwich_ever = "Reuben"

def fav(sammy)
  return "I love me a #{sammy}!"
end

fav(best_sandwich_ever)
# => I love me a Reuben! 
```

Enter fullscreen mode Exit fullscreen mode

***通过引用***

这里到底发生了什么？当我将`best_sandwich_ever`传递给`#fav`方法时，我是通过引用传递的，这意味着我的变量指向我的`Reuben`在内存中的确切位置。变量指向当前包含`Reuben`的盒子的编号。为什么？因为我的字符串会随着时间而改变，因此它的内存需求也会改变。如果我现在想指定有史以来最好的三明治是我最喜欢的酒吧的鲁本三明治呢？

```
best_sandwich_ever << " (but only from The Old Spot)"

fav(best_sandwich_ever)
# => I love me a Reuben (but only from The Old Spot)! 
```

Enter fullscreen mode Exit fullscreen mode

该字符串现在需要比字符串“Reuben”更多的内存，因此为了节省内存和处理时间，Ruby 修改了位于内存中原始位置的字符串，而不是制作一个副本(我们将在开始讨论按值传递时讨论这个问题)。

这里的要点是，在内存中的原始位置修改数据比制作副本并修改副本更节省内存，除非我们引用的数据对内存的要求很低，如整数。相应地，在 Ruby 中，具有较大内存需求的数据类型(如字符串和数组)是通过引用传递的。

***按值传递***

类似于我的最后一个例子，如果我创建一个名为

`albert_einsteins_iq`

并给它分配整数对象 160，

`albert_einsteins_iq = 160`

我的计算机的操作系统会立即将该对象放在内存中一个特定的、编号的位置。此时，如果我做一些疯狂的事情，比如 sum 160

```
albert_einsteins_iq = 160
 # => 160 
albert_einsteins_iq.digits.sum
 # => 7 
```

Enter fullscreen mode Exit fullscreen mode

看起来好像我把`albert_einsteins_iq`从 160 降到了 7(哎哟)。然而，事实并非如此，因为我一调用`albert_einsteins_iq`上的方法，数据项`160`的副本就被制作出来，并被分配到内存中不同于原始数据`160`的位置，当我对数字*求和时，副本被更改，只留下原始数据。*我们可以通过再次调用变量并查看它返回的内容来证明这一点。

```
albert_einsteins_iq
# => 160 
```

Enter fullscreen mode Exit fullscreen mode

在 Ruby 中，字符串、数组和散列是通过引用传递的。整数、浮点数和布尔值通过值传递。

**注意:**在 Ruby 2.4 之前，fixnum 也是一种按值传递的数据类型，但是从 2.4 开始，fixnum 和 bignum 变成了 integer 对象。还要注意的是，根据我们谈论的是哪种语言，通过引用传递和通过值传递的工作方式会有所不同，但是基本的概念(大部分)是相同的。

如果我以前知道这些，我会怎么回答我的杂货店朋友呢？我会说，通过引用传递是当数据项的实际内存地址被传递时，当被修改时，数据项本身被修改。而按值传递是当数据项的值被传递时，并且当被修改时，修改该值，使原始存储器地址处的数据项不被修改。

如果我传递一个包含我的购物清单的数组，传递的是我的数组的内存地址，当我从清单中移除物品时，数组会被修改。

如果我传递数字 1，传递的是数字 1，但是是在一个独立的内存地址，我最初把数字 1 赋给了一个变量，如果我用它做算术运算，它就被修改了。

然后我会问他是否知道拉克鲁瓦的水在哪个通道。