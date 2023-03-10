# 如何将几个提交合并成一个？

> 原文：<https://dev.to/andreisfedotov/how-to-join-several-commits-into-one-323j>

#### 首先道:

**1)** 考虑我们有以下提交历史:

```
* 493f284 2019-01-11 | fourth (HEAD -> master) [Andrei Fedotov]
* d2fd771 2019-01-11 | second 3 [Andrei Fedotov]
* 4ad568e 2019-01-11 | second 2 [Andrei Fedotov]
* 1c29a96 2019-01-11 | second 1 [Andrei Fedotov]
* ca8ad26 2019-01-11 | first [Andrei Fedotov] 
```

Enter fullscreen mode Exit fullscreen mode

我们希望将*第二个 1* 、*第二个 2* 和*第二个 3* 提交合并成一个名为*第二个*的提交。
执行下一个命令:

`git rebase -i HEAD~4`

**2)** 之后，提交列表窗口将按其创建顺序打开(从上到下):

```
pick 1c29a96 second 1
pick 4ad568e second 2
pick d2fd771 second 3
pick 493f284 fourth 
```

Enter fullscreen mode Exit fullscreen mode

**3)** 我们想要加入之前提交的提交，我们必须将其标记为*挤压*。我们希望保持不变的提交我们保持为 *pick*

```
pick 1c29a96 second 1
squash 4ad568e second 2
squash d2fd771 second 3
pick 493f284 fourth 
```

Enter fullscreen mode Exit fullscreen mode

**4)** 保存并关闭窗口。下一个窗口将会打开，我们应该在这里为我们的联合提交键入消息。因此，我们得出:

```
* 7a98610 2019-01-11 | fourth (HEAD -> master) [Andrei Fedotov]
* 5ef60fc 2019-01-11 | second [Andrei Fedotov]
* ca8ad26 2019-01-11 | first [Andrei Fedotov] 
```

Enter fullscreen mode Exit fullscreen mode

#### 另一种方式:

**1)** 假设我们又添加了一个提交，我们想将最后两个提交合并成一个:

```
* 865a0f6 2019-01-11 | third (HEAD -> master) [Andrei Fedotov]
* 7a98610 2019-01-11 | fourth [Andrei Fedotov]
* 5ef60fc 2019-01-11 | second [Andrei Fedotov]
* ca8ad26 2019-01-11 | first [Andrei Fedotov] 
```

Enter fullscreen mode Exit fullscreen mode

键入以下命令:

```
git reset --hard HEAD~2
git merge --squash HEAD@{1}
git commit 
```

Enter fullscreen mode Exit fullscreen mode

在打开的窗口中键入提交的名称。比如:*第三*。现在我们有:

```
* c79f494 2019-01-11 | third (HEAD -> master) [Andrei Fedotov]
* 5ef60fc 2019-01-11 | second [Andrei Fedotov]
* ca8ad26 2019-01-11 | first [Andrei Fedotov] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们合并的提交之前被推送到服务器，我们必须更新它们。可以通过输入下一个命令:

`git push origin +branch_name`

此外，我们可以使用有用且方便的别名:
**Bash** :

```
git config --global alias.squash '!f(){ git reset --soft HEAD~${1} && git commit --edit -m"$(git log --format=%B --reverse HEAD..HEAD@{1})";};f' 
```

Enter fullscreen mode Exit fullscreen mode

**Cmd** :

```
git config --global alias.squash "!f(){ git reset --soft HEAD~${1} && git commit --edit -m\"$(git log --format=%B --reverse HEAD..HEAD@{1})\";};f" 
```

Enter fullscreen mode Exit fullscreen mode

现在可以使用这个命令组合最后 N 次提交:

`git squash N`

干杯！