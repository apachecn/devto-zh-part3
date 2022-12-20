# 启动 Git 和 GitHub 的基本命令

> 原文：<https://dev.to/umeshdhakar/basic-commands-to-start-git-and-github-4157>

大家好，Git 和 GitHub 对我们这样的人来说是非常有用的工具。记下我们正在学习的任何东西是一个好习惯，有许多在线工具可以让记笔记变得更容易。
[notepad.pw](https://notepad.pw) 和 [simplenote](https://simplenote.com/) 是一些干净简单的笔记工具。可喜的是，我在学习 Git 的同时也养成了这个好习惯，创建了一个文档。一些好的[dev to](https://dev.to/)帖子激励我拿出这些文档并写在这里，这可能会帮助这里的某些人，当然也包括我。下面是在 GitHub(远程存储库)上启动 Git 和托管源代码的基本命令

安装 git。
`sudo apt-get install git`

在本地目录中创建一个 git 存储库。
`git init`

设置您的全球用户详细信息。
T0
T1】

创建一个自述文件来存储存储库的描述。
`touch README.md`

创建一个文件来忽略存储库中的一些文件和目录。
(写文件和目录的相对路径，这里也可以使用通配符)
`touch .gitignore`

创建项目文件或粘贴当前目录中的现有文件。

添加已更改的文件进行提交。
`git add <filename>`
或者一次性添加所有文件
`git add .`

检查存储库的状态。
`git status`

是时候在本地提交第一批更改了。
`git commit -m "initial commit" <file>`

我们所做的一切都存储在本地，现在我们将本地存储库链接到远程存储库。

在 GitHub 上创建一个新的资源库(现在我们也可以在 GitHub 上创建无限的私人回购，感谢 GitHub 的新年礼物😊).
单击绿色按钮“克隆或下载”,复制存储库的 URL。

将远程存储库与本地存储库链接起来。
`git remote add origin <repo_url>`

将更改推送到远程存储库的主分支。
`git push -u origin master`

**多了一个命令。💡**
如果你不小心提交了更改或者想撤销提交，这些命令可以节省你的时间。
`git reset --hard HEAD^`
`git push -f`

感谢您的阅读😊。还有更多的命令。这些只是可以帮助开始的基础。如果你有任何疑问，请告诉我，我很乐意帮助你。