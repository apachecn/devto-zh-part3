# 我如何提高我的 python 技能

> 原文：<https://dev.to/riquellopes/how-do-i-improve-my-python-skills-23jp>

我会给你一个简单的技巧来提高你的 python 技能。这很简单，额外的好处是，许多技术公司在那里应用你的挑战。我说的是 Hackerrank，这是一个著名的平台，你可以在这里练习编码和找工作。我通常每周接受一次编码挑战，我喜欢数学问题和 Python。

你可以在那里找到一系列的问题，从基础到最难的，每一步你都会遇到不同层次的问题。我用这个工具来更好地思考和练习。我的档案联系人是康塔托 44。我将分享一个你可以在 Hackerrank 上看到的简单问题:

```
"""
    >>> count_substring("ABCDCDC", "CDC")
    2
    >>> count_substring("ABCDCDCAAAACDC", "CDC")
    3
    >>> count_substring("ThIsisCoNfUsInG", "is")
    1
"""

def count_substring(string, sub_string):
    occurrences = 0

    for i, __ in enumerate(string):
        cut = string[i:len(sub_string)+i]
        if cut == sub_string:
            occurrences += 1
    return occurrences 
```

为了解决这个问题，我使用了 [Doctest](https://docs.python.org/2/library/doctest.html) ，因为编写测试既简单又快速。我用 [Doctest](https://docs.python.org/2/library/doctest.html) 提交解决方案。您可以在您的机器上执行两种模式的 [Doctest](https://docs.python.org/2/library/doctest.html) :

#### 第一模式:

在执行代码后，在最终代码中添加代码片段。

```
if __name__ == "__main__":
   import doctest
   doctest.testmod() 
```

运行测试功能。

```
$ python Findastring.py 
```

#### 第二模式:

ps:当我使用 Doctest 运行我的测试特性时，我会这样做。

```
$ python -m doctest Findastring.py 
```

今天我分享一个简单的技巧来提高你的开发者技能，如果有其他的技巧，请告诉我。

原文发布于:
[https://medium . com/@ riquellopes/how-do-I-improve-my-python-skills-89075 F2 e22e 2](https://medium.com/@riquellopes/how-do-i-improve-my-python-skills-89075f2e22e2)