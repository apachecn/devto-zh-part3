# Python 的禅

> 原文：<https://dev.to/therealdarkmage/the-zen-of-python-kb6>

今晚早些时候，我和我的一个学生偶然发现了一个独特的语法:

```
def insertion_wrapper(f):
    # code goes here...

class LinkedList:
# some code for LinkedList class
# ...
    @_insertion_wrapper
    def insertion_sort(self):
        # code goes here... 
```

Enter fullscreen mode Exit fullscreen mode

为了寻求帮助，我开始阅读那些名人的文章...

我忘了是哪一个，没关系。

我在查阅索引时发现了 Python 的禅宗。

我觉得这很有趣。

```
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

Enter fullscreen mode Exit fullscreen mode

的确，一个漂亮的帖子。

在 PEP 20 的底部有一个“复活节彩蛋”...他们告诉我们...

在`python3`终端中这样做产生了上面的文本。

作为一个有事业心的、探索性的黑客，我做了以下事情:

```
$ python3
> import this
...
> this
<module 'this' from '/usr/lib/python3.6/this.py'> 
```

Enter fullscreen mode Exit fullscreen mode

有意思！

让我们看看这个文件的内容:

```
cat /usr/lib/python3.6/this.py                             
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

Enter fullscreen mode Exit fullscreen mode

所以，这里有一些基本的密码。

让我们来分解它...这是我第一次看这个:

```
for c in (65, 97): 
```

Enter fullscreen mode Exit fullscreen mode

65 是大写的 A，97 是小写的 A。

```
for i in range(26): 
```

Enter fullscreen mode Exit fullscreen mode

字母表中的 26 个字母。

```
d[chr(i+c)] = chr((i+13) % 26 + c) 
```

Enter fullscreen mode Exit fullscreen mode

`d`是一本字典。

进入`d`的关键是 chr(i+c)。`i`正在从 0 迭代到 26。进入`d`的每个条目都是大写和小写的字母字符。

价值呢？

```
chr((i+13) % 26 + c) 
```

Enter fullscreen mode Exit fullscreen mode

哇，这是怎么回事？

因此，字母`i`向上/向前/向前移动了 13，然后我们将它修改 26，得到从 A 到 Z 的另一个字母，然后我们加上偏移量`c`。

`d`成为如何有效转换每个字符的地图。

```
"".join([d.get(c,c) for c in s])) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我为什么喜欢 Python 的原因。

列表理解是伟大的。

对于`s`中的每个角色`c`，我们从`d`中抓取相应的角色。

这是一个简单的密码，有趣的是它被内置在 Python 中。

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)