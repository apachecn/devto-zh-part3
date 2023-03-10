# Fosh:在按标签分组的多个目录中执行 shell 命令

> 原文：<https://dev.to/bimlas/fosh-execute-shell-commands-in-multiple-directories-grouped-by-tags-4jbp>

对于那些在不同目录下重复运行相同命令的开发者来说，Fosh 是一个高效的工具，它通过 T2 执行命令而不需要改变目录来节省时间。与其他类似工具不同，Fosh 不绑定某个软件(比如 Git)，它可以**执行任何 shell 命令**。它至少能在 Windows 和 Linux 上工作。

如果你喜欢迎头痛击，看看[https://github.com/bimlas/bash-mosh/](https://github.com/bimlas/bash-mosh/)T3

*   **一起管理多个 Git 定位器**
    *   签出项目及其子模块的同一分支
    *   使用相同的消息提交:不间断地遍历 repos，并复制/粘贴相同的提交消息
    *   防止早期推送:您不必记住哪些存储库已经修改，只需在一天结束时查看它们，以确定您需要将它们推送至何处
*   **同时控制多台流浪机器**

代码的精髓一言以蔽之:

```
for(dir in selectedDirectories) {
  cd(dir);
  shellCommand();
} 
```

这也意味着名字:`for ... shell ...`->-T1。

## 安装

```
$ npm install -g fosh 
```

获取源代码，报告错误，打开拉取请求，或者只是开始，因为你不知道你需要它:

*   [https://gitlab.com/bimlas/node-fosh](https://gitlab.com/bimlas/node-fosh)(官方知识库)
*   [https://github.com/bimlas/node-fosh](https://github.com/bimlas/node-fosh)(镜子，如果你喜欢的话，星星)

## 命令

### 标签:给目录分配标签

标签可以指定为前缀为`@`的命令行参数，目录应该在提示中列出，或者通过管道传输到 stdin。

```
$ fosh tag "@pictures" "@personal"
fosh: tag > /home/myself/photos
fosh: tag > /home/mom/my_little_family

$ echo -e "/home/myself/photos \n /home/mom/my_little_family" | fosh tag "@pictures" "@personal" 
```

#### 查找 Git 仓库

**用
“Git-repos”标签标记当前目录(`./`)下的**Git 仓库:

```
# Linux / Windows Git Bash / ...
$ find "./" -name ".git" -printf "%h\n" | fosh tag "@git-repos"

# Windows PowerShell
$ Get-ChildItem './' -Recurse -Directory -Hidden -Filter '.git' | ForEach-Object { Split-Path $_.FullName -Parent } | fosh tag '@git-repos' 
```

**在 Windows 上你可以使用万物搜索引擎的`es` [命令行界面](https://www.voidtools.com/support/everything/command_line_interface/)快速找到 Git 库**。为此，在 PowerShell 中使用以下命令(省略根目录的路径(`./src`)来查找任何位置):

```
$ es -regex '^\.git$' './src' | Split-Path -Parent | fosh tag '@git-repos' 
```

### List:打印标签列表文件的路径

您可以在文本编辑器(记事本、Vim 等)中编辑它。).

### 运行:执行多个目录下的命令

参数可以是标签和路径。

```
$ fosh run "@git-repos" "../wip-project"
fosh: run > git status --short --branch

__ @1 awesome-project (/home/me/src/) ________________________________________
## master
AM README.md
 M package.json

__ @2 git-test (/home/me/src/) _______________________________________________
## master...origin/master
 M README.adoc
 M encoding/cp1250-encoding-dos-eol.txt
 M encoding/dos-eol.txt

__ @3 wip-project (/home/me/helping-tom/) ____________________________________
## master...origin/master
 M example-code.js

==============================================================================
fosh: run > another command and so on ... 
```

#### 过滤目录列表

如果您想让**只在某些目录**中执行命令，您可以通过它们的索引来选择它们。

```
fosh: run > @1,3 git status --short --branch

__ @1 awesome-project (/home/me/src/) ________________________________________
## master
AM README.md
 M package.json

__ @3 wip-project (/home/me/helping-tom/) ____________________________________
## master...origin/master
 M example-code.js 
```

#### 在最近使用的目录中执行

**如果输出很长**并且你想在某些目录上执行额外的命令，这是很有用的。在这种情况下，打开一个新的终端窗口(这样您就可以在当前终端中查看结果)并不带参数地运行程序:当给出标记或目录参数时，目录列表总是被存储，但是如果您不带参数地运行它，它将在最后指定的目录上执行命令。

```
$ fosh run "@git-repos" "../wip-project"
fosh: run > git status --short --branch

__ @1 awesome-project (/home/me/src/) ________________________________________
## master
AM README.md
...

# Another terminal

$ fosh run
fosh: WARNING: Using most recently used directory list
fosh: run > @3 git diff

__ @3 wip-project (/home/me/helping-tom/) ____________________________________
 example-code.js | 1 +
 1 file changed, 1 insertion(+)

diff --git a/example-code.js b/example-code.js
index 12b5e40..733220f 100644
--- a/example-code.js
+++ b/example-code.js
... 
```