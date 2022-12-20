# 用 python 创建单词表

> 原文：<https://dev.to/samsondeen/creating-wordlist-with-python-to-bruteforce-njd>

" " "单词表可以用来暴力破解。
" " "

# 用 python 创建单词表

import itertools

字母= "1 "，" 2 "，" 3 "，" 4 "，" 5 "，" 6 "，" 7 "，" 8 "，" 9 "，" 0"
n = 6

# 创建单词表

file = open("wordlist.txt "，" w ")

# 生成组合然后写入“单词表”

对于 itertools.product 中的 wl(letters，repeat = n):
file.write(")。join(wl) + "\n ")