# 我在围棋中做了一个糟糕的拼写检查

> 原文：<https://dev.to/victorqribeiro/i-made-a-terrible-spell-checker-in-go-24d>

# goSpellcheck

一个用 Go 写的拼写检查器。

# 关于

由于我从未在 Go 中真正做过任何事情，所以我决定创建一个简单的拼写检查。于是我去了([https://github . com/dwyl/English-words/)【https://github . com/dwyl/English-words/】](https://github.com/dwyl/english-words/)%5Bhttps://github.com/dwyl/english-words/%5D)弄了一堆词当字典(words.txt)。

之后，我写了一些 Go 代码来检查给定的单词是否出现在字典中，如果没有，收集另外 3 个相似的单词并作为建议返回。我第一次用 python 写类似的代码时，用的是欧氏距离和编辑距离。然而这一次，我只是一个字母一个字母地检查，如果不一样就加 1。

之后，我去了(常见拼写错误列表/For machines)[[https://en . Wikipedia . org/wiki/Wikipedia:Lists _ of _ common _ misspellings/For _ machines](https://en.wikipedia.org/wiki/Wikipedia:Lists_of_common_misspellings/For_machines)]，用我写的一个小 bash 脚本测试了我的拼写检查。

这是我做的一个不错的小项目，这样我在大学假期的时候就不会太累了。

# 热用

只需调用 *goSpellcheck.go* 文件，传递一个单词作为参数。

```
go run goSpellcheck.go word 
```

# 得分

拼写检查器从 4278 个单词中得到 1185 个，大约 27.7%。

你可以在这里看一下代码