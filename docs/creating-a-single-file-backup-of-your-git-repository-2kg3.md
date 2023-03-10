# 创建 git 存储库的单个文件备份

> 原文：<https://dev.to/varunbarad/creating-a-single-file-backup-of-your-git-repository-2kg3>

我创建了一个 python 脚本，它创建了一个包含 git-bundle 和所有补丁文件的 zip 文件，用于存储库中不同的隐藏更改。

上周，我需要备份我笔记本电脑的内容，因为我需要把它送去维修。当我开始简单地将文件复制到外部硬盘时，我注意到 git 文件(git 用来存储项目版本历史的文件)花费了太多的时间，因为它们非常小，但是数量巨大。所以我决定找到一种方法，可以为每个启用 git 的项目创建一个文件，然后将这个文件复制到外部硬盘上。

我遇到了 git 本身提供的两个选项:

*   `git archive`
*   `git bundle`

创建的只是项目在特定情况下的快照，不包含任何版本历史的东西，所以这个选项对我来说已经过时了。

给了我一个选项来生成一个包含所有分支的版本历史的文件。在我看来，这就是我一直在寻找的解决方案。

但是，可惜的是，`bundle`并没有存储你在本地回购中的任何变更数据。

经过大量的搜索，我找不到任何既能给我版本历史又能给我所有隐藏在一个文件中的变更的东西。所以我决定编写我自己的 python 脚本，它将创建包含由`git bundle`生成的包文件的 zip 文件，以及我的 repo 中所有隐藏更改的补丁文件。

我在这篇文章中描述了我是如何制作这个程序的，但是如果你只想要这个脚本，我在这篇文章的底部放了一个要点的链接。

## 项目分解

我决定把这个项目分成几个部分，然后一个一个地攻克它们。

*   获取需要生成 git-backup 的目录
*   检查该目录是否确实是启用 git 的项目的根目录
*   从 python 脚本中创建一个`git bundle`文件
*   检查项目是否有任何尚未提交或隐藏的变更
*   粉碎我在上面的步骤中发现的任何变化
*   检查项目是否有任何隐藏的变更
*   为上一步中发现的所有隐藏的更改创建单独的补丁文件
*   创建一个 zip 文件，其中包含来自 git 的包文件和我们在上一步中创建的所有补丁文件
*   删除包和补丁文件，因为我们已经将它们包含在 zip 文件中

## 第一步:获取目录

我决定给用户两个选项。他们可以:

1.  通过终端进入目录，从那里执行我的脚本
2.  将目录作为命令行参数提供给我的脚本

```
import sys
import os

arguments = sys.argv[1:]
if len(arguments) == 0:
    directory_to_backup = os.getcwd()
else:
    directory_to_backup = arguments[0]

directory_to_backup = os.path.abspath(directory_to_backup) 
```

在 python 中`sys.argv`返回传递给我们程序的命令行参数列表。因为列表中的第一项总是我们程序的名字，所以我们忽略它，看看列表的其余部分。

如果列表的其余部分为空，我们考虑第一个用例，即用户从他们想要备份的目录中启动了我们的程序。因此我们使用`os.getcwd()`，它返回系统的当前工作目录，这将是我们的脚本启动的目录。

之后，我们使用`os.path.abspath(directory_to_backup)`清理提供给我们的路径，并将其转换为系统使用的绝对路径。

## 步骤 2:检查这是否是一个 git 项目的根目录

列表的下一步是检查提供给我们的目录是否确实是 git 启动的项目的根目录。这一点非常重要，因为如果我们试图在一个实际上不是 git 项目的文件夹上执行它，脚本的其余部分就会崩溃。

我决定只检查提供给我们的根文件夹中是否存在一个`.git`文件夹。Git 将该项目的所有数据存储在该项目中的一个`.git`文件夹中。如果你们知道更好的方法来做这个检查，请告诉我。

```
git_directory = os.path.join(directory_to_backup, '.git')
if os.path.exists(git_directory) and os.path.isdir(git_directory):
    create_backup_zip(directory_to_backup)
else:
    print(directory_to_backup + ' is not a git repository') 
```

`os.path.exists`检查`git_directory`的存在，`os.path.isdir`检查它实际上是一个目录，而不仅仅是一个名为`.git`的文件。

