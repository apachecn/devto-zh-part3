# 向 Node.js 显示一些🖤进程错误

> 原文：<https://dev.to/ehmicky/show-some-to-node-js-process-errors-5fdl>

Node.js 在控制台上打印进程错误(`uncaughtException`、`warning`、`unhandledRejection`、`rejectionHandled`)，非常有用。不幸的是，这些错误:

*   不要显示`warning`和`rejectionHandled`的堆栈跟踪，使它们难以调试。
*   不要包括`multipleResolves`错误(当一个承诺被解决/拒绝两次时)。
*   不方便登录外部服务。
*   很难测试。
*   不能有条件地跳过。
*   每次重复错误时打印(除了`warning`)。
*   对人类并不友好。

我创建了一个库来修复所有这些问题: [`log-process-errors`](https://github.com/ehmicky/log-process-errors) 。

欢迎到 https://github.com/ehmicky/log-process-errors 参观。欢迎任何反馈！