# Git:如何合并存储库

> 原文：<https://dev.to/nabbisen/git-merging-repositories-with-local-clones-81m>

## 简介

今天下午，为了便于集成，我将我的一个项目周围的多个 Github 库合并成了一个。
这篇文章展示了几种使用本地克隆的方法。
其中一个是我实际上收养的，其他的可能对将来有好处:)

## 环境

版本控制: [Git](https://git-scm.com/) 2.21.0

## 概述

这里有一个简短的概念:

```
 "repo1" as dest   <----merge----   "repo2" as src 
```

Enter fullscreen mode Exit fullscreen mode

另外，在本教程中有一个示例目录概述:

```
 %owner-dir%/
  |
  +--repo1/     # as dest
  |    |
  |    +--file1
  |    +--common-dir/
  |    |    |
  |    |    +--fileX
  |    |
  |    +--dir1/
  |         |
  |         +--...
  |
  +--repo2/     # as src
       |
       +--file2
       +--common-dir/
       |    |
       |    +--fileY
       |
       +--dir2/
            |
            +--... 
```

Enter fullscreen mode Exit fullscreen mode

## 教程

首先，`git clone`目标仓库:

```
git clone repo1 
```

Enter fullscreen mode Exit fullscreen mode

让我们进入目录:

```
cd %owner-dir%/repo1 
```

Enter fullscreen mode Exit fullscreen mode

那么，总的来说，只有 4 个步骤来实现它:

```
#1\. Add the src repository as a new remote
#1-1\. Case of using url to specify the location of the src repository
git remote add repo2 git@github.com:%owner-dir%/repo2
# or
git remote add repo2 https://.../%owner-dir%/repo2
#1-2\. Case of using path; For example, doing after `git clone repo2 %owner-dir%`
git remote add repo2 %owner-dir%/repo2

#2\. Fetch the contents of the src repository
git fetch repo2

#3\. Merge them
### Here choose an operation from below. ###

#4\. Push the changes
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

### 操作

它们是#3 的选项。合并它们”。我认为 1)-2)是基本的，其他的 3)-4)是可选的。

#### 前言

在 Git 2.9.0 和更高版本中，不同存储库的`git merge`命令需要`--allow-unrelated-histories`选项以避免`fatal: refusing to merge unrelated histories`错误。

除了 4)之外，src 存储库中的提交历史被保留。

#### 1)知识库合并

```
git merge repo2/master --allow-unrelated-histories 
```

Enter fullscreen mode Exit fullscreen mode

将添加 repo2 的所有文件和目录。

结果:

```
 repo1/
  |
  +--file1
  +--file2            (+)
  +--dir1/
  +--common-dir/
  |    |
  |    +--fileX
  |    +--fileY       (+)
  |
  +--dir2/            (+) 
```

Enter fullscreen mode Exit fullscreen mode

#### 2)子树合并

```
git merge -s subtree repo2/master --allow-unrelated-histories 
```

Enter fullscreen mode Exit fullscreen mode

使用此方法，可以添加与目标存储库名称相同的子目录以及根目录中的文件。
因此，在这种情况下，添加的只是两个文件，`common-dir`目录下的`fileY`和根目录下的`file2`文件。

结果:

```
 repo1
  |
  +--file1
  +--file2            (+)
  +--dir1/
  +--common-dir/
       |
       +--fileX
       +--fileY       (+) 
```

Enter fullscreen mode Exit fullscreen mode

#### 3)指定子目录的子树合并

`git merge -X<option>`令我困惑...

##### 3)-1 指定 dest repo 和 src 都有的子目录

```
git merge -X subtree=common-dir repo2/master --allow-unrelated-histories 
```

Enter fullscreen mode Exit fullscreen mode

添加根目录中的文件和指定子目录中的文件。

结果:

```
 repo1
  |
  +--file1
  +--file2            (+)
  +--dir1/
  +--common-dir/
       |
       +--fileX
       +--fileY       (+) 
```

Enter fullscreen mode Exit fullscreen mode

##### 3)-2 指定仅 src repo 拥有的子目录

```
git merge -X subtree=dir2 repo2/master --allow-unrelated-histories 
```

Enter fullscreen mode Exit fullscreen mode

只添加根目录中的文件。

结果:

```
 repo1
  |
  +--file1
  +--file2            (+)
  +--dir1/
  +--common-dir/
       |
       +--fileX 
```

Enter fullscreen mode Exit fullscreen mode

##### 3)-3 指定仅目标存储库拥有的子目录

```
git merge -X subtree=dir1 repo2/master --allow-unrelated-histories 
```

Enter fullscreen mode Exit fullscreen mode

创建名为指定目录的目录，然后将 src repo 中的所有文件和目录添加到该目录中。

结果:

```
 repo1
  |
  +--file1
  +--dir1/
  |    |
  |    +...
  |    +--file2       (+)
  |    +--common-dir/ (+)
  |    |    |
  |    |    +--fileY  (+)
  |    |
  |    +--dir2/       (+)
  |
  +--common-dir/
       |
       +--fileX 
```

Enter fullscreen mode Exit fullscreen mode

#### 4)合并为一个新的子目录

```
git read-tree --prefix=%new-subdir%/ repo2/master
git checkout -- .
git add .
git commit -m "commit messages" 
```

Enter fullscreen mode Exit fullscreen mode

不会继承 src repo 的原始提交历史记录。

结果:

```
 repo1
  |
  +--file1
  +--dir1/
  +--common-dir/
  |    |
  |    +--fileX
  |
  +--%new-subdir%     (+)
       |
       +--file2       (+)
       +--common-dir/ (+)
       |    |
       |    +--fileY  (+)
       |
       +--dir2/       (+) 
```

Enter fullscreen mode Exit fullscreen mode

## 在关闭

作为参考，这些是关于上述 Git 命令和策略的官方文档:

| 命令 | 统一资源定位器 |
| --- | --- |
| `git clone` | [https://git-scm.com/docs/git-clone](https://git-scm.com/docs/git-clone) |
| `git remote` | [https://git-scm.com/docs/git-remote](https://git-scm.com/docs/git-remote) |
| `git fetch` | [https://git-scm.com/docs/git-fetch](https://git-scm.com/docs/git-fetch) |
| `git merge` | [https://git-SCM . com/docs/git-merge](https://git-scm.com/docs/git-merge) |
| 合并策略 | [https://git-scm.com/docs/merge-strategies](https://git-scm.com/docs/merge-strategies) |
| `git push` | [https://git-SCM . com/docs/git-推送](https://git-scm.com/docs/git-push) |

感谢您的阅读:)