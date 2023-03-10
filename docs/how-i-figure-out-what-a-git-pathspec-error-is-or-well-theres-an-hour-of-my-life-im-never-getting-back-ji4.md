# 我是如何弄清楚什么是 git pathspec 错误的，或者“好吧，我生命中有一个小时再也回不来了”

> 原文：<https://dev.to/johnhorner/how-i-figure-out-what-a-git-pathspec-error-is-or-well-theres-an-hour-of-my-life-im-never-getting-back-ji4>

最近，我不得不从 git repo 中取回一个特定文件的早期版本。

就 git 而言，这很简单:

```
git checkout <commit id> <filename> 
```

所以，如果你想恢复提交时的版本，你可以这样做

```
git checkout f6b01b3 file.txt 
```

这就是你会得到的。

但是当我这么做的时候，我得到了这个:

```
error: pathspec 'file.txt' did not match any file(s) known to git 
```

花了很长时间才发现问题所在。

事情是这样的，上面结构中的一个文件夹被重命名了。

以下是如何重现所发生的事情。让我们在文件夹中创建一个 git repo 和一个文件:

```
john$ git init
Initialized empty Git repository in /Users/john/Library/Scripts/gitexample/.git/
john$ mkdir originalfolder
john$ cd originalfolder/ 
```

我们创建一个文件，添加并提交它。一切都好。

```
john$ echo "this is the initial file" > file.txt
john$ git add file.txt 
john$ git commit file.txt -m "initial"
[master (root-commit) f6b01b3] initial
 1 file changed, 1 insertion(+)
 create mode 100644 originalfolder/file.txt 
```

现在，我们改变了对文件夹名称的想法，我们将重命名它(请注意，这是`git mv`而不仅仅是`mv`):

```
john$ cd ../
john$ git mv originalfolder/ changedfolder
john$ cd changedfolder/
john$ echo "now I've added a line" >> file.txt 
```

我们将提交该文件的新版本:

```
john$ git commit file.txt -m "second"
[master ab9595b] second
 1 file changed, 2 insertions(+)
 create mode 100644 changedfolder/file.txt 
```

那里没问题。

现在我们想从最初的提交中取回`file.txt`的版本。

```
john$ git checkout f6b01b3 file.txt 
error: pathspec 'file.txt' did not match any file(s) known to git 
```

git 现在说它有一个 pathspec 错误，它找不到`file.txt`，也就是*就在那里* …

显然，问题在于提交`f6b01b3`时，它在一个文件夹中，而那个文件夹已经不存在了。

如果您想要恢复该文件，您需要将其恢复到原始路径:

```
john$ mkdir ../originalfolder
john$ mkdir cd ../originalfolder/
john$ git checkout f6b01b3 file.txt 
```

这没有任何问题。