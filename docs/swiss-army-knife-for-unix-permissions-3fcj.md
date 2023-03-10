# Unix 权限的瑞士军刀

> 原文：<https://dev.to/ehmicky/swiss-army-knife-for-unix-permissions-3fcj>

[Unix 文件权限](https://en.wikipedia.org/wiki/File_system_permissions)可以采用多种形式:符号(`ug+rw`)、八进制(`660`)或字符列表(`drw-rw----`)。

`unix-permissions`([https://github.com/ehmicky/unix-permissions](https://github.com/ehmicky/unix-permissions))支持在任何 Node.js 或 CLI 命令中使用其中任何一个(而不是局限于一个)。

该库还可以对 Unix 权限执行操作，例如:

*   测试、设置和取消设置。否则，使用逐位运算(`|`、`&`、`^`、`~`)可能会很繁琐且容易出错。
*   验证语法。
*   正常化。例如`u+r,u+w`可以简称为`u+rw`。
*   反转。例如，`117`的 [`umask`](https://linux.die.net/man/2/umask) 意味着将使用`661`权限创建新文件。
*   检查它们列表中的最小或最大权限。这对于聚合几个文件的所有权限非常有用，例如在目录递归期间。

请随时向我提供关于 dev.to 或 GitHub 的反馈！