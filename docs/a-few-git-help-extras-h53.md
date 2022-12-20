# 几个饭桶帮助群众演员

> 原文：<https://dev.to/cookrdan/a-few-git-help-extras-h53>

如果您需要快速参考，命令行上的`git help`非常有用。但是如果你曾经尝试过使用它，有很多 T2 的信息。

### 使用`-h`选项进行快速、简单的参考

尝试在命令上附加`-h`选项，以打印出简化的参考。像这样:

> `git checkout -h`

### 自行使用`git help`

在命令行中键入`git help`,您会得到一个带有快速描述的常用 Git 命令列表。

### 使用`git help -g`进行引导

有些人可能不知道这些指南存在于`git help` :

```
〉 git help -g

The common Git guides are:
   attributes          Defining attributes per path
   cli                 Git command-line interface and conventions
   core-tutorial       A Git core tutorial for developers
   cvs-migration       Git for CVS users
   diffcore            Tweaking diff output
   everyday            A useful minimum set of commands for Everyday Git
   glossary            A Git Glossary
   hooks               Hooks used by Git
   ignore              Specifies intentionally untracked files to ignore
   modules             Defining submodule properties
   namespaces          Git namespaces
   repository-layout   Git Repository Layout
   revisions           Specifying revisions and ranges for Git
   tutorial            A tutorial introduction to Git
   tutorial-2          A tutorial introduction to Git: part two
   workflows           An overview of recommended workflows with Git

'git help -a' and 'git help -g' list available subcommands and some
concept guides. See 'git help <command>' or 'git help <concept>'
to read about a specific subcommand or concept. 
```

例如，尝试:

> `git help glossary`

祝您愉快！