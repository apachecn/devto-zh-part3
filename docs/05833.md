# 将注册表连接到 Firebase -第 2 部分

> 原文：<https://dev.to/desoga/connect-registration-form-to-firebase-part-2-53cb>

在本教程的第一部分[中，我们只处理了注册表单的用户界面。在这一部分，我们将通过以下方式完成本教程:](https://dev.to/desoga/connect-registration-form-to-firebase-part-1-51b5)

*   用 Javascript 处理表单提交

*   将 Firebase 实时数据库连接到注册表

## 用 Javascript 处理表单提交

为此，我们将侦听提交事件，然后获取文档对象模型(DOM)的值。这是通过 javascript 事件监听器完成的。