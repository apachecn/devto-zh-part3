# 从 Mercurial 历史记录中删除文件

> 原文：<https://dev.to/alexjitbit/removing-files-from-mercurial-history-1b15>

有时，您需要从 Mercurial 中完全删除文件，甚至从历史记录中删除。例如，如果您意外地在 repo 中存储了一个敏感文件(某个密码，或者访问密钥，或者代码签名证书等等。等等。)或者犯了巨大的二进制。以下是移除它的方法:

## 输入“转换”分机

您将需要`hg convert`命令。该命令通常用于将 SVN/GIT 存储库转换为 Mercurial，但是它也可以用于将**从 Mercurial 转换为 Mercurial** 。

最酷的是`--filemap`选项，它指定了哪些文件应该包含在转换过程中，哪些不应该。

将此代码添加到`.hgrc`以启用扩展:

```
[extensions]
hgext.convert= 
```

## 从 hg 中删除文件的步骤

1.  确保您的所有团队成员已经将他们的本地变更提交到中央回购(如果有的话)

2.  备份您的存储库

3.  创建一个“map.txt”文件:

```
# this filemap is used to exclude specific files
exclude "subdir/filename1.ext"
exclude "subdir/filename2.ext"
exclude "subdir2" 
```

然后运行这个命令:

```
hg convert --filemap map.txt c:/oldrepo c:/newrepo 
```

注意:你必须在路径中使用“正斜杠”,即使是在 Windows 上。

那就等等，耐心点。过一会儿，你会在`c:\newrepo`有一个新的回购，只是没有文件

## (可选)将新回购推回中央回购(如 Bitbucket)

如果您有一些中央 hg 存储，您将不得不“剥离”所有的变更集，然后再次“推送”您的本地存储库。

例如，如果你像我们一样使用 Bitbucket，请进入“管理条”。在“修订”框中输入“0 ”,这将删除所有提交，但“wiki”和“issues”区域将被保留。然后——再次将本地回购推回 Bitbucket。

但是有一个重要的问题——因为上面的过程会影响历史，并为所有的变更集重新分配新的“id ”,所以您的团队成员必须再次从中央 repo 中重新克隆 repo。