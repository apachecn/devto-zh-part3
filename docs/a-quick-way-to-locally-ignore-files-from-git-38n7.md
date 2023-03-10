# 从 Git 本地忽略文件的快速方法。

> 原文：<https://dev.to/siaw23/a-quick-way-to-locally-ignore-files-from-git-38n7>

很多时候，我发现自己不得不忽略一些我不应该推送到 GitHub 上的存储库的文件，所以我会有选择地做一些类似于`git add file.rb another_file.rb`的事情，这样我就可以只提交我想要更改的文件。这是乏味的，更不用说巨大的时间浪费。

如果你熟悉 Rails，有时你需要对你的`database.yml`或者甚至是你的 Gemfile 进行修改。您对这些文件所做的更改只针对您自己，您不需要将其推送给其他所有人，您也不想编辑`.gitignore`。

这里有一个简单的方法可以在本地忽略您的更改，这样您就不必一直错误地跟踪它们，或者花时间有选择地跟踪一些文件。

我将这些命令添加到我的别名中，以便于访问:

`alias untrack='git update-index --assume-unchanged'`

你可以这样使用它:`untrack database.yml`

要撤销这个操作，你可以做`track database.yml`，但前提是你已经添加了`track='git update-index --no-assume-unchanged'`，当然`untrack`和`track`可以是你选择的任何东西。

编码快乐！