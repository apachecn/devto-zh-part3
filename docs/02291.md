# Python 警告# 1:Python 字典中的多行注释

> 原文：<https://dev.to/ivarojha/python-caveats-1-multiline-comment-within-a-python-dict-1k0k>

假设我们有一个 Python 字典，我们要记录键值对的一般格式。由于缺乏更好的例子，我们就以下面的 dict 为例:

```
family = {
    "goku": {
        "name": "Goku",
        "race": "Saiyan",
        "aliases": ["Son Goku", "Kakarot"],
    },
    "chi-chi": {
        "name": "Chi Chi",
        "race": "Human",
        "aliases": ["The Ox-Princess"],
    },
    "gohan": {
        "name": "gohan",
        "race": "Saiyan",
        "aliases": ["Great Saiyaman", "Son Gohan"],
    },
} 
```

键值对的一般格式是:

```
"username": {
    "name": "",
    "race": "",
    "aliases" [],
} 
```

为了添加注释，我们可以这样做:

```
family = {
    """
    General format of entries is

    "username": {
        "name": "",
        "race": "",
        "aliases" [],
    }

    """
    "goku": {
        "name": "Goku",
        "race": "Saiyan",
        "aliases": ["Son Goku", "Kakarot"],
    },
    "chi-chi": {
        "name": "Chi Chi",
        "race": "Human",
        "aliases": ["The Ox-Princess"],
    },
    "gohan": {
        "name": "gohan",
        "race": "Saiyan",
        "aliases": ["Great Saiyaman", "Son Gohan"],
    },
} 
```

现在让我们检查一下这首歌的音调。

```
In [15]: family.keys()
Out[15]: 
['\n    General format of entries is\n\n    "username": {\n        "name": "",\n        "race": "",\n        "aliases" [],\n    }\n\n    goku',
 'gohan',
 'chi-chi'] 
```

停下来。为什么要修改`goku`字符串？这是因为在 Python 中，用`"""`包围某物并不一定意味着是注释。它只是一个多行字符串。把它想象成一个字符串，不赋给任何变量。与其他编程语言不同，Python 中没有多行注释这种东西。放置多行注释的正确方法是使用`#`。

您可能还记得使用`"""`将注释放在函数/类的开头，也称为文档字符串。你知道吗，那些文档字符串并不是真正的注释。它只是一个字符串和一个标准约定[<sup>【1】</sup>](#note2)。

所以在 dict 中放置注释的正确方式应该是

```
family = {

    # General format of entries is
    # "username": {
    #    "name": "",
    #    "race": "",
    #    "aliases" [],
    # }

    "goku": {
        "name": "Goku",
        "race": "Saiyan",
        "aliases": ["Son Goku", "Kakarot"],
    },
    "chi-chi": {
        "name": "Chi Chi",
        "race": "Human",
        "aliases": ["The Ox-Princess"],
    },
    "gohan": {
        "name": "gohan",
        "race": "Saiyan",
        "aliases": ["Great Saiyaman", "Son Gohan"],
    },
} 
```

#### TL；博士；医生

Python 中没有多行注释这种东西。在每一行使用`#`来真正创建多行注释。

[人教版 257 - Docstring 约定](https://www.python.org/dev/peps/pep-0257/)

最初发布于[rookieslab.com](https://www.rookieslab.com/posts/python-caveats-multiline-comment-inside-a-dict)