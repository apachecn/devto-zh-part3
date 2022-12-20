# 让 git 忘记跟踪的文件

> 原文：<https://dev.to/varunbarad/make-git-forget-a-tracked-file-263h>

Android Studio 的“新建项目”向导生成的默认`.gitignore`文件保持了一些 VCS 本地 IDE 配置文件的跟踪。现在，我会在创建一个新项目后立即调整`.gitignore`文件，以避免这些文件被跟踪。

但是上周我打开了我的一个旧项目，当时我还没有对`.gitignore`做那些修改，所以它正在跟踪那些文件。我希望 git 停止跟踪这些文件，但仍然希望将它们保存在我的文件系统中。

所以我去寻找如何做到这一点，并找到了以下方法。

1.  提交/隐藏所有其他更改
2.  将该文件添加到您的`.gitignore`
3.  提交`.gitignore`更改
4.  执行命令`git rm --cached <file>`(针对您想要从跟踪中移除的每个文件)
5.  承诺将这些文件从 VCS 移走。

**注意:**这将把文件保留在您的目录中，但下次在其他机器上进行这些更改时，删除的文件将从该机器上删除。因此，建议在提取和粘贴复制的文件之前，先制作这些文件的副本。

来源: [Stackoverflow](https://stackoverflow.com/a/1274447)

这只是我最近需要的一个小技巧，想和大家分享一下。如果你有更多这样的建议，我很乐意在这个 [twitter 帖子](https://twitter.com/varun_barad/status/1136608795973603332)中听到。如果你有任何想谈论的事情，请随时联系我 [@varun_barad](https://twitter.com/varun_barad) 。