## 步骤 3:在我的脚本中创建一个 git 包

在此之前，我只从终端创建过 git 包，从未在 python 脚本中创建过。我不知道该怎么做。

首先，我决定从我的脚本中发出终端命令，并捕获这些命令的输出。但后来我想，它可能无法跨平台运行(我看的是你的 Windows)。

然后我开始寻找 python 中的一个库来与 git 库交互。谷歌得出的第一个结果是 **GitPython** 。

我开始阅读它的文档，很快(不到 5 分钟)就被它抛给我的所有 git 相关概念淹没了。但是经过大约 10 分钟的搜索，我发现了一些有用的东西。我可以获得该 git 项目的`Repo`对象的句柄，然后使用该句柄执行我的 git 命令。

```
parent_directory, directory_name = os.path.split(directory_to_backup)
repository = Repo(directory_to_backup)
git_handle = repository.git
git_bundle_file_name = os.path.join(parent_directory, (directory_name + '.bundle'))
git_handle.bundle('create', git_bundle_file_name, '--all') 
```

这相当于:

```
git bundle create <git_bundle_name> --all 
```

`--all`标志包括所有分支，而不仅仅是当前分支。

## 步骤 4:检查任何未提交或隐藏的变更

接下来，我需要检查任何尚未提交和隐藏的更改，以便我们可以隐藏它们。

只需运行`git status`就能给我一个已更改文件的列表，但它还会输出其他内容，使之对人友好。有一个选项`--porcelain`去掉了所有额外的信息。

有一些文件没有被跟踪，当所有未提交的文件都是没有被跟踪的文件时，我们不想创建一个新的隐藏。因此，我们根据第一列中的值过滤这些行，并删除第一列值仅由`?`组成的任何行。我不知道第一列的值是什么意思，但我只知道`?`表示未被跟踪的文件。如果在删除所有未跟踪的文件后，我们的未转移文件列表不为空，那么我们执行下一步，否则我们跳过它。

## 步骤 5:隐藏工作树和暂存区中的任何变更

如果我们发现我们有任何未转移但不是未跟踪的文件，那么我们隐藏这些更改。这个命令很简单。

```
git_handle.stash('push', '-m', 'git-backup-stash') 
```

## 步骤 6:现在我们检查项目是否有任何隐藏的变更

现在我们检查项目是否有任何隐藏的变更。我们这样做是为了确定是否需要制作任何补丁文件。Git 有一个简单的命令来列出所有的仓库。

```
stashes = git_handle.stash('list').strip()
stash_list = stashes.split(os.linesep)

if (len(stashes) > 0) and (len(stash_list) > 0):
    # Create patch-files here 
```

## 第七步:为我们所有的库存创建补丁文件

现在，我们为本地存储库中的任何存储创建补丁文件。这些文件随后可以用于恢复隐藏的更改。每个 stash 条目都有自己的补丁文件。

补丁文件名的格式如下:

`<stash_name>:<branch_name>:<stash_message>.txt`

```
if (len(stashes) > 0) and (len(stash_list) > 0):
    patch_files_directory = os.path.join(parent_directory, 'patch-files')
    os.makedirs(patch_files_directory, exist_ok=True)
    for stash in stash_list:
        stash_name = stash.split(': ')[0]
        stash_branch_name = stash.split(': ')[1].split()[1]
        stash_message = ': '.join(stash.split(': ')[2:])

        patch_file_name = ':'.join([stash_name, stash_branch_name, stash_message]) + '.txt'
        patch_file_path = os.path.join(patch_files_directory, patch_file_name)
        patch_contents = git_handle.stash('show', '-p', stash_name)
        with open(patch_file_path, 'x') as f:
            f.write(patch_contents)
        patch_files.append(patch_file_path) 
```

## 第 8 步:创建一个包和所有补丁文件的 zip 文件

现在我们需要做的就是将上述所有文件整合到一个 zip 文件中。首先，我们将 git 创建的包文件写入 zip 文件。然后，我们将补丁文件(如果有的话)放入 zip 文件中名为“补丁文件”的子文件夹中。

