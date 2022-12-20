# 将注册表连接到 Firebase -第 1 部分

> 原文：<https://dev.to/desoga/connect-registration-form-to-firebase-part-1-51b5>

在本教程中，我将向您展示如何将您的注册表单连接到 Firebase。Firebase 是一个移动和 web 开发平台，为程序员提供各种工具，帮助他们开发高质量的应用程序。

出于本教程的目的，我们将利用 Firebase 实时数据库来存储注册表单中的数据。在这里，数据存储为 JSON，并实时同步到每个连接的客户端。

### 教程大纲:

*   为注册表单创建 Html 模板。

*   为设计和响应添加 css

*   将表单连接到 Javascript

*   将 Firebase 实时数据库连接到注册表

## 为注册表单创建 Html 模板

我们不会使用任何 html 框架，比如 Bootstrap，也不会使用任何形式的 boiler 模板。我们的注册表单将从零开始构建，它也将具有响应性。