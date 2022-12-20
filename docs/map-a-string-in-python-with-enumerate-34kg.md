# 使用枚举在 Python 中映射字符串

> 原文：<https://dev.to/flaviabastos/map-a-string-in-python-with-enumerate-34kg>

问题:创建一个字符串中字母和索引的映射。

我的第一种方法是使用 *range(len(s))* 遍历字符串，并在添加字母之前检查它是否存在于映射中:

```
 s = 'mozzarella'
    mapped_s = {}
    for i in range(len(s)):
        if s[i] in mapped_s:
            mapped_s[s[i]].append(i)
        else:
            mapped_s[s[i]] = [i]
    print(f' mapped: {mapped_s}') 
```

那管用:

```
{'m': [0], 'o': [1], 'z': [2, 3], 'a': [4, 9], 'r': [5], 'e': [6], 'l': [7, 8]} 
```

但是有一种更 pythonic 化的方法来获得这个映射:使用 enumerate()！

```
 letters = 'mozzarella'
    mapped_s = collections.defaultdict(list)
    for index, letter in enumerate(letters):
        mapped_s[letter].append(index)
    print(f'mapped: {dict(mapped_s)}') 
```

Enumerate 是一个 [python 内置函数](https://docs.python.org/3.6/library/functions.html#enumerate)，它给出了索引的元组列表和 iterable 中的元素:

```
 print(f'enum {list(enumerate('mozzarella'))}')
enum [(0, 'm'), (1, 'o'), (2, 'z'), (3, 'z'), (4, 'a'), (5, 'r'), (6, 'e'), (7, 'l'), (8, 'l'), (9, 'a')] 
```

它可以用来循环遍历 iterable，同时跟踪索引和元素，类似于 _ range(len(s))_ 在我的第一个例子中所做的:

```
 for i, element in enumerate('mozzarella'):
        print(f'{i}: {element}') 
```

它还消除了在创建字母映射之前检查字母是否已经存在的需要！

你可以在文档中阅读更多关于枚举[的内容，或者阅读由](https://docs.python.org/3.6/library/functions.html#enumerate)[丹·巴德](https://dbader.org/blog/python-enumerate)写的这篇超棒的文章。

> 帖子[用 Python 映射一个字符串并枚举](https://wp.me/pa0b0y-1n)最初发表在 [flaviabastos.ca](https://flaviabastos.ca) 上