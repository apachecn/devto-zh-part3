# 通过正则表达式实现自动完成

> 原文：<https://dev.to/kozmof/implementing-auto-completion-by-regex-kbe>

使用输入作为模式(这是主要用例的反向方式)，您可以轻松实现简单的单词自动补全功能！

```
import re

example_words = ["Python", "Linux", "HHKB", "Pizza", "Ninja"]

pattern = input()

for word in example_words:
    if re.match(pattern, word, re.IGNORECASE):
        print(word) 
```

输入示例

```
li 
```

输出

```
Linux 
```

版本:Python 3