```
zip_file_name = directory_name + '.zip'
zip_file_path = os.path.join(parent_directory, zip_file_name)
with ZipFile(zip_file_path, 'x') as z:
    z.write(git_bundle_file_name, arcname=os.path.split(git_bundle_file_name)[1])
    for patch_file in patch_files:
        z.write(patch_file, arcname=os.path.join('patch-files', os.path.split(patch_file)[1])) 
```

## 第九步:删除捆绑文件和补丁文件

别急，伙计，我们仍然需要删除 git-bundle 文件和我们创建的补丁文件。由于我们已经将它们包含在我们的 zip 文件中，我们不再需要它们，因此应该清理它们。

```
os.remove(git_bundle_file_name)
if len(patch_files) > 0:
    shutil.rmtree(patch_files_directory) 
```

## 全码

```
#!/usr/bin/python3 
import os
import sys
import re
import shutil
from git import Repo
from zipfile import ZipFile

def create_backup_zip(directory_to_backup):
    parent_directory, directory_name = os.path.split(directory_to_backup)
    repository = Repo(directory_to_backup)
    git_handle = repository.git
    git_bundle_file_name = os.path.join(parent_directory, (directory_name + '.bundle'))
    git_handle.bundle('create', git_bundle_file_name, '--all')

    pattern_for_untracked_files_flag = re.compile(r'^\?+$')
    non_staged_files_raw_output = git_handle.status('--porcelain')
    if len(non_staged_files_raw_output.strip()) > 0:
        non_staged_files = list(filter(
            lambda x: pattern_for_untracked_files_flag.match(x[0]) is None,
            list(map(lambda _: _.strip().split(), non_staged_files_raw_output.split(os.linesep)))
        ))
    else:
        non_staged_files = []

    if len(non_staged_files) > 0:
        git_handle.stash('push', '-m', 'git-backup-stash')

    stashes = git_handle.stash('list').strip()
    stash_list = stashes.split(os.linesep)
    patch_files = []
    if (len(stashes) > 0) and (len(stash_list) > 0):
        patch_files_directory = os.path.join(parent_directory, 'patch-files')
        os.makedirs(patch_files_directory, exist_ok=True)
        for stash in stash_list:
            stash_name = stash.split(': ')[0]
            stash_branch_name = stash.split(': ')[1].split()[1]
            stash_message = ': '.join(stash.split(': ')[2:])

            patch_file_name = ':'.join([stash_name, stash_branch_name, stash_message]) + '.txt'
            patch_file_path = os.path.join(patch_files_directory, patch_file_name)
            patch_contents = git_handle.stash('show', '-p', stash_name)
            with open(patch_file_path, 'x') as f:
                f.write(patch_contents)
            patch_files.append(patch_file_path)

    zip_file_name = directory_name + '.zip'
    zip_file_path = os.path.join(parent_directory, zip_file_name)
    with ZipFile(zip_file_path, 'x') as z:
        z.write(git_bundle_file_name, arcname=os.path.split(git_bundle_file_name)[1])
        for patch_file in patch_files:
            z.write(patch_file, arcname=os.path.join('patch-files', os.path.split(patch_file)[1]))

    os.remove(git_bundle_file_name)
    if len(patch_files) > 0:
        shutil.rmtree(patch_files_directory)
    print(zip_file_name + ' created')

if __name__ == " __main__":
    arguments = sys.argv[1:]
    if len(arguments) == 0:
        directory_to_backup = os.getcwd()
    else:
        directory_to_backup = arguments[0]

    directory_to_backup = os.path.abspath(directory_to_backup)

    git_directory = os.path.join(directory_to_backup, '.git')
    if os.path.exists(git_directory) and os.path.isdir(git_directory):
        create_backup_zip(directory_to_backup)
    else:
        print(directory_to_backup + ' is not a git repository') 
```

[GitHub gist:https://gist . GitHub . com/VarunBarad/c 291 e 98 DD 426 b 0 da 1322171290 D7 bbd 0](https://gist.github.com/VarunBarad/c291e98dd426b0da1322171290d7bbd0)

## 这就是所有的乡亲

这是我解决问题的方法，我需要备份我的项目，其中也包括我未完成的工作。如果你对这些或天空下的任何其他话题有任何其他建议，[联系我](https://varunbarad.com/contact)或发推特给我 [@varun_barad](https://twitter.com/varun_barad) 。