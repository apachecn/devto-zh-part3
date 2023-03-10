# 提升 yo py/js adhd af 研究 blaggin

> 原文：<https://dev.to/therealdarkmage/level-up-yo-py-js-adhd-af-research-blaggin-2bh>

# 博客-2019-5-19 . MD

警告:这个博客是多动症 af，无处不在，字面上只是去与我自己的大脑流在这里。全部。完毕。的。地点。小心，就像布鲁克一样。如果你听到了他美妙的声音(减去晒干的西红柿——那是我的！)在你的脑海里，继续读下去。

* * *

### “Javascript 怎么写内存泄漏？”是我的第一个想法。

[https://auth 0 . com/blog/four-types-of-leaks-in-your-JavaScript-code-and-how-to-get-off-them/](https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/)

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Memory _ Management # Release _ when _ the _ Memory _ is _ not _ needle _ more](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management#Release_when_the_memory_is_not_needed_anymore)

高级语言嵌入了一个名为“垃圾收集器”的软件，其工作是跟踪内存分配和使用，以便发现何时不再需要一块分配的内存，如果不再需要，它将自动释放它。这个过程是近似的，因为知道是否需要某块内存的一般问题是不可判定的(不能通过算法解决)。

[https://en.wikipedia.org/wiki/Decidability_%28logic%29](https://en.wikipedia.org/wiki/Decidability_%28logic%29)

[https://en.wikipedia.org/wiki/Undecidable_problem](https://en.wikipedia.org/wiki/Undecidable_problem)

要发现漏洞，请使用 chrome 内存使用工具

这对想破坏网站的人有用吗？

* * *

### [https://blog . cy stack . net/arbitrary-file-read-vulnerability-in-hacker rank/](https://blog.cystack.net/arbitrary-file-read-vulnerability-in-hackerrank/)

```
Accept ../../../../../../../../../../etc/passwd{{ 
```

Enter fullscreen mode Exit fullscreen mode

最近的 hackerrank.com 开发。哥们连工资都没拿:(

* * *

### 摘自安珀·布朗的惊人之作《电池包括在内，但它们在漏水》

[https://py found . blogspot . com/2019/05/amber-brown-batteries-included-but . html](https://pyfound.blogspot.com/2019/05/amber-brown-batteries-included-but.html)

*   用 mypy 打字效果最好
*   ssl 模块需要一个 monkeypatch 来连接非 ASCII 域名
*   日期时间需要 pytz
*   六对于编写 Python 2 和 3 的代码来说是不可选的
*   客户端文档建议读者使用请求
*   与 arrow、dateutil 和 moment 等竞争对手相比，datetime 模块令人困惑
*   asyncio
*   attrs
*   扭曲的

* * *

### “什么是 mypy？”

[http://www.mypy-lang.org/](http://www.mypy-lang.org/)

mypy 是一个类型检查器。如果你以静态类型的风格编写代码，这将非常有帮助。我会把它融入到我的心流中。

* * *

### “连接到非 ascii 域名的 ssl 模块是什么？”

ssl 模块是显而易见的，但似乎不能处理非 ascii。嗯...

[https://github.com/python-trio/trio/issues/11](https://github.com/python-trio/trio/issues/11)

* * *

### “pytz 是什么，datetime 为什么需要它？”

[http://pytz.sourceforge.net/](http://pytz.sourceforge.net/)

这让我陷入了一个关于时间如何运作的疯狂的兔子洞。为什么有这么多不同的方法来处理时间？这太疯狂了。

这里有一些有用的东西:[https://stack overflow . com/questions/35057968/get-system-local-time zone-in-python/35058476](https://stackoverflow.com/questions/35057968/get-system-local-timezone-in-python/35058476)

```
from dateutil.tz import tzlocal
datetime.now(tzlocal()) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### “六是什么，为什么它对于编写 py2/3 代码是非可选的？”

[https://pypi.org/project/six/](https://pypi.org/project/six/)

我对 python2 tbh 真的不感兴趣...我很晚才开始学习 python，所以我现在主要坚持使用 3。向前而不是向后等等。如果我有一个真正强有力的理由去挖掘，我可能会，但现在...

* * *

### 没听说过箭头，没听说过瞬间，他们能做什么？

[https://arrow.readthedocs.io/en/latest/](https://arrow.readthedocs.io/en/latest/)

[https://github . com/zacwill/moment](https://github.com/zachwill/moment)

然而更多的处理时间的方法，太好了！`-_-`

* * *

### 几乎没听说过 asyncio，它能干什么？

[https://docs.python.org/3/library/asyncio.html](https://docs.python.org/3/library/asyncio.html)

如果这类似于 macOS/iOS 上 objective-c 中的 GCD，这就像是以多线程方式编写代码的一种很酷的方式。

[https://docs.python.org/3/library/asyncio-task.html](https://docs.python.org/3/library/asyncio-task.html)

```
import asyncio
import time

async def say_after(delay, what):
    await asyncio.sleep(delay)
    print(what)

async def main():
    print(f"started at {time.strftime('%X')}")

    await say_after(1, 'hello')
    await say_after(2, 'world')

    print(f"finished at {time.strftime('%X')}")

asyncio.run(main()) 
```

Enter fullscreen mode Exit fullscreen mode

预期产出:

```
started at 17:13:52
hello
world
finished at 17:13:55 
```

Enter fullscreen mode Exit fullscreen mode

我注意到的一件无关的事情是，你可以在 f 字符串中编写任意代码。总的来说，这似乎非常脆弱/糟糕。我需要写代码来玩这个。

* * *

### 不确定是否听说过 attrs，它能做什么？

[https://github.com/python-attrs/attrs](https://github.com/python-attrs/attrs)

在推荐名单上读到安珀·布朗的名字，我有点激动。用 python 写类有什么难的，需要一个完整的包来简化它？

[https://nedbatchelder.com/blog/200605/dunder.html](https://nedbatchelder.com/blog/200605/dunder.html)

他们引用了那些特殊的方法，比如`__init__`、`__repr__`等等。通常情况下，如果你想让他们在那里，你必须写这些，但我猜 attrs 会帮助处理那些跑腿的工作或其他事情。

或许值得一查。

* * *

### 我很早以前就用过 twisted 了，在 python3 支持里哪里有？

[https://twistedmatrix.com/trac/](https://twistedmatrix.com/trac/)

他们说是“的一个子集”,而且还在增长，但我们下次再来看看...

* * *

《权力的游戏》的最后一集就要开始了，我需要做好准备，去街角的商店买零食(这也算锻炼)，然后发布这个东西，所以我将它称为博客帖子。放轻松！

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)