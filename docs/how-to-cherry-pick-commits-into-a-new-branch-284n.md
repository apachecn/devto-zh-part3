# 如何挑选提交到一个新的分支

> 原文：<https://dev.to/grepliz/how-to-cherry-pick-commits-into-a-new-branch-284n>

您是否曾经发现自己处于这样一种情况:您需要在 git 的一个新分支中挑选一些您自己的提交？我个人不喜欢使用`git cherry-pick`,因为这通常是出错的信号，但不幸的是，有时这是摆脱棘手 git 情况的唯一方法。最近我在工作中遇到了这种情况，我采取了这些步骤来重建我的分支机构。

## **第一步**

首先，我需要搜索提交列表，找到与我相关的提交:

`git log --pretty=format:"%h%x09%an%x09%s" | grep "Liz Lam"`

哇哦。？你刚才问的是什么？！！？？！我们来详细看一下。

给你一个存储库中的提交列表。

`--pretty=format:`允许您以对您有意义的方式格式化提交(或者在这种情况下，对我有意义)。

`%h`版画《SHA1 杂记》(短版)。

`%x09`打印标签。

`%an`打印作者姓名。

`%s`打印提交的摘要。

所以把它放在一起，`git log --pretty=format:"%h%x09%an%x09%s"`会输出这样的东西:

```
02705d38d4    Fabien Villepinte    l10n: fr.po Fix some typos from round3    
5a05494049    Fabien Villepinte    l10n: fr.po Fix some typos
ca1b411648    Johannes Schindelin   mingw: safe-guard a bit more against getenv` 
```

Enter fullscreen mode Exit fullscreen mode

然后我把它输入到`grep "Liz Lam"`中，只得到我的提交。

## **第二步**

一旦我有了提交列表，我将检查并手动选择我感兴趣的那些。假设我想出了这样一个清单:

`ff92f85cac`
`c8879ed156`
T2】

我随后创建了一个新的分支并摘下了 SHA1 提交的列表:

`git checkout -b my_new_branch`

`git cherry-pick ff92f85cac`
`git cherry-pick c8879ed156`
`git cherry-pick b01cb9a3f9`

瞧啊。现在，我有了一个新创建的分支，其中包含了我新挑选的提交。