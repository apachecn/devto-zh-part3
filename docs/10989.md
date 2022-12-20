# 合并我的第一个公关

> 原文：<https://dev.to/natec425/getting-my-first-pr-merged-2mn0>

我最近将我的第一个非平凡的 PR 合并到了一个开源项目中，我希望我的故事也能帮助你感到有能力做出贡献。

# 开源会找到你

首先，我希望你感到被授权，而不是负债，去参与。不管我们感觉如何(或被告知如何)，你可以成为一名优秀的开发人员，而不必做出任何开源贡献。

我正在帮助我的学生调试一些 DB 代码，我注意到了一个我认为可以改进的特定错误消息。

```
(sqlalchemy.exc.InvalidRequestError) A value is required for bind parameter 'id' [SQL: 'select\\n    *\\nfrom\\n     reviews\\nwhere\\n    id = %(id)s'] (Background on this error at: http://sqlalche.me/e/cd3x) 
```

明确地说，我认为这是一个很好的错误信息。它甚至包括一个简短的在线帮助链接。我只是觉得还可以改进。

> 如果你有痒，就去挠一挠。如果你觉得你对一个项目没有任何贡献。那个。是。好的。你已经做得很好了。

# 婴儿步伐

我有两个主要的痛点:

1.  终端包装一点都不好玩。也许可以做一点工作，将错误消息更优雅地拆分成多行。
2.  相关的 SQL 语句看起来与编写时略有不同。我认为可以做一些工作来使 SQL 更容易识别。

所以上面的例子会变成类似下面这样:

```
(sqlalchemy.exc.InvalidRequestError) A value is required for bind parameter 'id'
[SQL: (select
    *
from
    reviews
where
    id = %(id)s)]
(Background on this error at: http://sqlalche.me/e/cd3x) 
```

太棒了，我有一个明显的瘙痒范围。找到负责的代码没花太多时间，而且这么好看又小巧。我不得不改了两条小线。

```
 details = [self._message(as_unicode=as_unicode)]
        if self.statement:
-           details.append("[SQL: %r]" % self.statement) +           details.append("[SQL: %s]" % self.statement)
            if self.params:
                params_repr = util._repr_params(self.params, 10)
                details.append("[parameters: %r]" % params_repr)
        code_str = self._code_str()
        if code_str:
            details.append(code_str)
-       return " ".join(["(%s)" % det for det in self.detail] + details) +       return "\n".join(["(%s)" % det for det in self.detail] + details) 
```

我不得不把一辆`%r`换成一辆`%s`，把一辆`" "`换成一辆`"\n"`。

> 找到相关的代码可能需要一点工作，但是一个新手程序员可以做出这样的改变。

# 事情没那么简单

正在讨论的项目 SqlAlchemy([https://github.com/sqlalchemy/sqlalchemy/](https://github.com/sqlalchemy/sqlalchemy/))是一个流行的 Python 项目，支持 Python 2 和 3。因此，我遇到了一个与跨版本支持相关的障碍，我自己并没有做好解决这个问题的准备。此外，很多测试因为这样一个小变化而中断。看一眼最终的公关:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 问题#4500:更改语句错误格式(换行符和%s)  #4501](https://github.com/sqlalchemy/sqlalchemy/pull/4501) 

[![natec425 avatar](img/e3982941b7f742df8d297dcd9dbef67f.png)](https://github.com/natec425) **[natec425](https://github.com/natec425)** posted on [<time datetime="2019-02-17T01:30:48Z">Feb 17, 2019</time>](https://github.com/sqlalchemy/sqlalchemy/pull/4501)

这以两种方式更改了 StatementError 的错误格式:

1.  将每个错误细节分成多个新行，而不是分散在一行中。希望这能帮助读者更容易地浏览错误信息。

2.  将消息中的 SQL 表示更改为使用 **str** (%s)，而不是使用 **repr** (%r)的当前行为。这应该有助于读者识别他们的 SQL 的结构，特别是如果它是一个多行 SQL 语句。在多行的情况下，SQL 将打印在多行上，而不是打印转义的“\n”。

修复:#4500

此拉取请求是:

*   [X ]一个简短的代码修复
    *   请附上问题编号，如果不存在问题，请创建一个问题，其中必须包括问题的完整示例。没有问题的一行代码修复和演示将不被接受。
    *   请在提交消息中包含:`Fixes: #<issue number>`
    *   请包括测试。未经测试的单行代码修复将不被接受。

[View on GitHub](https://github.com/sqlalchemy/sqlalchemy/pull/4501)

这个小小的差距有点扩大了。

> 您将无法完成配置项构建。没关系。

# 但有帮助

值得庆幸的是，项目的维护者在整个过程中都非常乐于助人，非常友好。我对钻研 python2-3 的问题感到不舒服，所以他介入并解决了这个问题。如果由我来决定，我会问“那么 SqlAlchemy 什么时候停止对 python2 的支持😄?"。这就是我的最后一点。

> **解决问题**不是目标。
> **成为团队**的一员是目标。

# 重复着自己

1.  可以投稿。我觉得你不需要。你自己的工作很重要。
2.  如果你发现了一个机会，记住你能做到。
3.  这可能会以你意想不到的方式变得困难。
4.  你并不孤单。遇到障碍是团队的一部分。