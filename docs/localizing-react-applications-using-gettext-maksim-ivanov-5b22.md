# 使用 Gettext -马克西姆伊万诺夫本地化 React 应用程序

> 原文：<https://dev.to/fikajs/localizing-react-applications-using-gettext-maksim-ivanov-5b22>

有很多解决方案可以本地化 React 应用程序。

其中大多数致力于提供基于简单 JSON 或 YAML 格式的翻译文件消费方式。

但那只是一面——消费。他们将创建、更新和删除这些翻译的任务留给了开发人员。

在 Mojang 中，我们创建了一个自动化流程来处理基于 Gettext 的翻译。

这个工具创建于 20 多年前，它的格式为翻译人员所熟知，我将展示我们如何利用它来翻译基于 React 的前端。

这是在 [FikaJS 会议](https://www.meetup.com/fikajs/)上录制的一段对话