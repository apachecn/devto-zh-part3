# 饭桶 reflog 救了我一命。

> 原文：<https://dev.to/leanminmachine/git-reflog-saved-my-ass-bmc>

# 我是个饭桶。

我像往常一样惊慌失措，因为我不小心将我的更改提交到了主分支。我们应该`git checkout`创建一个新的分支，在合并到 master 之前打开一个 pull 请求。

所以我疯狂地搜索，然后出现了这个:

### 去复位-软

[git check out——如何将 Git 存储库恢复到之前的提交堆栈溢出状态](https://stackoverflow.com/questions/4114095/how-to-revert-a-git-repository-to-a-previous-commit)
`git reset --soft <commit>`

*   保留更改的同时恢复原状。
*   不改变工作树。
*   返回提交状态，如取消文件暂存。

我试过用`git reset --soft HEAD^`。我还是不确定 HEAD^和海德有什么不同。显然:

```
Use ~ most of the time — to go back a number of generations, usually what you want
Use ^ on merge commits — because they have two or more (immediate) parents 
```

来源:[git 中的 HEAD^和 HEAD~有什么区别？-堆栈溢出](https://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git)

所以它起作用了，我的改变没有被搁置。但是当我换到一个新的分支的时候，它好像消失了？？所以我很惊慌，因为我通常使用`git status`和`git log`来检查修改的文件，暂存/未暂存等。我非常害怕，我所有的代码都不见了。

### git reflog

谢天谢地，在谷歌搜索之后，我找到了这个命令:`git reflog` — [恢复 git 重置—软堆栈溢出](https://stackoverflow.com/questions/4589251/recovering-a-git-reset-soft) —它记录了不再在我的工作树中的提交。瞧，我的承诺还在。唷，我所要做的就是重置回提交状态，并确保现在将其向上推。

有点奇怪为什么`git log`不显示提交。