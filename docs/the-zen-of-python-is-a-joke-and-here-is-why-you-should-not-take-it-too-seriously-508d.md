# Python 的禅宗是一个笑话，原因如下(你不应该太认真)

> 原文：<https://dev.to/abdurrahmaanj/the-zen-of-python-is-a-joke-and-here-is-why-you-should-not-take-it-too-seriously-508d>

Python 的禅启发了全世界的程序员，但是，不要从字面上理解它。

## 令人肃然起敬的禅宗，一种表述

输入这个

```
import this 
```

给了我们

```
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those! 
```

这就是所谓的 python 之禅

## 一句令人捧腹的好笑话

让我们来看看下面一行。

```
There should be one-- and preferably only one --obvious way to do it. 
```

大多数开发人员认为这意味着 python 让你用一种方式编写代码，而不是用多种方式来表达。许多人接着将语言学家发明的 perl 与数学家发明的 python 进行对比。

然而，这一行有两个问题

*   应该有还是最好应该有
*   那个-

那么，是否应该有一种方式的含义，是必须有一种方式还是建议有一种方式？是强制还是只是推荐？

这一行没有让我们去猜测，它实际上显示了。

为了保持一致性，它应该是

```
There should be one-- and preferably only one-- obvious way to do it. 
```

或者

```
There should be one --and preferably only one --obvious way to do it. 
```

但这两者都是

```
There should be one-- and preferably only one --obvious way to do it. 
```

正如大卫·古杰提到的，看最后一行:

> 名称空间是一个非常棒的想法——让我们多做一些吧！

另一种使用方式！

## 向进口本身问好

让我们走这条线

```
Explicit is better than implicit. 
```

但是我们通过
进口

```
import this 
```

oxforddictionaries.com 将显性定义为

清楚而详细地陈述，不留任何混淆或怀疑的余地。

并且隐含为

虽然没有直接表达，但暗示了。

那么，印刷的名称和方式是显性的还是隐性的呢？

## 文件告诉我们什么

下面是 py3.7
中的 zen 模块源代码

```
s = """Gur Mra bs Clguba, ol Gvz Crgref

Ornhgvshy vf orggre guna htyl.
Rkcyvpvg vf orggre guna vzcyvpvg.
Fvzcyr vf orggre guna pbzcyrk.
Pbzcyrk vf orggre guna pbzcyvpngrq.
Syng vf orggre guna arfgrq.
Fcnefr vf orggre guna qrafr.
Ernqnovyvgl pbhagf.
Fcrpvny pnfrf nera'g fcrpvny rabhtu gb oernx gur ehyrf.
Nygubhtu cenpgvpnyvgl orngf chevgl.
Reebef fubhyq arire cnff fvyragyl.
Hayrff rkcyvpvgyl fvyraprq.
Va gur snpr bs nzovthvgl, ershfr gur grzcgngvba gb thrff.
Gurer fubhyq or bar-- naq cersrenoyl bayl bar --boivbhf jnl gb qb vg.
Nygubhtu gung jnl znl abg or boivbhf ng svefg hayrff lbh'er Qhgpu.
Abj vf orggre guna arire.
Nygubhtu arire vf bsgra orggre guna *evtug* abj.
Vs gur vzcyrzragngvba vf uneq gb rkcynva, vg'f n onq vqrn.
Vs gur vzcyrzragngvba vf rnfl gb rkcynva, vg znl or n tbbq vqrn.
Anzrfcnprf ner bar ubaxvat terng vqrn -- yrg'f qb zber bs gubfr!"""

d = {}
for c in (65, 97):
    for i in range(26):
        d[chr(i+c)] = chr((i+13) % 26 + c)

print("".join([d.get(c, c) for c in s])) 
```

然而，在禅中我们看到了

```
Readability counts. 
```

可读性很强...

## 但是等待，也有例外

嗯，你是对的，也有例外。是的，也许 zen 文件是 zen 的一个例外，但是等等...禅宗告诉我们

```
Special cases aren't special enough to break the rules. 
```

那好吧...我想我们已经明白了

## 结论

结束语是本文的标题。注意**这个字也是**。