# 不要使用 git stash pop

> 原文：<https://dev.to/jaga/don-t-use-git-stash-pop-4m3p>

[![room-of-requirement](img/cd8ae7375d4145405e3f5c2bebd5636d.png)](///static/27d7ec9074e09ad28289a938649ffbc4/36da7/room-of-requirement.jpg)

## 为什么？

git `stash`是一个非常强大的概念，它就像哈利波特中你的代码的*需求室*，你把它扔在这里，你可以在将来再次需要它的时候把它拿回来。

但是处理它可能是危险的。

运行`git stash pop`在应用后扔掉存储。`git stash apply`将它留在隐藏列表中，以后可以重新使用(或者使用`git stash drop`将其删除)

> 注意:如果在`pop`期间有冲突，它的行为就像 git stash apply 一样，并且 stash 不会丢失

##### 值得注意

`git stash pop`相当于`git stash apply && git stash drop`

## 结论

当你搞砸了从仓库里拿东西的时候，有时可以成为救命恩人

 形象功劳: