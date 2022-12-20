# [修复]致命:此操作必须在工作树中运行

> 原文：<https://dev.to/mu/fixed-fatal-this-operation-must-be-run-in-a-work-tree-2de4>

此错误可能是由于将 git 错误配置为`bare`而导致的。因此，下面的命令将删除/重置裸设置

```
git config --unset core.bare 
```

Enter fullscreen mode Exit fullscreen mode