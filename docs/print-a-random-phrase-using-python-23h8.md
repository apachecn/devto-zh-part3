# 使用 Python 打印一个随机短语

> 原文：<https://dev.to/voyeg3r/print-a-random-phrase-using-python-23h8>

几天前，我分享了一个小小的 shell 脚本，从我的一篇学习文本中随机选择了一个段落。现在让我们看看它使用 python 的版本

```
#!/usr/bin/env python3
#  -*- coding: utf-8 -*-
#         vim: ai ts=4 sts=4 et sw=4
#    Filename: randphrase.py
#      Author: Sergio Araujo
#     Purpose: read a random paragraph from a file
# Last Change: mai 31 2019 19:02
#     Created: sex 31 mai 2019 18:47:16 -03
#       email: <voyeg3r âœ‰ gmail.com>
#      Github: https://github.com/voyeg3r
#     twitter: @voyeg3r
#
# sources: 
# https://stackoverflow.com/a/40197938/2571881
# https://stackoverflow.com/a/509295/2571881 
import random
import os

# clear the screen (I have used the os module just for the sake of cleaning the screen) def cls():
    if os.name == 'posix':
        os.system('clear')
    else:
        os.system('cls')

cls()

file = "/home/sergio/.dotfiles/nvim/wiki/phrases.md"

par = open(file).read().split('\n\n')

# here I pick up a random paragraph (avoiding the two first paragraphs because they 
# are explanatory for the file and the last one wich contains tags) print()
print(random.choice(par[2:-1]))
print() 
```

Enter fullscreen mode Exit fullscreen mode