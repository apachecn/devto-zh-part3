# 用 Shell 脚本打印一个随机短语

> 原文：<https://dev.to/voyeg3r/print-a-random-phrase-with-shell-script-3jmb>

几天前，我有了一个想法，从一个文件中获取一个随机的短语，我习惯于保存我正在学习的新英语单词的短语。初稿是这样的:

```
#!/usr/bin/env bash
#     Filename: getphrase.sh
#      Created: 2019-05-24 07:33
#  Last Change: May 24 2019 08:23
#       Author: Sérgio Araújo - voyeg3r at gmail

# PRINT A RANDOM PARAGRAPH FROM A GIVEN FILE
# I have a file called phrases were I collect phrases in English
# that I am learning and I had the crazy idea of getting on random phrase

# This script will get a random phrase (the criteria is: paragraphs separated by blank lines)

# target file
file=~/.dotfiles/nvim/wiki/phrases.md

# count registers (number of phrases)
# RS -> Register Separator == "" (which means blank lines)
# FS -> Field Separator == "\n" (which means line breaks)
# NR -> prints the register number for all registers
# once we want the number of registers we are getting the last one
COUNT=$(awk 'BEGIN {RS="";FS="\n"} {print NR}' $file | tail -1)

# print a random number: shuf -i 1-10 -n 1
# from the third phrase (the first two are explanatory of the file)
# the last line contains tags, nor a real phrase so I have to get
# from the third paragraph to the end minus one
# var=10 ; echo $((var - 2)) 
RANDPHRASE=$(shuf -i 3-$((COUNT -1)) -n 1)

clear
echo "phrase number: $RANDPHRASE"
echo

# if the NR (number of register) is equal to "number" 
awk -v number=$RANDPHRASE 'BEGIN {RS="";FS="\n"} NR==number' $file
echo 
```

Enter fullscreen mode Exit fullscreen mode