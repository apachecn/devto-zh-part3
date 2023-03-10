# 撤销 Git 中的更改

> 原文：<https://dev.to/zellwk/undoing-changes-in-git-36m>

[https://www.youtube.com/embed/za-DSrhiXJY](https://www.youtube.com/embed/za-DSrhiXJY)

# 用 Git 撤销

至此，您已经知道 Git 就像一个保存点系统。你到目前为止所做的就是学会储蓄。但是你如何撤销，并回到以前的状态呢？

这就是我们要讲的内容

## 本地 vs 远程

撤销已经在遥控器上的东西更复杂。这就是为什么你想把事情留在你的本地，直到他们得到某种确认。

## 四种常见场景

1.  放弃本地更改
2.  修改以前的提交
3.  回滚到上一次提交
4.  恢复已推送到远程的提交

## 场景 1:丢弃本地更改

第一个场景是当您创建了一些更改时。他们还没有承诺。并且您想要删除这些更改。

假设我们想要创建一个新的特性。我们将在项目中添加一些 HTML 和 CSS:

```
<!--In index.html-->
<div class="feature"></div> 
```

```
/* In CSS file */
.feature {
  font-size: 2em; 
  /* Other styles */
} 
```

要放弃这些更改:

1.  去集结地
2.  选择要放弃更改的文件
3.  右键单击文件
4.  选择放弃更改

[![Dischard changes in the contextual menu](img/9069033a7b8e1b10b5a0cfa60420b37d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--trqCh7kj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-undo/discard.png)

## 场景 2:修改之前的提交

当您创建了一个 commit 并且遗漏了一些更改时。您希望在之前的提交消息中添加这些更改。

您可以:

1.  去集结地
2.  暂存要提交的文件
3.  点击修改复选框
4.  编辑您的提交消息
5.  犯罪

[![The amend checkbox in the commit area](img/bfeabac898f2544fa79b53d497d651a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T1kHjV6T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-undo/amend.png)

## 场景 3:回滚到上一次提交

您的本地存储库中已经有一些提交。您决定不再需要这些提交。您希望从以前的状态“加载”您的文件。

您可以:

1.  进入 Git 历史
2.  右键单击要回滚到的提交
3.  在这里选择复位`branch`

[![Reset option in the contextual menu](img/250103dd6dd7413b59c54eeaf1a373d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--85ROt6gm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-undo/reset.png)

注意:您只能重置到尚未推入远程的提交。

## 场景 4:恢复已推送到远程的提交

如果您有一个已经被推入远程分支的提交，您需要恢复它。

恢复意味着通过创建新的提交来撤消更改。如果您添加了一行，此还原提交将删除该行。如果您删除了一行，此还原提交会将该行添加回来。

要恢复，您可以:

1.  转到 Git 历史
2.  右键单击要恢复的提交
3.  选择还原提交
4.  确保勾选了`commit the changes`。
5.  单击还原

[![Revert in the contextual menu](img/ac3d0ec15a370dd67f06e94ba350fd36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h8vhTUCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-undo/revert.png)

[![Commit the changes option is checked](img/61bbd671d7199924c9ef1dd6874cc401.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eW1Q185m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2018/git-undo/revert2.png)

## 其他场景

Github 有一篇有用的文章向你展示了如何用 Git 撤销几乎所有的事情。如果面对其他场景会有帮助。在这里阅读[。](https://blog.github.com/2015-06-08-how-to-undo-almost-anything-with-git/)

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。