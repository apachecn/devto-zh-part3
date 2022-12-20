# 使用普通 Javascript 的俱乐部花名册应用程序

> 原文：<https://dev.to/desoga/club-roster-application-with-vanilla-javascript-12ci>

所以经过深思熟虑。我已经决定在这里发表我的第一篇技术文章[。这篇文章着重于构建一个基本的应用程序，任何 javascript 初学者都会觉得有趣和有帮助。在这个应用程序中，我们将利用 Bootstrap 4、本地存储，当然还有普通的 javascript。Bootstrap 4 和本地存储分别取代了 CSS 和后端语言。事不宜迟，让我简单解释一下我们将要构建的内容。](https://dev.to/)

## 关于申请

我们正在构建的内容包括使用一个表单来注册一个名为 **The Titans** 的特定体育俱乐部的球员名单。一旦球员的数据被输入到表格中，它就会显示在 bootstrap 4 表中。然后，该表中的数据被保存到浏览器的本地存储中。这确保了每当我们重新加载页面时，我们的数据不会消失或消失。这听起来好玩又容易吗？好吧，我们开始吧。

## 用 Bootstrap 4 创建用户界面

首先，我们将创建**导航条**。记住，我们使用的是 Bootstrap 4，要做到这一点，我们不需要使用任何 CSS。**导航条**将包含俱乐部的标志。