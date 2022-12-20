# 用 Flask、Postman 和 PyTest 构建 Restful API 第 1 部分(阅读时间:6 分钟)

> 原文：<https://dev.to/steelwolf180/building-restful-api-with-flask-postman--pytest---part-1read-time-6-mins-15b4>

[![Photo by Christopher Burns on Unsplash](img/162e718bfe06a7e78c399b65712f0c3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R8fFKB25--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ox6hxide0m857l3crfdo.jpg)

## 简介

这是用 Flask、Postman 和 PyTest 构建 Restful API 的 3 部分系列的第一部分。

我将基于用 Pytest 测试 Python 应用程序的[来解释用于创建**费用管理器**项目的库和工具，以便您理解为什么您可能会在 Flask 中使用这些工具或库作为开发过程的一部分来开发 API。](https://semaphoreci.com/community/tutorials/testing-python-applications-with-pytest)

而在本系列的第 2 部分，我将介绍费用管理器的 **API 设计**和**邮递员**中的**模拟** API 端点。

最后，对于第 3 部分，我们将在使用 **PyTest** 构建的**测试用例**的**烧瓶**中实现 API。

## 从 Django 开发者到 API 开发者的旅程

当我刚开始成为一名 Django T1 的开发者时，我学习了如何用 T2 Django T3、T4 SQL T5 数据库和 JQuery T7 建立一个基本网站的基础知识。

随着我经验的增长，我意识到使用 Django 作为后端的前端框架来构建单页面 web 应用程序的重要性。

在工作中，我学会了将我对 Udacity 前端框架的理解与 **Django Rest 框架**结合起来，为前端开发 API，供我的后端使用。

## 使用 Flask 构建 API

Flask 是另一个基于 python 的 web 框架，被认为是开发 Restful APIs 的 **go to** 框架。

我在 **freeCodeCamp** 中看到了多篇文章或源代码，因为它的灵活性&创建 API 不需要太多努力。

根据使用 Django Rest 框架的经验，当我第一次开始使用它时，我非常怀疑它。

随着时间的推移，flask 说服了我，但由于 flask 的灵活性，学习起来可能会很痛苦。

由于与 **Django Rest 框架**相比，在构建 API 时没有**一种特定的方式**。

## 嘲讽&测试利用邮递员

在构建 API 时，我开始使用一些工具，这些工具可以让我以更快的速度测试我的 API。

其中一个工具是 Postman，我最初用它作为我的前端开发人员的测试工具和 API 文档。

但是我后来发现，你可以通过使用 Postman 的模拟服务器特性来打破前端和后端的**相互依赖**问题。

## 打破前端&后端的 API 依赖工作流。

使用 Postman 的模拟服务器，前端开发人员可以专注于集成模拟端点，而不依赖于后端。

而对于后端开发人员，他们可以专注于为前端提供一个工作端点，并在它完成时交换它。进一步减少开发团队的发货时间。

## 用 PyTest 创建测试用例

PyTest 是我在 python 中的 goto 测试框架，因为它易于学习，并且与默认安装的 **unittest** 库相比，样板代码较少。

它遵循了 **JUnit** 的实现风格，这在您第一次开始使用时可能会有所暗示。

## 结论

我希望本系列的第一部分解释了使用 Postman 和 Pytest 等工具或库有助于加速开发过程的基本原理。

请继续关注本系列的第 2 部分和第 3 部分，它们将教您如何创建模拟端点，并使用 Pytest 中的测试用例在 Flask 中实现它。

## 链接

*   [烧瓶](http://flask.pocoo.org/)
*   [邮递员](https://www.getpostman.com/)
*   [PyTest](https://docs.pytest.org/en/latest/)
*   [用 pytest 测试 Python:简单、快速、有效、可扩展](https://www.amazon.com/Python-Testing-pytest-Effective-Scalable/dp/1680502409)
*   [使用 PyTest 测试 Python 应用](https://semaphoreci.com/community/tutorials/testing-python-applications-with-pytest)
*   [Pytest 的清洁手](https://blog.daftcode.pl/the-cleaning-hand-of-pytest-28f434f4b684)

如果你喜欢我的文章，要么注册 [Max 的每周简讯](https://mailchi.mp/a1fd1bd1de4a/devto)或者你可以**关注**来获取我在 **Dev** 上的文章的最新更新

这篇文章最初发表在 max 的博客上，在 [Building Restful API with Flask，Postman & PyTest - Part 1](https://www.maxongzb.com/building-restful-api-with-flask-postman-and-pytest-part-1-read-time-6-mins/) ，图片来自[照片由 Christopher Burns 在 Unsplash](https://unsplash.com/photos/8KfCR12oeUM) 上拍摄