# Subversion 中的可执行文件

> 原文：<https://dev.to/adzubla/executables-in-subversion-a5n>

添加“可执行位”:

```
svn propset svn:executable on <list of files> 
```

删除“可执行位”:

```
svn propdel svn:executable <list of files> 
```

之后，记得提交目录。